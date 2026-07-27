# Troubleshooting

## Insufficient calibration accuracy

- Use more than five calibration poses (seven to eleven give better results). Add `CASE` blocks in `moveTocalibrationPose()` and update `W_NUM_CALIBRATION_POSES` to match.
- Increase the variation between poses — especially in the pose angles. The variance of the calibration *movements* matters more than the variance of the poses.
- Make sure the calibration plate covers as much of the camera image as possible and is fully visible.
- Prefer a wenglor ZVZJ calibration plate over a printed one (typical reprojection error `0.1` vs `0.5`). If printing, print at actual size on stiff, flat material.
- Check the reprojection error returned by `calibration:calculate` — high values indicate a poor calibration.

## Height offset in detected poses

- Check that the uniVision job is set properly, especially the height offset from the calibration target to the object in the **Device Robot Vision**.
- Ensure the correct tool (TCP) is selected — the program reads the current pose via `$POS_ACT`.

## Communication errors

- Verify that the network configuration of the wenglor vision device matches your setup (default device IP `192.168.100.1`, robot server port `6008`) in `wenglorVision.xml`.
- Make sure `wenglorVision.xml` was copied to `C:\KRC\ROBOTER\Config\User\Common\EthernetKRL\` and that the **EthernetKRL (EKI)** technology package is installed.
- Check that the name `wenglorVision` at `W_CONNECTION[]` in `wenglorUserConfig.src` matches the XML file name.
- Ensure the robot server on the vision device is active: device website → Jobs → Processing Instance → Robot Server, with **KUKA** selected as the robot manufacturer.
- Check general network connectivity and firewall rules between the controller and the device.

## Error codes returned by the device

If the robot server returns a negative error code (`-5001` … `-5010`), the example maps it to a readable message in `wenglorGlobal.setReturnError` and shows it on the smartPAD via `MsgNotify` before exiting.

For the meaning of each code, see the [Generic Robot Vision Interface → Error codes](https://wenglor.github.io/robot-vision-generic-string/4_0_robot_vision_server/4_5_0_generic_robot_vision_interface/#error-codes) in the wenglor robot vision manual.

## Program exits unexpectedly

- No calibration poses taught: make sure `moveTocalibrationPose()` contains a movement for each pose up to `W_NUM_CALIBRATION_POSES`.
- Detection pose not taught in `moveToDetectObjectsPose()` for the `camera_not_on_robot` use case.
- No reply from the camera, or an unparseable error code — the program shows a warning and exits. Check the device state via `state[<use_case>];`.
