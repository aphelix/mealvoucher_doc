# mealvoucher_doc

The Encore_POSLIB_API.docx document is the library generic Api document where interfaces, data structures,  return values,  and basic components are specified. This page contains sample codes and explanations for the convenience of software developers within the scope of meal voucher developments.

General Configuration Files
----------------------------
The library contains 2 configuration files. These are eftpos.cfg and devices.xml


eftpos.cfg 

This file is the configuration file that contains the general library definitions

LOG_FILE_PATH specifies which directory the logs will be placed in. Transaction flow can be controlled via logs.

ex: LOG_FILE_PATH = C:\OKCDriver\Log

Moreover, log rotation and other log properties are defined in this file. Within the scope of the MV., as the slip will be printed by the library during the closing operation FISCAL_PRINTER must be 1 in the eftpos.cfg file. Other can be the same values from existing eftpos.cfg file


devices.xml

In this file, the configuration values of the pos devices are defined. Library calls should be made with the related pos id value. Also different connection types can be defined.In the Current Connection tag, the actively used connection can be determined. MealVoucherTransactionAction and Simple Transaction Action should be GUI. Other can be the same values from existing devices.xml file

MV Generic Info
- Other information needed in post-call meal voucher payment or values to be needed from the user are received or displayed via the gui.
- In Setcard contactless transactions, the card must be kept on the POS device for a long time. Otherwise, the card cannot be read.

