.. _common-aocoda-f405v3:

==================
Aocoda-RC F405V3
==================
.. image:: ../../../images/aocoda-f405v3/aocoda_f405v3.jpg
     :target: ../_images/aocoda-f405v3/aocoda_f405v3.jpg


the above image and some content courtesy of `aocoda-rc.com <https://www.aocoda-rc.com/>`__

.. note:: Both versions use the same firmware and differ in the IMU used

Specifications
==============

-  **Processor**

   -  STM32F405RGT6 ARM (168MHz)
   -  microSDCard for datalogging


-  **Sensors**

   -  InvenSense MPU6000 or ICM-42688P IMU (accel, gyro)
   -  BMP280 barometer
   -  Voltage & 130A current sensor


-  **Power**

   -  3-6S Lipo input power
   -  5V 2.5A BEC for peripherals
   -  9V 3A BEC for video


-  **Interfaces**

   -  USB port (type-C)
   -  5x UARTS
   -  8x PWM outputs DShot capable, 4 outputs BiDirDShot capable
   -  1x RC input PWM/SBUS
   -  I2C port for external compass, airspeed sensor, etc.
   -  Switchable Camera inputs
   -  DJI Goggles
   -  Power Monitor
   -  Buzzer and LED stripe
   -  Built-in OSD

-  **Size and Dimensions**

   - 37 mm x 37 mm
   - 8.8 g

-  **Mounting Hole**

   - 30.5 mm x 30.5 mm


Wiring Diagram
==================

.. image:: ../../../images/aocoda-f405v3/aocoda_f405v3_wiring_diagram.png
     :target: ../_images/aocoda-f405v3/aocoda_f405v3_wiring_diagram.png
  
Default UART order
==================

- SERIAL0 = USB (MAVLink2)
- SERIAL1 = UART1 (GPS)
- SERIAL2 = UART2 (RCinput) 
- SERIAL3 = UART3 (ESC Telemetry)
- SERIAL4 = UART4 (VTX or DJI HD Air Unit)
- SERIAL5 = UART5 (ESP32-C3)

.. note:: Serial port protocols (Telem, GPS, etc.) can be adjusted to personal preferences.

RC Input
========

RC input is configured by default on SERIAL2 (USART2). The 4V5 pin is supplied by both USB and on board 5V BEC from battery.

 - SBUS/DSM/SRXL connects to the RX2 pin.
 - FPort requires connection to TX2. Set :ref:`SERIAL2_OPTIONS<SERIAL2_OPTIONS>` = 7. See FPort Receivers.
 - CRSF also requires a TX2 connection, in addition to RX2, and automatically provides telemetry.
 - SRXL2 requires a connection to TX2 and automatically provides telemetry. Set :ref:`SERIAL2_OPTIONS<SERIAL2_OPTIONS>` to “4”.

Any UART can be used for RC system connections in ArduPilot also. See :ref:`common-rc-systems` for details.

RSSI Input
==========================

Analog inputs are provided.

- RSSI reference pin number is "13".

.. note:: set :ref:`RSSI_TYPE<RSSI_TYPE>` = 1 for analog RSSI, = 3 for RSSI provided by RC protocols such as CRSF.

OSD Support
===========

The Aocoda-RC F405V3 has an on-board OSD using :ref:`OSD_TYPE<OSD_TYPE>` =  1 (MAX7456 driver). The CAM and VTX pins provide connections for using the internal OSD.

DJI Video and OSD
=================

An "SH1.0 6P" connector supports a standard DJI HD VTX connection and SERIAL4 is already setup for this by default.  Pin 1 of the connector is 9V or VBat so be careful not to connect this to any peripheral requiring 5V.

Dshot capability
================

Motor 1-4 are Bi-Directional DShot capable. All motor/servo outputs 1-8 are Dshot, and PWM capable. However, mixing Dshot and normal PWM operation for outputs is restricted into groups, ie. enabling Dshot for an output in a group requires that ALL outputs in that group be configured and used as Dshot, rather than PWM outputs. The output groups that must be the same (PWM rate or Dshot, when configured as a normal servo/motor output) are: 1/2, 3/4, 5/6, 7/8. Outputs 9 (LED) are in separate groups and are not Dshot capable.

GPIOs
=====

The Aocoda-RC F405V3 outputs can be used as GPIOs (relays, buttons, RPM etc). To use them you need to set the output's ``SERVOx_FUNCTION`` to -1. See :ref:`common-gpios` page for more information.

