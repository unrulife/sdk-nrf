.. _bluetooth_mesh_light:

Bluetooth: Mesh light
#####################

.. contents::
   :local:
   :depth: 2

The Bluetooth® mesh light sample demonstrates how to set up a mesh server model application, and control LEDs with Bluetooth mesh using the :ref:`bt_mesh_onoff_readme`.

.. note::
   This sample is self-contained, and can be tested on its own.
   However, it is required when testing the :ref:`bluetooth_mesh_light_switch` sample.

This sample also provides support for point-to-point Device Firmware Update (DFU) over the Simple Management Protocol (SMP).

Requirements
************

The sample supports the following development kits:

.. table-from-sample-yaml::

The sample also requires a smartphone with Nordic Semiconductor's nRF Mesh mobile app installed in one of the following versions:

  * `nRF Mesh mobile app for Android`_
  * `nRF Mesh mobile app for iOS`_

.. note::
   |thingy53_sample_note|

.. include:: /includes/tfm.txt

DFU requirements
================

The configuration overlay :file:`overlay-dfu.conf` enables DFU support in the application, and applies for the nRF52 series, including:

* nrf52dk_nrf52832

* nrf52840dk_nrf52840

* nrf52833dk_nrf52833

While this overlay configuration is only applicable for the nRF52 Series in this sample, DFU over SMP can be utilized on other platforms as well.

.. note::
   Point-to-point DFU for :ref:`zephyr:thingy53_nrf5340` is supported by default.
   See :ref:`thingy53_app_update` for more information about updating firmware image on :ref:`zephyr:thingy53_nrf5340`.

The DFU feature also requires a smartphone with Nordic Semiconductor's nRF Device Manager mobile app installed in one of the following versions:

  * `nRF Device Manager mobile app for Android`_
  * `nRF Device Manager mobile app for iOS`_

Overview
********

The mesh light sample is a Generic OnOff Server with a provisionee role in a mesh network.
There can be one or more servers in the network, for example light bulbs.

The sample instantiates up to four instances of the Generic OnOff Server model for controlling LEDs.
The number of OnOff Server instances depends on available LEDs, as defined in board DTS file.

Provisioning is performed using the `nRF Mesh mobile app`_.
This mobile application is also used to configure key bindings, and publication and subscription settings of the Bluetooth mesh model instances in the sample.
After provisioning and configuring the mesh models supported by the sample in the `nRF Mesh mobile app`_, you can control the LEDs on the development kit from the app.

Provisioning
============

The provisioning is handled by the :ref:`bt_mesh_dk_prov`.
It supports four types of out-of-band (OOB) authentication methods, and uses the Hardware Information driver to generate a deterministic UUID to uniquely represent the device.

Models
======

The following table shows the mesh light composition data for this sample:

.. table::
   :align: center

   =================  =================  =================  =================
   Element 1          Element 2          Element 3          Element 4
   =================  =================  =================  =================
   Config Server      Gen. OnOff Server  Gen. OnOff Server  Gen. OnOff Server
   Health Server
   Gen. OnOff Server
   =================  =================  =================  =================

.. note::
   When used with :ref:`zephyr:thingy53_nrf5340`, Element 4 is not available.
   :ref:`zephyr:thingy53_nrf5340` supports only one RGB LED, and treats each RGB LED channel as a separate LED.

The models are used for the following purposes:

* :ref:`bt_mesh_onoff_srv_readme` instances in elements 1 to N, where N is number of on board LEDs, each control LEDs 1 to N, respectively.
* Config Server allows configurator devices to configure the node remotely.
* Health Server provides ``attention`` callbacks that are used during provisioning to call your attention to the device.
  These callbacks trigger blinking of the LEDs.

The model handling is implemented in :file:`src/model_handler.c`, which uses the :ref:`dk_buttons_and_leds_readme` library to control each LED on the development kit according to the matching received messages of Generic OnOff Server.

User interface
**************

Buttons:
   Can be used to input the OOB authentication value during provisioning.
   All buttons have the same functionality during this procedure.

LEDs:
   Show the OOB authentication value during provisioning if the "Push button" OOB method is used.
   Show the OnOff state of the Generic OnOff Server of the corresponding element.

Configuration
*************

|config|

Source file setup
=================

This sample is split into the following source files:

* :file:`main.c` used to handle initialization.
* :file:`model_handler.c` used to handle mesh models.
* :file:`thingy53.c` used to handle preinitialization of the :ref:`zephyr:thingy53_nrf5340` board.
  Only compiled when the sample is built for :ref:`zephyr:thingy53_nrf5340` board.

DFU configuration
=================

To enable the DFU feature, set :makevar:`OVERLAY_CONFIG` to :file:`overlay-dfu.conf` when building the sample.
For example, when building from the command line, use the following command:

  .. code-block:: console

     west build -b <BOARD> -p -- -DOVERLAY_CONFIG="overlay-dfu.conf"

The configuration overlay :file:`overlay-dfu.conf` enables the DFU feature.
To review the required configuration alterations, open and inspect the :file:`overlay-dfu.conf` file.
For more information about using configuration overlay files, see :ref:`zephyr:important-build-vars` in the Zephyr documentation.

FEM support
===========

.. include:: /includes/sample_fem_support.txt

Building and running
********************

.. |sample path| replace:: :file:`samples/bluetooth/mesh/light`

.. include:: /includes/build_and_run_ns.txt

.. _bluetooth_mesh_light_testing:

Testing
=======

After programming the sample to your development kit, you can test it by using a smartphone with `nRF Mesh mobile app`_ installed.
Testing consists of provisioning the device and configuring it for communication with the mesh models.

Provisioning the device
-----------------------

.. |device name| replace:: :guilabel:`Mesh Light`

.. include:: /includes/mesh_device_provisioning.txt

Configuring models
------------------

See :ref:`ug_bt_mesh_model_config_app` for details on how to configure the mesh models with the nRF Mesh mobile app.

Configure the Generic OnOff Server model on each element on the **Mesh Light** node:

* Bind the model to **Application Key 1**.

  Once the model is bound to the application key, you can control the first LED on the device.
* In the model view, tap :guilabel:`ON` (one of the Generic On Off Controls) to light up the first LED on the development kit.

Make sure to complete the configuration on each of the elements on the node to enable controlling each of the remaining three LEDs.

Running DFU
===========

After the sample is built with the :file:`overlay-dfu.conf` and programmed to your development kit, support for FOTA upgrades is enabled.
See FOTA in Bluetooth mesh (link) for instructions on how to perform FOTA upgrade and initiate the DFU process.

Dependencies
************

This sample uses the following |NCS| libraries:

* :ref:`bt_mesh_onoff_srv_readme`
* :ref:`bt_mesh_dk_prov`
* :ref:`dk_buttons_and_leds_readme`

In addition, it uses the following Zephyr libraries:

* ``include/drivers/hwinfo.h``
* :ref:`zephyr:kernel_api`:

  * ``include/kernel.h``

* :ref:`zephyr:bluetooth_api`:

  * ``include/bluetooth/bluetooth.h``

* :ref:`zephyr:bluetooth_mesh`:

  * ``include/bluetooth/mesh.h``

The sample also uses the following secure firmware component:

* :ref:`Trusted Firmware-M <ug_tfm>`
