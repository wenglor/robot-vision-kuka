# Example KUKA KRL program files for the generic vision interface

**Version:** 2.1.0

This repository demonstrates how to use the Generic Vision Interface with wenglor vision devices on a KUKA controller. The included `.src`, `.dat`, and `.xml` files form a working sample program [wenglorMain.src](sources/wenglorMain.src) that you can adopt and customize for your application.

> NOTE
>
> This repository focuses exclusively on KUKA Robots-specific topics. For general robot vision information, please refer to the [wenglor robot vision manual](https://wenglor.github.io/robot-vision-generic-string/).

📖 **Full documentation** is available in the [online manual](https://wenglor.github.io/robot-vision-kuka/)

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Installation](#installation)
3. [Running the Sample Program](#running-the-sample-program)
4. [Configuration](#configuration)
   1. [Network Setup (`wenglorVision.xml`)](#network-setup-wenglorvisionxml)
   2. [Adjusting Parameter (`wenglorUserConfig.src`)](#adjusting-parameter-wengloruserconfigsrc)
   3. [Teaching Poses and Defining Movements (`wenglorUserConfig.src`)](#teaching-poses-and-defining-movements-wengloruserconfigsrc)
5. [Troubleshooting](#troubleshooting)
   1. [Communication errors](#communication-errors)
   2. [Calibration failed](#calibration-failed)
6. [Support & Feedback](#support--feedback)

---

## Prerequisites

- Basic knowledge of **KRL** (KUKA Robot Language)
- **EthernetKRL** support
- KUKA Robot Controller (KRC) with KRL support
- [B60](https://www.wenglor.com/en/Machine-Vision/Smart-Cameras-and-Vision-Sensors/Smart-Camera-B60/c/cxmCID221375) (firmware >= 1.3) or [Machine Vision Controller (MVC)](https://www.wenglor.com/en/Machine-Vision/Machine-Vision-Controllers/c/cxmCID221381) (firmware >= 1.0)
- A [uniVision](https://www.wenglor.com/en/Machine-Vision/Machine-Vision-Software/Image-Processing-Software-uniVision-3/c/cxmCID222459) job for calibration and object detection

---

## Installation

1. Get the files from the [sources](sources) directory.
2. Copy them to the robot controller.

   | Sources                                        | Destination                                      |
   |------------------------------------------------|--------------------------------------------------|
   | [wenglorVision.xml](sources/wenglorVision.xml) | `C:\KRC\ROBOTER\Config\User\Common\EthernetKRL\` |
   | .src and .dat files                            | `KRC:\R1\Program\`                               |

3. Follow the [configuration](#configuration) steps.
4. Run the program [wenglorMain.src](sources/wenglorMain.src) on your robot.

---

## Running the Sample Program

1. Connect to the robot controller.
2. On the KUKA teach panel, load the wenglorMain.src program.
3. Start execution and follow the console logs.

---

## Configuration

### Network Setup (`wenglorVision.xml`)

```xml
<EXTERNAL>
    <IP>192.168.100.1</IP> <!-- IP address of the vision device -->
    <PORT>6008</PORT>      <!-- Port of the robot vision server on the vision device -->
    <TYPE>Server</TYPE>
</EXTERNAL>
```

### Adjusting Parameter (`wenglorUserConfig.src`)

<details>
   <summary>Click to see the relevant parameter adjustments in the wenglorUserConfig.src file </summary>

```src
   ;----------------------------------------
   ; Name of the xml file
   W_CONNECTION[] = "wenglorVision"
   ;----------------------------------------
   ; Comment out the correct line depending
   ; on the selected camera/robot setup
   W_USE_CASE[] = "camera_not_on_robot"
   ;W_USE_CASE[] = "camera_on_robot"              <!-- If your camera is mounted on the robot, pick this case. -->
   ;----------------------------------------
   ; Comment out the correct line depending
   ; on the selected calibration board
   W_CALIBRATION_TARGET[] = "zvzj001"             <!-- Pick the calibration board you are using. -->
   ;W_CALIBRATION_TARGET[] = "zvzj002"
   ;W_CALIBRATION_TARGET[] = "zvzj003"
   ;W_CALIBRATION_TARGET[] = "zvzj004"
   ;----------------------------------------
   ; Define the uniVision jobs
   W_CALIBRATION_JOB[] = "calibration.u3p"        <!-- Update to your calibration job name. -->
   W_DETECT_OBJECTS_JOB[] = "find_objects.u3p"    <!-- Update to your detection job name. -->
   W_DETECT_TARGET_JOB[] = "find_target.u3p"
   ;----------------------------------------
   ; Adjust validation z safety offset [mm]
   W_SAFETY_OFFSET_MM = 10
   ;----------------------------------------
   ; Adjust the number of calibration poses
   ; you want to use. 5 poses are required
   ; at least.
   W_NUM_CALIBRATION_POSES = 5                    <!-- How many calibration poses do you want to use? Minimum is 5. -->
   ;----------------------------------------
   ; Comment out the correct line depending
   ; on the selected detection
   W_USER_COMMAND[] = "singleDetection"
   ;W_USER_COMMAND[] = "multiDetection"           <!-- Want to detect multiple objects with one capture? Then pick this one. -->
   ;W_USER_COMMAND[] = "updateReferenceFrame"
```

</details>

### Teaching Poses and Defining Movements (`wenglorUserConfig.src`)

If you taught more than 5 poses remember to update the number of calibration poses

<details>
   <summary>Click to see where to set the poses in the wenglorUserConfig.src file </summary>

```src
GLOBAL DEF moveTocalibrationPose(poseNum:IN)
   INT poseNum
   SWITCH poseNum
      CASE 1
         ; Add movement to calibration pose 1 here.
         ;
                                                   <!-- Teach pose here. -->
      CASE 2
         ; Add movement to calibration pose 2 here.
         ;
                                                   <!-- Teach pose here. -->
      CASE 3
         ; Add movement to calibration pose 3 here.
         ;
                                                   <!-- Teach pose here. -->
      CASE 4
         ; Add movement to calibration pose 4 here.
         ;
                                                   <!-- Teach pose here. -->
      CASE 5
         ; Add movement to calibration pose 5 here.
         ;
                                                   <!-- Teach pose here. -->
         ;------------------------------------
         ; Add more poses by copy "CASE #" and
         ; increase the pose number e.g.: CASE 6
         ; Make sure W_NUM_CALIBRATION_POSES
         ; above has the right value.
         ;
                                                   <!-- Teach further poses here to increase accuracy. -->
      DEFAULT
         ; no movement
   ENDSWITCH

END

GLOBAL DEF moveToDetectObjectsPose()
   ; Add movement to detectObjectsPose here.
   ;
                                                   <!-- Teach detection pose here. -->
END

GLOBAL DEF moveToSafetyPose()
   ; Add movement to pose here.
   ; use case camera not on robot
   ; for removing calibration board
   ;
                                                   <!-- Teach safety pose here. -->
END

GLOBAL DEF moveToDetectTargetPose()
   ; Add movement to targetPose here.
   ;
                                                   <!-- Teach detect target pose here (updateReferenceFrame). -->
END

GLOBAL DEF testCalibrationPoses()                  <!-- Call from wenglorMain.src to test your calibration pose movements -->
   INT i
   FOR i=1 to W_NUM_CALIBRATION_POSES
      moveTocalibrationPose(i)
   ENDFOR
END
```

</details>

## Troubleshooting

### Communication Errors

- Verify IP/port in [wenglorVision.xml](sources/wenglorVision.xml)
- Ensure the robot server on the vision device is active
  - Go to the device website->Jobs->Processing Instance->Robot Server
- Check network connectivity/firewall

### Calibration failed

- Ensure your taught number of calibration poses equals *W_NUM_CALIBRATION_POSES*
- Match *W_CONNECTION[]* with the XML filename

---

## Support & Feedback

- **Bugs:** Please open a new Issue in the [GitHub Issues section](../../issues) if needed
- **Feature Requests & Ideas:** Discuss suggestions in the Discussions → Ideas category under [GitHub Discussions](../../discussions)