The numbering of the GPIOs for PIN variables in ArduPilot is:

 - PWM1 50
 - PWM2 51
 - PWM3 52
 - PWM4 53
 - PWM5 54
 - PWM6 55
 - PWM7 56
 - PWM8 57

 - LED 58
 - BUZZER 80

 - PINIO1 81 for 9V DCDC control (HIGH:on; LOW:off)
 - PINIO2 82 for ESP32-C3 control (HIGH:on; LOW:off)
 - PINIO3 83 for CAM1/CAM2 control (HIGH:CAM2; LOW:CAM1)

VTX Power/ESP32-C/Camera Control
================================

- GPIO 81 controls the VTX BEC output to pins marked “9V”. Setting this GPIO low removes voltage supply to pins.
- GPIO 82 controls the ESP32-C3 working status. Setting this GPIO low stop ESP32-C from working.
- GPIO 83 controls the CAM1/CAM2 input. Setting this GPIO low for CAM1, high for CAM2.

Set a RELAYx_PIN to “81” to control the switching. Then select an RC channel for control (Chx) and set its RCx_OPTION to the appropriate Relay (1-6) that you had set its pin parameter above.

For example, use Channel 10 to control the switch using Relay 2:

    RELAY_PIN2 = “81”

    RC10_OPTION = “34” (Relay2 Control)

.. warning:: PINIO1 is for 9V DC-DC control (HIGH:on; LOW:off). Default 9V DC is ON. Please install an antenna on VTX when battery powered.

ESP32-C
========

ESP32-C is a diversity chip for custom purpose, default with drone id application.

1. `ArduRemoteID for DroneID application <https://github.com/ArduPilot/ArduRemoteID>`__, compatible with `ASTM F3411 Specification for Remote ID and Tracking and ASD-STAN prEN 4709-002 Direct Remote Identification <https://mavlink.io/en/services/opendroneid.html>`__.
2. `DroneBridge-ESP32 for wifi telemetry application <https://github.com/DroneBridge/ESP32>`__

.. note:: UART5 TX on ESP32-C pin 3, UART5 RX on ESP32-C pin 2.

Connecting a GPS/Compass module
===============================

This board does not include a GPS or compass so an :ref:`external GPS/compass <common-positioning-landing-page>` should be connected in order for autonomous modes to function.

.. note:: If the GPS is attached to UART1 TX/RX and powered from the adjacent 5V pins, a battery must be plugged in for power to be provided.

.. tip:: you could use the 4V5 pin to supply both the RC and the GPS without a battery attached on the bench for setup as long as the total current does not exceed the USB source (normally 1A on most computers)

Battery Monitor Settings
========================

These should already be set by default. However, if lost or changed:

Enable Battery monitor with these parameter settings :

:ref:`BATT_MONITOR<BATT_MONITOR>` = 4

Then reboot.

Battery monitor pins & options:

 - :ref:`BATT_VOLT_PIN<BATT_VOLT_PIN>` = 12
 - :ref:`BATT_CURR_PIN<BATT_CURR_PIN>` = 11
 - :ref:`BATT_VOLT_MULT<BATT_VOLT_MULT>` = 11
 - :ref:`BATT_AMP_PERVLT<BATT_AMP_PERVLT>` = 55.0 (note: Please calibrate before use, depending on current sensor.)

.. note:: this autopilot uses a high precision current sensor input which is sensitive to ESC switching noise. Please check carefully before use that current readings are accurate across the usage range. If not, low ESR capacitors on the ESC power inputs may need to be added.

Where to Buy
============


- `aocoda-rc.com <https://www.aocoda-rc.com/products/47>`__
- `aocoda-rc aliexpress <https://www.aliexpress.com/item/1005005610849417.html>`__


Firmware
========
This board does not come with ArduPilot firmware pre-installed. Use instructions :ref:`here to load ArduPilot the first time<common-loading-firmware-onto-chibios-only-boards>`.

Firmware for these boards can be found `here <https://firmware.ardupilot.org>`_ in  sub-folders labeled "Aocoda-RC-F405V3".

.. note:: If you experience issues with the device ceasing to initialize after power up, see :ref:`common-when-problems-arise` section for H7 based autopilots for a possible solution.

[copywiki destination="plane,copter,rover,blimp"]
