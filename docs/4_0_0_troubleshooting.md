# 4. Troubleshooting

Quick reference — jump to the matching symptom below for details:

| Symptom | Likely cause | Section |
| --- | --- | --- |
| Reprojection error is high / picks are inaccurate | Too few or too similar calibration poses, poor calibration plate visibility | [Insufficient calibration accuracy](#insufficient-calibration-accuracy) |
| Robot picks above/below the object | Height offset or wrong tool (TCP) | [Height offset in detected poses](#height-offset-in-detected-poses) |
| No connection / no reply from the camera | Wrong IP/port, EthernetKRL misconfigured, robot server inactive | [Communication errors](#communication-errors) |
| Program shows a warning with a negative number (`-5001` … `-5011`) | Device-side error, e.g. bad job name or missing calibration | [Error codes returned by the device](#error-codes-returned-by-the-device) |
| Program exits without a clear error | Poses not taught, missing detection pose | [Program exits unexpectedly](#program-exits-unexpectedly) |

## Insufficient calibration accuracy

- At least five calibration poses are required; teaching more improves accuracy (seven to eleven give better results). Add `CASE` blocks in `moveTocalibrationPose()` and update `W_NUM_CALIBRATION_POSES` to match.
- Increase the variation between poses — especially in the pose angles. The variance of the calibration *movements* matters more than the variance of the poses.
- Make sure the calibration plate covers as much of the camera image as possible and is fully visible.
- Prefer a wenglor ZVZJ calibration plate over a printed one (typical reprojection error `0.1` vs `0.5`). If printing, print at actual size on stiff, flat material.
- Check the reprojection error returned by `calibration:calculate` — high values indicate a poor calibration.

## Height offset in detected poses

- Check that the uniVision job is set properly, especially the height offset from the calibration target to the object in the **Device Robot Vision**.
- Ensure the correct tool (TCP) is selected — the program reads the current pose via `$POS_ACT`.

## Communication errors

- Verify that the network configuration of the wenglor Machine Vision Device matches your setup (default device IP `192.168.100.1`, robot server port `6008`) in the EthernetKRL configuration file.
- On **KSS**, make sure `wenglorVision.xml` was copied to `C:\KRC\ROBOTER\Config\User\Common\EthernetKRL\` and that the **EthernetKRL (EKI)** technology package is installed.
- On **iiQKA.OS2**, make sure `iiQKA_wenglorVision.xml` was imported under `Option packages` → `iiQKA.EthernetKRL` → `Ethernet configuration` and that the **iiQKA.EthernetKRL** option package is installed.
- Check that the name at `W_CONNECTION[]` in the user configuration matches the XML file name — `wenglorVision` on KSS, `iiQKA_wenglorVision` on iiQKA.OS2.
- Ensure the robot server on the Machine Vision Device is active: device website → Jobs → Processing Instance → Robot Server, with **KUKA** selected as the robot manufacturer.
- Check general network connectivity and firewall rules between the controller and the device.

## Error codes returned by the device

If the robot server returns a negative error code (`-5001` … `-5011`), the example maps it to a readable message in `wenglorGlobal.setReturnError` and shows it on the smartPAD via `MsgNotify` before exiting.

For the meaning of each code, see the [Generic Robot Vision API → Error codes](https://wenglor.github.io/robot-vision-generic-string/5_6_0_generic_robot_vision_api/#error-codes) in the wenglor robot vision manual.

## Program exits unexpectedly

- No calibration poses taught: make sure `moveTocalibrationPose()` contains a movement for each pose up to `W_NUM_CALIBRATION_POSES`.
- Detection pose not taught in `moveToDetectObjectsPose()` for the `camera_not_on_robot` use case.
- No reply from the camera, or an unparseable error code — the program shows a warning and exits. Check the device state via `state[<use_case>];`.
