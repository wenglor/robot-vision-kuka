# Example KUKA KRL program files for the generic vision interface

**Version:** 2.1.0

This repository demonstrates how to use the Generic Vision Interface with wenglor Machine Vision Devices on a KUKA controller. The included `.src`, `.dat`, and `.xml` files form a working sample program that you can adapt and customize for your application.

The example is provided once per KUKA system software, in the [sources](sources) directory: the folder [`KSS`](sources/KSS) for KUKA System Software KSS, and the folder [`iiQKA.OS2`](sources/iiQKA.OS2) for iiQKA.OS2. **Use only the set that matches your controller.** Both sets contain the same four components — `wenglorUserConfig`, `wenglorGlobal`, `wenglorMain` (each `.src` and `.dat`) and the EthernetKRL channel configuration `wenglorVision.xml`. In the `iiQKA.OS2` folder, every file carries the prefix `iiQKA_` — for example `iiQKA_wenglorUserConfig.src`.

> NOTE
>
> This repository focuses exclusively on KUKA Robots-specific topics. For general robot vision information, please refer to the [wenglor robot vision manual](https://wenglor.github.io/robot-vision-generic-string/).

📖 **Full documentation** is available in the [online manual](https://wenglor.github.io/robot-vision-kuka/)

---

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
  - [KSS](#kss)
  - [iiQKA.OS2](#iiqkaos2)
- [Running the Sample Program](#running-the-sample-program)
- [Configuration](#configuration)
  - [Network Setup (`wenglorVision.xml`)](#network-setup-wenglorvisionxml)
  - [Adjusting Parameters (`wenglorUserConfig.src`)](#adjusting-parameters-wengloruserconfigsrc)
  - [Teaching Poses and Defining Movements (`wenglorUserConfig.src`)](#teaching-poses-and-defining-movements-wengloruserconfigsrc)
- [Troubleshooting](#troubleshooting)
  - [Communication Errors](#communication-errors)
  - [Calibration failed](#calibration-failed)
- [Support & Feedback](#support--feedback)

---

## Prerequisites

- Basic knowledge of **KRL** (KUKA Robot Language)
- KUKA Robot Controller (KRC) with KRL support
- **EthernetKRL** (EKI) support — on iiQKA.OS2 this is the **iiQKA.EthernetKRL** option package
- [B60](https://www.wenglor.com/B60) (firmware >= 1.3) or [Machine Vision Controller (MVC)](https://www.wenglor.com/MachineVisionController) (firmware >= 1.0)
- A [uniVision](https://www.wenglor.com/uniVision3) job for calibration and object detection

Requirements per system software:

| System software | Controller | Tested software | Additional requirements |
|-----------------|------------|-----------------|-------------------------|
| KSS | KRC4, KRC5 | KSS 8.3.33 with EthernetKRL 3.2.4 | — |
| iiQKA.OS2 | KR C5-2, KR C5 micro-2 | iiQKA.OS2 KSS9.2 or higher with iiQKA.EthernetKRL V6.1.2 | PC software iiQWorks.Cockpit 1.3 and iiQWorks.Sim 1.2 |

> NOTE
>
> The KSS reference setup was tested on a KRC4 with a KR 6 R1820 Arc robot arm, and additionally on KRC5 controllers.

---

## Installation

Pick the file set that matches your controller and follow the corresponding section below.

### KSS

1. Get the files from the [`KSS`](sources/KSS) folder.
2. In [wenglorVision.xml](sources/KSS/wenglorVision.xml), adjust the IP address of the Machine Vision Device (by default `192.168.100.1`) and the port of the robot server (by default `6008`).
3. Copy the files to the robot controller.

   | Sources                                        | Destination                                      |
   |------------------------------------------------|--------------------------------------------------|
   | [wenglorVision.xml](sources/KSS/wenglorVision.xml) | `C:\KRC\ROBOTER\Config\User\Common\EthernetKRL\` |
   | .src and .dat files                            | `KRC:\R1\Program\`                               |

4. Follow the [configuration](#configuration) steps.
5. Run the program [wenglorMain.src](sources/KSS/wenglorMain.src) on your robot.

### iiQKA.OS2

On iiQKA.OS2 the files are not copied to the controller directly — use the KUKA PC software iiQWorks to transfer them.

1. Get the files from the [`iiQKA.OS2`](sources/iiQKA.OS2) folder.
2. In iiQWorks.Cockpit, open the `Software Repository`, select **iiQKA.EthernetKRL** version `6.1.2` and download it. Then, in iiQWorks.Sim, select the robot in the `Devices` tree and add the package to the controller from `Option packages configuration` → `Available options`.
3. Go to `Option packages` → `iiQKA.EthernetKRL (V6.1.2)` → `Ethernet configuration` and import [iiQKA_wenglorVision.xml](sources/iiQKA.OS2/iiQKA_wenglorVision.xml). Select the imported `iiQKA_wenglorVision` channel and, in its `<Client>` element, adjust the IP address of the Machine Vision Device and the port of the robot server.
4. Go to `PROGRAM`, right-click the `Program` folder, choose `Import` → `Import KRL directory` and select the `iiQKA.OS2` folder.
5. Transfer the configuration and the robot programs to the robot with `CONFIGURATION` → `Deploy Configuration onto Controller`.
6. Follow the [configuration](#configuration) steps.
7. Select and run `iiQKA_wenglorMain` on the robot panel.

> NOTE
>
> The [Installation & Setup](https://wenglor.github.io/robot-vision-kuka/1_0_0_installation/) page of the online manual walks through both procedures step by step, with screenshots.

---

## Running the Sample Program

1. Connect to the robot controller.
2. On the KUKA teach panel, load the main module — `wenglorMain.src` on KSS, `iiQKA_wenglorMain` on iiQKA.OS2.
3. Start execution and follow the console logs.

---

## Configuration

> NOTE
>
> The parameters and the poses to teach are identical for both system software variants. Only the file and channel names differ: the KSS set uses `wenglorUserConfig.src` with the channel `wenglorVision`, the iiQKA.OS2 set uses `iiQKA_wenglorUserConfig.src` with the channel `iiQKA_wenglorVision`. The file names below are the KSS ones.

### Network Setup (`wenglorVision.xml`)

```xml
<EXTERNAL>
    <IP>192.168.100.1</IP> <!-- IP address of the Machine Vision Device -->
    <PORT>6008</PORT>      <!-- Port of the robot vision server on the Machine Vision Device -->
    <TYPE>Server</TYPE>
</EXTERNAL>
```

On iiQKA.OS2, these values are not edited in the file directly — set them in the `<Client>` element of the `iiQKA_wenglorVision` channel in the iiQWorks Ethernet configuration, as described under [iiQKA.OS2](#iiqkaos2).

### Adjusting Parameters (`wenglorUserConfig.src`)

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

If you taught more than five poses, remember to update the number of calibration poses.

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

---

## Troubleshooting

### Communication Errors

- Verify IP/port in [wenglorVision.xml](sources/KSS/wenglorVision.xml) (KSS) or in the `iiQKA_wenglorVision` channel configuration (iiQKA.OS2)
- Ensure the robot server on the Machine Vision Device is active
  - Go to the device website → Jobs → Processing Instance → Robot Server
- Check network connectivity/firewall

### Calibration failed

- Ensure your taught number of calibration poses equals *W_NUM_CALIBRATION_POSES*
- Match *W_CONNECTION[]* with the XML filename

---

## Support & Feedback

- **Bugs:** Please open a new Issue in the [GitHub Issues section](../../issues) if needed.
- **Feature Requests & Ideas:** Discuss suggestions in the Discussions → Ideas category under [GitHub Discussions](../../discussions).
