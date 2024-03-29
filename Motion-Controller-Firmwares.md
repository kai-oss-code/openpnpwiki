## Controllers and Firmwares for Advanced Motion Control

This page list Controllers and Firmwares that are known to be compatible with [[GcodeAsyncDriver]] and [[Advanced Motion Control]] features. Other controllers may also support the needed features, if you think your controller can do it, please [contact us in the user group](https://groups.google.com/forum/#!forum/openpnp). 

### Key Features

These are of course _in addition_ to basic homing and motion commands like [`G1`](https://www.reprap.org/wiki/G-code#G0_.26_G1:_Move) etc.

1. The controller provides a [`M115`](https://www.reprap.org/wiki/G-code#M115:_Get_Firmware_Version_and_Capabilities) firmware report command. This is used to automatically detect the type of the firmware in OpenPnP. Some automated setup can then be provided by the [[Issues and Solutions]] system. Even for a non-motion controller (e.g. a feeder control board) it is recommended to implement this as a minimum, to easily identify a controller on a port and to allow for [[automatic Issues & Solutions auto-setup|Issues-and-Solutions]]. 

The following requirements apply for controllers with axes attached:

2. The controller can manage extra axes (`A`, `B`, `C` etc.) as true axes, with simultaneous motion. Controllers/firmwares that only support multiplexing "extruder" `E` axes with `T` [tool select commands](https://www.reprap.org/wiki/G-code#T:_Select_Tool) are not valid. Any mixing of axes must be correctly supported, including all aspects of feed rate and acceleration limiting according to the [NIST RS274/NGC Interpreter – Version 3 standard](https://www.nist.gov/publications/nist-rs274ngc-interpreter-version-3), more specifically section "2.1.2.5 Feed Rate", or better. 

3. The controller can report axes positions, including extra axes (`A`, `B`, `C` etc.), typically with the [`M114`](https://www.reprap.org/wiki/G-code#M114:_Get_Current_Position) command.

4. The controller can reset axes positions, including extra axes (`A`, `B`, `C` etc.), typically with the [`G92`](https://www.reprap.org/wiki/G-code#G92:_Set_Position) command. 

5. `G92` must work correctly when motion is still pending. Either by implicitly waiting for still-stand or (better!) by allowing on-the-fly offsetting. 

6. The controller must be able to wait for motion completion, typically with the [`M400`](https://www.reprap.org/wiki/G-code#M400:_Wait_for_current_moves_to_finish) command. Any further commands sent after the `M400` must be suspended until motion completion. The controller must only acknowledge the command, when motion is complete i.e. the "ok" response must be suspended until then, providing blocking synchronization to OpenPnP. 

7. The controller must support dynamic acceleration and/or jerk limits, typically by the [`M204`](https://www.reprap.org/wiki/G-code#M204:_Set_default_acceleration) command for acceleration or the [`M201.3`](https://makr.zone/tinyg-new-g-code-commands-for-openpnp-use/577/) command for jerk.

## Duet3D

[Duet3D 2/3 controllers](https://www.duet3d.com/index.php?route=common/home#products) firmwares have been perfected for use with OpenPnP. Many thanks to Duet3D for providing a free Duet 3 board and to [dc42](https://github.com/dc42) for implementing substantial improvements, and accepting a crucial [pull request](https://github.com/Duet3D/RepRapFirmware/pull/471). For advanced OpenPnP use, Duet firmware has been improved in...

* USB serial speed
* Fixes for compressed G-code parsing
* Fixes and configurable option for correct feed rate calculations according to section 2.1.2.5 of the NIST G-Code standard.
* Configurable grace period for proper look-ahead planning

Firmware [version 3.3beta](https://github.com/Duet3D/RepRapFirmware/wiki/Changelog-RRF-3.x-Beta-&-RC#reprapfirmware-33beta1) or newer must be used to support most OpenPnP Advanced Motion Control features. Use the [[Issues and Solutions]] system to help you detect the correct version and configuration and to setup proper G-code.

Refer to the Duet3D Wiki on how to upgrade:

* [Duet 2 Installing and Updating Firmware](https://duet3d.dozuki.com/Wiki/Installing_and_Updating_Firmware)

* [Duet 3 Installing and Updating Firmware](https://duet3d.dozuki.com/Wiki/Getting_Started_With_Duet_3#Section_Updating_Duet_3_main_board_firmware)

## Smoothieware

A special Smoothieware firmware for OpenPnP is available. It contains some bug-fixes and features that are crucial for use with OpenPnP but are not present/accepted in the official Smoothieware firmware. The firmware and more details are available here:

* [makr.zone: "Smoothieware: New Firmware for PnP"](https://makr.zone/smoothieware-new-firmware-for-pnp/500/)

Refer to the Smoothieware Wiki on how to upgrade:

* [Flashing Smoothie Firmware](http://smoothieware.org/flashing-smoothie-firmware)

Please make sure your Smoothieware is configured to use true axes, i.e. `A` `B` `C`, not extruders (we're not 3D printing!). If your config.txt contains something like this:
  
```
# Extruder module configuration
# See http://smoothieware.org/extruder
extruder.hotend.enable                          true          # Whether to activate the extruder module at all. All configuration is ignored if false
extruder.hotend.steps_per_mm                    8.8888      # Steps per mm for extruder stepper
```

Then you must remove the `extruder` parts and instead use the `delta`, `epsilon` and `zeta` definitions as described in the [Smoothieware 6axis page](https://smoothieware.org/6axis).

If you skip this, you will get a complaint by [[Issues and Solutions]] saying "The driver does not report axes in the expected X Y Z A B C order".

![driver reported](https://user-images.githubusercontent.com/9963310/109156343-02633d00-7771-11eb-8f22-73a0af0ef0a7.png)

## Marlin 2.0

OpenPnP user Bill made a Marlin 2.0 port to Teensy 4.1 with advanced axis support. More information there:

* [Bill's Marlin 2.0 fork](https://github.com/bilsef/Marlin/tree/Teensy4.1_PnP_6axis)
* [Teensy 4.1 controller base PCB](https://github.com/bilsef/teensy4_pnp_controller)

## TinyG 

[TinyG](https://synthetos.com/project/tinyg) is often used for Pick & Place because it comes with the Liteplacer kit. Some features have been added to TinyG to make it more like other controllers and to optimize its use with OpenPnP. The firmware and more details are available here:

* [makr.zone: TinyG: New G-code commands for OpenPnP use](https://makr.zone/tinyg-new-g-code-commands-for-openpnp-use/577/).
* See the [proposed changes (Pull Request)](https://github.com/synthetos/TinyG/pull/258).
* Follow [the discussion](https://groups.google.com/d/msg/openpnp/veyVAwqS0do/Zsn73noGBQAJ).

The changes have now officially been adopted by the TinyG project. But please still use the [makr.zone link](https://makr.zone/tinyg-new-g-code-commands-for-openpnp-use/577/), as the official firmware downloads are not yet updated and additional guidance is needed. 

___

## Advanced Motion Control Topics

### Motion Control
- [[Advanced Motion Control]]
- [[GcodeAsyncDriver]]
- [[Motion Planner]]
- [[Visual Homing]]
- [[Motion Controller Firmwares]]

### Machine Axes
- [[Machine Axes]]
- [[Backlash-Compensation]]
- [[Transformed Axes]]
- [[Linear Transformed Axes]]
- [[Mapping Axes]] 
- [[Axis Interlock Actuator]]

### General
- [[Issues and Solutions]]
