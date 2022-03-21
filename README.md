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

>int32_t D10FlowController::MV_SimpleAuthorize(uint64_t amount, AuthResponse**  authResponse, SLIP_LIST** slipList)

SUCCESS (0) is returned on success, related error code is returned on failure.

**Return values:** <br/>
LIBRARY_CANNOT_BE_CREATED <br/>
READ_DEVICE_CFG_ERROR <br/>
AMOUNT_ERROR <br/>
MEALVOUCHER_GUI_ERROR <br/>
TRANSACTION_INTERRUPTED_BY_USER <br/>
ACTION_HANDLER_TIMEOUT <br/>
ACTION_HANDLER_ERROR <br/>
MESSAGE_CREATE_ERROR <br/>
CONNECTION_FAILED <br/>
RECV_ERROR <br/>
TRANSACTION_INTERRUPTED_BY_POS <br/>
MESSAGE_INTEGRITY_ERROR <br/>
RECV_TIMEOUT <br/>
SEND_TIMEOUT <br/>
SEND_ERROR<br/>


The payment process is done through simple-payment type and using GUI. The call is made with amount information only. Meal card selection is done through the GUI. The slip information as a result of the transaction is returned in the slipList and the transaction result information is returned in the authResponse. Slips are printed on the library side during the closing of the receipt. The memory management of the 2nd and 3rd variables is the responsibility of the caller and should be deleted after the operation. Payment can be partial. All payments (transaction) of a receipt are written to the lastreceipt file. The payment information of the last transaction is written in the lastslip file. The slip information obtained as a result of the payment is written to the slipdb folder. They can be controlled.

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

- **Receipt examples**

Metropol:<br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/metropol_payment.png?raw=true)


SetCard:<br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/setcard_payment.png?raw=true)


Edenred:<br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/edenred_payment.png?raw=true)

Sodexo:<br/>
Sodexo transaction process is only possible by extra input via pos screen.

Choose "Kartlı İslem". It means "Payment with credit card" <br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/sodexo_flow1.jpg?raw=true) <br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/sodexo_flow2.jpg?raw=true) <br/>
Choose "Satıs". It means "Sales" <br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/sodexo_flow3.jpg?raw=true) <br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/sodexo_flow4.jpg?raw=true) <br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/sodexo_flow5.jpg?raw=true) <br/>
Receipts:<br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/sodexo_payment.jpg?raw=true) <br/>




MV_Void
-------

> int32_t D10FlowController::MV_Void(VoidRequest& voidReq, AuthResponse** authResponse, SLIP_LIST** slipList)
SUCCESS (0) is returned on success, related error code is returned on failure

It is used to cancel a meal voucher payment. 

- The relevant meal voucher card type, reference number, the batchno, the stanno  required during the void process are entered through the GUI. 

- The reference number to be entered can vary on the slip of each meal voucher type. You can find related information below.

- Since the additional information will be entered with the GUI, first parameter of MV_Void (VoidRequest) can be used with default 0 and "" empty string values. So it is ignored during transaction process.

- If there is no batchno or stanno on the slip. You can leave them 0 when you input on the GUI.

SUCCESS (0) is returned on success, corresponding error code is returned on failure

