.. _doc_bl_storage:

Bootloader storage
##################

.. contents::
   :local:
   :depth: 2

The bootloader storage library is used to read and write both one-time programmable (OTP) data and a non-volatile counter.
It can be used by both the |NSIB| (NSIB) and the application, but the application has read access only.

The library has the following functions for either reading or writing, or in some cases both reading and writing:

* Image slot addresses
* A monotonic counter used to enforce anti-rollback protection
* Hashes of public keys
* Invalidation tokens used to revoke public keys
* Additional public key metadata

The library uses either the OTP region of the User information configuration registers (UICR), when present on SoCs like the nRF9160 or nRF5340, or the internal flash memory.
When the library uses the internal flash memory, the bootloader blocks the write access before booting the next image.

See :ref:`bootloader_provisioning` for more information about the provisioned data and how the bootloader uses it.

See :file:`tests/subsys/bootloader/bl_storage/` for tests of the library.

API documentation
*****************

| Header file: :file:`include/bl_storage.h`
| Source files: :file:`subsys/bootloader/bl_storage/`

.. doxygengroup:: bl_storage
   :project: nrf
   :members:
