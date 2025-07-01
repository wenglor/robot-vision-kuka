# Example KUKA KRL program files for the generic vision interface

**Version:** 2.0.0

> **Note** This repository contains example configuration and KRL program files to set up and start the generic vision interface to wenglor vision devices on your KUKA robot.

---
##  Contents
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
  - [1. Edit *wenglorVision.xml*](#1-edit-network-setup-in-wenglorvisionxml)
  - [2. Edit *wenglorUserConfig.src*](#2-edit-parameter-in-wengloruserconfigsrc)
  - [3. Teach Robot Poses](#3-teach-poses-and-add-movement-commands)
- [Troubleshooting](#troubleshooting)
- [Required Files](#required-files)
---

## Prerequisites
- Basic KRL knowledge
- EthernetKRL support
- KUKA Robot Controller (KRC) with KRL support
- [B60](https://www.wenglor.com/de/Machine-Vision/Smart-Cameras-und-Vision-Sensoren/Smart-Camera-B60/c/cxmCID221375) with Firmware version 1.3 or newer
- [Machine Vision Controller - MVC](https://www.wenglor.com/de/Machine-Vision/Machine-Vision-Controller/c/cxmCID221381) with Firmware Version 1.0 or newer
- A valid [univision](https://www.wenglor.com/de/Machine-Vision/Machine-Vision-Software/Bildverarbeitungssoftware-uniVision-3/c/cxmCID222459) job for the calibration and the detection
---

## Quick Start
1. Download the [required files](#required-files)
2. Copy them to your robot controller.

   | Sources                                | Destination                                      |
   |----------------------------------------|--------------------------------------------------|
   | [wenglorVision.xml](wenglorVision.xml) | `C:\KRC\ROBOTER\Config\User\Common\EthernetKRL\` |
   | *.src and *.dat files                  | `C:\KRC\R1\Program\`                             |

3. Follow the [configuration](#configuration) steps.
4. Run the program [wenglorMain.src](wenglorMain.src) on your robot.
---

## Configuration
### 1. Edit network setup in wenglorVision.xml
```xml
<EXTERNAL>
    <IP>192.168.100.1</IP> <!-- IP address of the vision device -->
    <PORT>6008</PORT>      <!-- Port of the robot vision server on the vision device -->
    <TYPE>Server</TYPE>
</EXTERNAL>
```
### 2. Edit Parameter in wenglorUserConfig.src
<details>
   <summary>Click to see the relevant parameter adjustments in the wenglorUserConfig.src file </summary>

```src
;----------------------------------------
   ; Name of the xml file
   g_connection_name[]="wenglorVision"
   ;----------------------------------------
   ;----------------------------------------
   ; Comment out the correct line depending
   ; on the selected camera/robot setup
   g_use_case[]="camera_not_on_robot"
   ;g_use_case[]="camera_on_robot"              <!-- If your camera is mounted on the robot, pick this case. -->
   ;----------------------------------------
   ;----------------------------------------
   ; Comment out the correct line depending
   ; on the selected calibration board
   ;g_calib_target[]="zvzj001"
   ;g_calib_target[]="zvzj002"
   g_calib_target[]="zvzj003"                   <!-- Pick the calibration board you are using. -->
   ;g_calib_target[]="zvzj004"
   ;----------------------------------------
   ;----------------------------------------
   ; Adjust the number of calibration poses
   ; you want to use. 5 poses are required
   ; at least.
   g_num_calibration_poses = 5                  <!-- How many calibration poses do you want to use? Minimum is 5. -->
   ;----------------------------------------
   ;----------------------------------------
   ; Comment out the correct line depending
   ; on the selected detection
   g_detection_case[]="single"
   ;g_detection_case[]="multi"                  <!-- Want to detect multiple objects with one capture? Then pick this one. -->
   ;----------------------------------------

   GLOBAL DEF call_calibration_job()
      ; change job name here
      send_simple_command("job:change[calibration.u3p];")      <!-- Update to your calibration job name. -->
   END

   GLOBAL DEF call_detection_job()
      ; change job name here
      send_simple_command("job:change[find_objects.u3p];")     <!-- Update to your detection job name. -->
   END
```
</details>

### 3. Teach poses and add movement commands
>If you taught more than 5 poses remember to update the number of calibration poses

<details>
   <summary>Click to see where to set the poses in the wenglorUserConfig.src file </summary>

```src
GLOBAL DEF calibration_pose(pose_num:IN)
   INT pose_num
   SWITCH pose_num
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
      ;------------------------------------
      ; Add more poses by copy "CASE #" and
      ; increase the pose number e.g.: CASE 6
      ; Make sure g_num_calibration_poses
      ; above has the right value.
      ;
                                                   <!-- Teach further poses here to increase accuracy. -->
      DEFAULT
         ; no movement
   ENDSWITCH

END

GLOBAL DEF detection_pose()
   ; Add movement to detection_pose here.
   ;
                                                   <!-- Teach detection pose here. -->
END

GLOBAL DEF safety_pose()
   ; Add movement to pose here.
   ; use case camera not on robot
   ; for removing calibration board
   ;
                                                   <!-- Teach safety pose here. -->
END

GLOBAL DEF validation_movement()
   DECL FRAME p_valid
   DECL INT safety_z_offset_mm

   ; Edit safety offset here
   safety_z_offset_mm = 5                          <!-- Update safety offset for validation movement. -->

   detection_pose()
   WAIT SEC 1
   p_valid = get_validation_pose(safety_z_offset_mm)
   ; Add movement to pose here. Make sure
   ; that the assignment to the pose is correct.
   ; Unfold movement section below to ensure
   ; the movement target is exactly named as
   ; the p_valid variable above.
   ;
                                                   <!-- Add movement to validation pose with name p_valid here. -->
END

GLOBAL DEF object_movement(p_obj:IN)
   DECL FRAME p_obj
   ; Add movement to pose here. Make sure
   ; that the assignment to the pose is correct.
   ; Unfold movement section below to ensure
   ; the movement target is exactly named as
   ; the p_obj variable above.
   ;
                                                   <!-- Add movement to object pose with name p_obj here. -->
END

GLOBAL DEF test_calibration_poses()                <!-- Add to wenglorMain.src to test your calibration pose movements -->
   INT i
   FOR i=1 to g_num_calibration_poses
      calibration_pose(i)
   ENDFOR
END
```
</details>

## Troubleshooting
### Communication errors
  - Verify IP/port in [wenglorVision.xml](wenglorVision.xml)
  - Check network connectivity/firewall
### Calibration failed
  - Ensure your number of calibration poses set equals *g_num_calibration_poses*
  - Match *g_connection_name* with XML filename
---

## Required Files
* [wenglorGlobal.dat](wenglorGlobal.dat)
* [wenglorGlobal.src](wenglorGlobal.src)
* [wenglorMain.src](wenglorMain.src)
* [wenglorUserConfig.dat](wenglorUserConfig.dat)
* [wenglorUserConfig.src](wenglorUserConfig.src)
* [wenglorVision.xml](wenglorVision.xml)


##