**Return values:** <br/>
LIBRARY_CANNOT_BE_CREATED  <br/>
READ_DEVICE_CFG_ERROR  <br/>
MEALVOUCHER_GUI_ERROR <br/>
TRANSACTION_INTERRUPTED_BY_USER <br/>
ACTION_HANDLER_TIMEOUT  <br/>
ACTION_HANDLER_ERROR <br/>
MESSAGE_CREATE_ERROR <br/>
CONNECTION_FAILED <br/>
RECV_ERROR <br/>
TRANSACTION_INTERRUPTED_BY_POS <br/>
MESSAGE_INTEGRITY_ERROR <br/>
RECV_TIMEOUT <br/>
SEND_TIMEOUT <br/>
SEND_ERROR <br/> <br/>

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
```
 <br/> <br/>


**Necessary Information found on the payment slip**  <br/>

***Note: Leading zero values are ignored in GUI input for reference number***

Metropol:<br/>
You have to enter "Onay No" on the payment slip as the reference number in gui. <br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/metropol_void.png?raw=true) <br/>


SetCard:<br/>
You have to enter "Onay No" on the payment slip as the reference number in gui. <br/>

Edenred:<br/>
You have to enter "Grup" as batchno, "Sıra" as stanno and "Sıra" as the reference number in gui. <br/>

Sodexo:<br/>
You have to enter "Grup No" as batchno, "Islem No" as stanno and "Onay Kodu" as the reference number in gui. <br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/sodexo_void.png?raw=true) <br/> <br/> <br/> <br/>


**Void receipt examples**  <br/>
Metropol:<br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/metropol_void.png?raw=true) <br/> <br/>
SetCard:<br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/setcard_void.png?raw=true) <br/> <br/>
Edenred:<br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/edenred_void.jpg?raw=true) <br/> <br/>
Sodexo:<br/>
![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/sodexo_void2.jpg?raw=true) <br/> <br/> <br/>




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

Multinet -> GUI <br/>
Metropol -> On Pos   <br/>
setcard  ->  On Pos  <br/>
edenred  ->  On Pos  <br/>
sodexo   ->   GUI <br/><br/>


MV_IsLastTransactionDone
--------------------
> int MV_IsLastTransactionDone (SlipStamp**)

It is the function to returns whether the last operation was successful or not. The user may query whether last transaction on the POS is saved for the end of day. Integration Card Reference No (cashRegisterRefNo) is received within the SlipStamp structure.

SlipStamp contains the last slip stamp values. If the transaction is saved successfully, the function returns 0.

**Caution:** SlipStamp is created by library and users have to deallocate it after usage.

**Return values:** <br/>
LIBRARY_CANNOT_BE_CREATED <br/>
READ_LAST_SLIP_STAMP_ERROR <br/>
CANNOT_FOUND_LAST_SLIP_INFO <br/>
MESSAGE_CREATE_ERROR <br/>
CONNECTION_FAILED <br/>
RECV_ERROR<br/>
TRANSACTION_INTERRUPTED_BY_POS <br/>
MESSAGE_INTEGRITY_ERROR <br/>
RECV_TIMEOUT <br/>
SEND_TIMEOUT <br/>
SEND_ERROR <br/>
WRONG_MESSAGE <br/>
TRANSACTION_ERROR <br/> <br/>


**Usage example:**

```
SlipStamp* slipStamp = nullptr;

// Getting device.
UnmanagedEftLib::EftPosDevice* eftPosDevice = eftpos.CreatePosDevice(gPosId);
if (!eftPosDevice)
{
  // error...
}

ret = eftPosDevice->MV_IsLastTransactionDone(&slipStamp);
if (ret)
{
	STRING text = "Last Transaction is not successfully DONE. Status is " + I2S(ret);
	//error...
	return;
}

STRING str = STRING("\r\n**********************************************\r\nSlip Stamp:\n") +
"  BankId: " + IntegerToString(slipStamp->acquirerID) +
"  BatchNo: " + IntegerToString(slipStamp->batchNo) +
"  StanNo: " + IntegerToString(slipStamp->stanNo) +
"  UID: " + STRING(slipStamp->cashRegisterRefNo);
Show(str);

eftPosDevice->Free(slipStamp);
```

**Note:** In some exceptional cases, although the payment transaction is successful, the slip list returned from the function may be empty. In this case, it is necessary to query the status of the transaction and make an additional request if it is successful. This is a process that the cashier application has to manage. Please see "THE PROCESS WHERE THE TRANSACTION WAS SUCCESSFUL BUT THE SLIPS COULD NOT BE RECEIVED" <br/> <br/>


MV_GetAdditionalCopy
--------------------
> int32_t D10FlowController::MV_GetAdditionalCopy(SLIP_TYPE slipType, SlipStamp& slipStamp, SLIP_LIST** slips)

It is used to get additional slip copies. 

**Caution:**  <br/>
- SlipList is created by the library and users have to deallocate it after usage. SlipType parameter is not used yet. It is available for future usage. 
- Integration Card Reference No is required in order to be able to follow up the mv transaction over the ECR. It must be sent within the SlipStamp structure's cashRegisterRefNo field.

**Note:** This function is used through the slipstamp parameter obtained by the IsLastTransactionDone query in cases where the transaction is successful but the slips are not received. In more detail, in some exceptional cases, although the payment transaction is successful, the slip list returned from the function may be empty. In this case, it is necessary to query the status of the transaction and make an additional request if it is successful. This is a process that the cashier application has to manage. Please see "THE PROCESS WHERE THE TRANSACTION WAS SUCCESSFUL BUT THE SLIPS COULD NOT BE RECEIVED" <br/> <br/>


**Return values:**
LIBRARY_CANNOT_BE_CREATED    <br/>
READ_DEVICE_CFG_ERROR    <br/>
MEALVOUCHER_GUI_ERROR     <br/>
TRANSACTION_INTERRUPTED_BY_USER    <br/>
ACTION_HANDLER_TIMEOUT     <br/>
ACTION_HANDLER_ERROR    <br/>
WRONG_MESSAGE    <br/>
TRANSACTION_COULD_NOT_BE_FOUND_IN_BATCH    <br/>
MESSAGE_CREATE_ERROR    <br/>
CONNECTION_FAILED    <br/>
RECV_ERROR   <br/>
TRANSACTION_INTERRUPTED_BY_POS    <br/>
MESSAGE_INTEGRITY_ERROR    <br/>
RECV_TIMEOUT     <br/>
SEND_TIMEOUT    <br/>
SEND_ERROR    <br/>
WRONG_MESSAGE    <br/>


**Usage example:**

```
SlipStamp* slipStamp = nullptr;

