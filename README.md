# mealvoucher_doc

The Encore_POSLIB_API.docx document is the library generic Api document where interfaces, data structures,  return values,  and basic components are specified. This page contains sample codes and explanations for the convenience of software developers within the scope of meal voucher developments.

General Configuration Files
----------------------------
The library contains 2 configuration files. These are eftpos.cfg and devices.xml


**eftpos.cfg**

This file is the configuration file that contains the general library definitions

LOG_FILE_PATH specifies which directory the logs will be placed in. Transaction flow can be controlled via logs.

ex: LOG_FILE_PATH = C:\OKCDriver\Log

Moreover, log rotation and other log properties are defined in this file. Within the scope of the MV., as the slip will be printed by the library during the closing operation FISCAL_PRINTER must be 1 in the eftpos.cfg file. Other can be the same values from existing eftpos.cfg file


**devices.xml**

In this file, the configuration values of the pos devices are defined. Library calls should be made with the related pos id value. Also different connection types can be defined.In the Current Connection tag, the actively used connection can be determined. MealVoucherTransactionAction and Simple Transaction Action should be GUI. Other can be the same values from existing devices.xml file

**MV Generic Info**
- Other information needed in post-call meal voucher payment or values to be needed from the user are received or displayed via the gui.
- In Setcard contactless transactions, the card must be kept on the POS device for a long time. Otherwise, the card cannot be read.
- CancelTrx function can be used to interrupt the process or return to the pos main screen.
- GUI example:

![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/mv1.png?raw=true)


MV_SimpleAuthorize
------------------

int32_t D10FlowController::MV_SimpleAuthorize(uint64_t amount, AuthResponse**  authResponse, SLIP_LIST** slipList)
SUCCESS (0) is returned on success, related error code is returned on failure

The payment process is done through simple-payment type and using gui. The call is made with amount information only. Meal card selection is done through the gui.The slip information as a result of the transaction is returned in the slipList and the transaction result information is returned in the authResponse. Slips are printed on the library side during the closing of the receipt. The memory management of the 2nd and 3rd variables is the responsibility of the caller and should be deleted after the operation.Payments can be partial. All payments (transaction) of a receipt are written to the lastreceipt file. The payment information of the last transaction is written in the lastslip file. The slip information obtained as a result of the payment is written to the slipdb folder.

**Usage example:**

```
  AuthResponse* authResponse = nullptr;
  SLIP_LIST* slipList = nullptr;
  int amount = 200

  // Getting device.
  UnmanagedEftLib::EftPosDevice* eftPosDevice = eftpos.CreatePosDevice(gPosId);
  if (!eftPosDevice)
  {
      // error...
  }			

  int ret = eftPosDevice->MV_SimpleAuthorize(amount, &authResponse, &slipList);
  if(ret)
  {
    // error...
  }

  eftPosDevice->Free(authResponse);
  eftPosDevice->Free(slipList);		
```



MV_Void
-------

int32_t D10FlowController::MV_Void(VoidRequest& voidReq, AuthResponse** authResponse, SLIP_LIST** slipList)
SUCCESS (0) is returned on success, related error code is returned on failure

It is used to cancel a meal voucher payment. The relevant meal voucher card type, reference number, the batchno, the stanno  required during the void process are entered through the GUI. In Multinet void transactions, the reference number is sufficient. There is no batchno and stanno information on the slip.

- Endered iptal işleminde referans numarasına slipte bulunan sıra numarası batchNo ya slipte bulunan Grupno stanno ya slipte bulunan gene sıra numarası bilgisi girilmelidir 
- Setcard iptal işlemlerinde slip üzerindeki  ONAY NO, BATCH NO, STAN NO: değerleri sırasıyla girilmelidir. (baştaki sıfır göz ardı edilir)
- Metrolpol iptal işlemlerinde slip üzerindeki  ONAY NO, BATCH NO, STAN NO: değerleri sırasıyla girilmelidir.(basştaki sıfır göz ardı edilir)

**Usage example:**

```
VoidRequest voidRequest;
SLIP_LIST* slipList = 0;
AuthResponse* authResponse = 0;

// Getting device.
UnmanagedEftLib::EftPosDevice* eftPosDevice = eftpos.CreatePosDevice(gPosId);
if (!eftPosDevice)
{
	// error...
}

ret = eftPosDevice->MV_Void(voidRequest, &authResponse, &slipList);
if(ret)
{
	// error...
}

eftPosDevice->Free(authResponse);
eftPosDevice->Free(slipList);

slipList = nullptr;
authResponse = nullptr;
```

SUCCESS (0) is returned on success, corresponding error code is returned on failure

MV_BalanceInquiry
-----------------

int32_t D10FlowController::MV_BalanceInquiry()
SUCCESS (0) is returned on success, related error code is returned on failure

**Usage example:**

```
// Getting device.
UnmanagedEftLib::EftPosDevice* eftPosDevice = eftpos.CreatePosDevice(gPosId);
if (!eftPosDevice)
{
  // error...
}

int ret = eftPosDevice->MV_BalanceInquiry();
```

As a result of the call, the value returned by the pos is displayed on the pos device for some mv firms, for others, it will be displayed on GUI screen.

Multinet -> GUI'de gözükmektedir.
Metropol -> Pos Cihazı ekranında gözükmeketedir 
setcard  ->  Pos Cihazı ekranında gözükmeketedir 
edenred  ->  Pos Cihazı ekranında gözükmeketedir 
sodexo   ->   GUI'de gözükmektedir.



MV_EndOfDay
------------





MV_GetAdditionalCopy
--------------------

