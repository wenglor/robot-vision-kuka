# KUKA Robots Vision Manual

> NOTE
>
> This manual focuses exclusively on KUKA Robots-specific topics. For general robot vision information, please refer to the [wenglor robot vision manual](https://wenglor.github.io/robot-vision-generic-string/).

This repository contains an example KRL program to set up and start the generic vision interface to wenglor Machine Vision Devices on your KUKA robot.

The robot vision example for KUKA consists of the following files:

- `wenglorGlobal.dat`
- `wenglorGlobal.src`
- `wenglorMain.dat`
- `wenglorMain.src`
- `wenglorUserConfig.dat`
- `wenglorUserConfig.src`
- `wenglorVision.xml`

> NOTE
>
> The robot example is available on [www.wenglor.com/product/DNNF023](https://www.wenglor.com/product/DNNF023) → Downloads → Programming examples and configuration files → Examples_Robot_Vision.
>
> - The example was tested with the **KUKA KRC4** robot controller, **KR 6 R1820** Arc robot arm, software **KSS 8.3.33** with **EthernetKRL 3.2.4**. It was additionally tested with **KRC5** robot controllers.

---

## Table of Contents

1. [Installation & Setup](1_0_installation/index.md)
2. [User Configuration](2_0_user_configuration/index.md)
3. [Robot Program](3_0_robot_program/index.md)
4. [Troubleshooting](4_0_troubleshooting/index.md)
5. [Support & Feedback](5_0_support_and_feedback/index.md)

> NOTE
>
> The generic robot vision API (commands, return values, error codes), the calibration guidelines, and the uniVision job setup are documented once in the [wenglor robot vision manual](https://wenglor.github.io/robot-vision-generic-string/4_0_robot_vision_server/) and are **not** repeated here. This manual only describes how the KUKA example uses them.