// Getting device.
UnmanagedEftLib::EftPosDevice* eftPosDevice = eftpos.CreatePosDevice(gPosId);
if (!eftPosDevice)
{
  // error...
}

ret = eftPosDevice->MV_IsLastTransactionDone(&slipStamp);
if (ret)
{
	STRING text = "Last Transaction is not successfully DONE. Status is " + I2S(ret);
	//error...
	return;
}

SLIP_LIST* slipList = 0;
slipType_ = MERCHANT_CUSTOMER_COPY;
ret = eftPosDevice->MV_GetAdditionalCopy(slipType_, slipStamp, &slipList);

if(ret == SUCCESS)
{
	// Slips had been received and they can be printed when fcuCloseDoc called.
	fcuCloseDoc();
}

eftPosDevice->Free(slipList);
eftPosDevice->Free(slipStamp);
```


MV_EndOfDay
------------
> int MV_EndOfDay (EOD_INFO_LIST**, SLIP_LIST**)

It is used to initiate the end of day of all meal voucher payments.

If the function call is success, it returns 0.

Caution EOD_INFO_LIST and SlipList are created by library and users have to deallocate them after usage.

EOD_INFO_LIST output parameter contains only **acqId (mealvoucher id)** and **result** value for each eod transaction.

All banks end of day transactions are accomplished in a single MV_EndOfDay call.

**mealvoucher ids**    <br/>
0xA1:  Multinet  161    <br/>
0xA2:  Metropol  162    <br/>
0xA3:  Edenred  163    <br/>
0xA4:  Sodexho  164    <br/>
0xA5:  SetCard  165    <br/>

**Return values:**
LIBRARY_CANNOT_BE_CREATED
CONNECTION_FAILED
MESSAGE_CREATE_ERROR
RECV_ERROR
CONNECTION_FAILED
TRANSACTION_INTERRUPTED_BY_POS
MESSAGE_INTEGRITY_ERROR
RECV_TIMEOUT
SEND_TIMEOUT
SEND_ERROR
READ_SLIP_ERROR


**Usage example:**

```
SlipStamp* slipStamp = nullptr;

// Getting device.
UnmanagedEftLib::EftPosDevice* eftPosDevice = eftpos.CreatePosDevice(gPosId);
if (!eftPosDevice)
{
  // error...
}

ret = eftPosDevice->MV_IsLastTransactionDone(&slipStamp);
if (ret)
{
	STRING text = "Last Transaction is not successfully DONE. Status is " + I2S(ret);
	//error...
	return;
}

SLIP_LIST* slipList = 0;
slipType_ = MERCHANT_CUSTOMER_COPY;
ret = eftPosDevice->MV_GetAdditionalCopy(slipType_, slipStamp, &slipList);

if(ret == SUCCESS)
{
	// Slips had been received and they can be printed when fcuCloseDoc called.
	fcuCloseDoc();
}

eftPosDevice->Free(slipList);
eftPosDevice->Free(slipStamp);
```

<br/> <br/> <br/> <br/>





**THE PROCESS WHERE THE TRANSACTION WAS SUCCESSFUL BUT THE SLIPS COULD NOT BE RECEIVED:**<br/>

In some exceptional cases, although the payment transaction is successful, the slip list returned from the function may be empty. In this case, it is necessary to query the status of the transaction and make an additional request if it is successful. This is a process that the cashier application has to manage. <br/> 

![alt text](https://github.com/aphelix/mealvoucher_doc/blob/master/excp_flow.png?raw=true) <br/> <br/> <br/>







