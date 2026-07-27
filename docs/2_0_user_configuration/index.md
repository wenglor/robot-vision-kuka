# User Configuration

All parameters you need to adapt to your setup are located in the `wenglorUserConfig` module. Adjust them according to your needs before running the program.

<img src="images/01_user_config_module.png" alt="wenglorUserConfig.src parameters" class="big"/>

## Parameters

Edit the following parameters in `wenglorUserConfig.src`:

| Parameter | Description |
| --- | --- |
| `W_CONNECTION[]` | Name of the EthernetKRL channel. Must match the name of the `wenglorVision.xml` file. By default it fits automatically — **do not rename it**. |
| `W_USE_CASE[]` | Defines whether the camera is on the robot or not (`camera_on_robot` or `camera_not_on_robot`). |
| `W_CALIBRATION_TARGET[]` | ID of the calibration plate: `zvzj001`, `zvzj002`, `zvzj003`, or `zvzj004`. Select `zvzj001` if using ZVZJ005 and `zvzj002` if using ZVZJ006 (same size). |
| `W_CALIBRATION_JOB[]` | Name of the uniVision job for calibration. |
| `W_DETECT_OBJECTS_JOB[]` | Name of the uniVision job for detection. |
| `W_DETECT_TARGET_JOB[]` | Name of the uniVision job for detecting the calibration plate (used by `updateReferenceFrame`). |
| `W_SAFETY_OFFSET_MM` | Z offset in millimeters for the validation of the calibration. |
| `W_NUM_CALIBRATION_POSES` | Number of calibration poses to use. At least **5** poses are required, and the value must match the number of poses taught in `moveTocalibrationPose()`. |
| `W_USER_COMMAND[]` | The routine to execute (`singleDetection`, `multiDetection`, or `updateReferenceFrame`). |
| `W_BASE_NUM` | Base number used for the reference frame in the `updateReferenceFrame` use case. |
| `W_BASE_NAME[]` | Name of the reference frame base (`wReferenceFrame` by default). |
| `W_MACHINE_POSES_TAUGHT` | Set to `TRUE` after teaching the poses relative to the reference frame in the `updateReferenceFrame` use case. See [Robot Program → `updateReferenceFrame`](../3_0_robot_program/index.md#updatereferenceframe). |

## Mobile platform use case

For the mobile platform use case (`updateReferenceFrame`), also set:

- `W_BASE_NUM` and `W_BASE_NAME[]` — the base that is updated to compensate the positional drift of the platform.
- `W_MACHINE_POSES_TAUGHT` — set to `TRUE` after teaching the poses relative to the reference frame.

For the general concept, see [Calibration Guidelines](https://wenglor.github.io/robot-vision-generic-string/4_0_robot_vision_server/4_1_calibration_guidelines/) in the wenglor robot vision manual.

## Example

```text
;----------------------------------------
; Name of the xml file
W_CONNECTION[] = "wenglorVision"
;----------------------------------------
; Comment out the correct line depending
; on the selected camera/robot setup
W_USE_CASE[] = "camera_not_on_robot"
;W_USE_CASE[] = "camera_on_robot"
;----------------------------------------
; Comment out the correct line depending
; on the selected calibration board
W_CALIBRATION_TARGET[] = "zvzj001"
;W_CALIBRATION_TARGET[] = "zvzj002"
;W_CALIBRATION_TARGET[] = "zvzj003"
;W_CALIBRATION_TARGET[] = "zvzj004"
;----------------------------------------
; Define the uniVision jobs
W_CALIBRATION_JOB[] = "calibration.u3p"
W_DETECT_OBJECTS_JOB[] = "find_objects.u3p"
W_DETECT_TARGET_JOB[] = "find_target.u3p"
;----------------------------------------
; Adjust validation z safety offset [mm]
W_SAFETY_OFFSET_MM = 10
;----------------------------------------
; Number of calibration poses (min. 5)
W_NUM_CALIBRATION_POSES = 5
;----------------------------------------
; Comment out the correct line depending
; on the selected detection
W_USER_COMMAND[] = "singleDetection"
;W_USER_COMMAND[] = "multiDetection"
;W_USER_COMMAND[] = "updateReferenceFrame"
;----------------------------------------
; Mobile Platform use case
W_BASE_NUM = 10
W_BASE_NAME[] = "wReferenceFrame"
W_MACHINE_POSES_TAUGHT = FALSE
```

## Teaching the poses

The movements to the robot poses are defined in the pose procedures of `wenglorUserConfig.src`. Add the movements to your taught poses inside them:

| Procedure | Purpose |
| --- | --- |
| `moveTocalibrationPose(poseNum)` | Moves to each calibration pose (`CASE 1` … `CASE 5`). Add or remove `CASE` blocks to match `W_NUM_CALIBRATION_POSES`. |
| `moveToDetectObjectsPose()` | Moves to the detection pose used for object recognition. |
| `moveToSafetyPose()` | Moves to a safety pose so the operator can remove the calibration plate (`camera_not_on_robot` only). |
| `moveToDetectTargetPose()` | Moves to the pose from which the calibration target is detected (`updateReferenceFrame` only). |

<img src="images/02_calibration_poses.png" alt="Calibration poses in moveTocalibrationPose()" class="medium"/>

For details on how to teach the poses, see [Robot Program](../3_0_robot_program/index.md).

> NOTE
>
> Also check that **KUKA** is selected in the robot manufacturer drop-down of the robot server on the Machine Vision Device website (e.g. B60, MVC). See [Settings on Device Website](https://wenglor.github.io/robot-vision-generic-string/4_0_robot_vision_server/4_2_0_settings_on_device_website/) in the wenglor robot vision manual.
