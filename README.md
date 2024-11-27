# FastPay Merchant SDK [![Maintenance](https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=java&logoColor=white)]()   [![Maintenance](https://img.shields.io/badge/Android-3DDC84?style=for-the-badge&logo=android&logoColor=white)]() 

[![Maintenance](https://img.shields.io/badge/Maintained%3F-yes-green.svg)]()
[![Website shields.io](https://img.shields.io/website-up-down-green-red/http/shields.io.svg)](https://fastpay.blackace.tech)
[![PyPI license](https://img.shields.io/pypi/l/ansicolortags.svg)](https://pypi.python.org/pypi/ansicolortags/)
[![Generic badge](https://img.shields.io/badge/Version-v1.0.2-blue.svg)](https://shields.io/)


This is official documentation for fastpay merchant SDK.

![alt text](https://fast-pay.iq/images/logo_dark.png)

## Scaffolding Provided

This repository provides the following components:

- FastpaySDK.aar

## Features
1. Make payment transaction using Fastpay App.
2. Check the status of the payments which you make.
3. Verify payment with OTP.
4. SDK status provided by SDK callbacks.
5. Application redirect with required data while using fastpay personal applciation.

### SDK flow
![alt text](flow.png)

## Initialization

Please follow the below steps to integrate the payment SDK to an applications.

#### Step-1
Users need to add below dependency to their application module build.gradle file :

```gradle
android{
    ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```
```gradle
dependencies {
    implementation fileTree(include: ['*.jar','*.aar'], dir: 'libs')
    implementation 'com.journeyapps:zxing-android-embedded:4.1.0'
}
```
#### Step-2
Users need to add below dependency to their project module build.gradle file :
```gradle
allprojects {
    repositories {
        google()
        mavenCentral()
    }
}
```
#### Step-3 (Implementation)
 -  Import FastPaySDK in your class
```java
import com.fastpay.payment.model.merchant.FastpayRequest;
import com.fastpay.payment.model.merchant.FastpayResult;
```
 - Initiate FastPaySDK
    * **Store ID:** Merchant’s Store Id to initiate transaction
    * **Store Password:** Merchant’s Store password to initiate transaction
    * **Order ID/ Bill No:** Order ID/Bill number for the transaction, this value should be unique in every transaction
    * **Amount:** Payable amount in the transaction ex: “1000”
    * **Currency:** Payment currency in the transaction (Default: IQD)
    * **Environment:** Payment Environment to initiate transaction (SANDBOX for test & PRODUCTION for real life transaction).
    * **Call back Uri:** When the SDK redirect to the fastpay application for payment and after payment cancel or failed it throws a callback with this uri. It is used for deeplinking with the client app for catching callbacks from fastpay application.
    * **Callback( Sdk status, message):** There are four sdk status (e.g. *FastpayRequest.SDKStatus.INIT*) and status message.
 ```java
public enum SDKStatus{
        INIT,
        PAYMENT_WITH_FASTPAY_APP,
        PAYMENT_WITH_FASTPAY_SDK,
        CANCEL
}
```

Call back Uri

 ```java
callback URI pattern (SUCCESS): sdk://your.website.com/further/paths?status=success&transaction_id=XXXX&order_id=XXXX&amount=XXX&currency=XXX&mobile_number=XXXXXX&time=XXXX&name=XXXX
callback URI pattern (FAILED): sdk://your.website.com/further/paths?status=failed&order_id=XXXXX
``` 
 
Initialization
 ```java
FastpayRequest request = new FastpayRequest(this, "1111_1111", "password1234", amount, orderId, FastpaySDK.SANDBOX, "sdk://fastpay-sdk.com/callback", (sdkStatus, message) ->{
     Toast.makeText(MainActivity.this,message,Toast.LENGTH_LONG).show()
});
request.startPaymentIntent(YourActivity.this,FASTPAY_REQUEST_CODE);
```
 - Receive Payment result 
 Implement `onActivityResult()` overridden method to get transaction `success` & `failure` result using result code.
 Transaction success result can be receive from `FastpayResult` parcelable model using `FastpayResult.EXTRA_PAYMENT_RESULT` key and failure message can be receive as string using     `FastpayRequest.EXTRA_PAYMENT_MESSAGE` key.
 - Payment Success Data definition
   *  **Transaction Status:** Payment status weather it is success / failed.
   *  **Transaction ID:** If payment is successful then a transaction id will be available.
   *  **Order ID:** Unique Order ID/Bill number for the transaction which was passed at initiation time.
   *  **Payment Amount:** Payment amount for the transaction.
   *  **Payment Currency:** Payment currency for the transaction. 
   *  **Payee Name:** Payee name for a successful transaction.
   *  **Payee Mobile Number:** Payee name for a successful transaction.
   *  **Payment Time:** Payment occurrence time as the timestamp.


```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
   super.onActivityResult(requestCode, resultCode, data);

   if (requestCode == FASTPAY_REQUEST_CODE) {
       switch (resultCode) {
           case Activity.RESULT_OK:
               if (data != null && data.hasExtra(FastpayResult.EXTRA_PAYMENT_RESULT)) {
                   FastpayResult result = data.getParcelableExtra(FastpayResult.EXTRA_PAYMENT_RESULT);
                   Log.d("payment_result", result.getTransactionId());
               }
               break;
           case Activity.RESULT_CANCELED:
               if (data != null && data.hasExtra(FastpayRequest.EXTRA_PAYMENT_MESSAGE)) {
                   String message = data.getStringExtra(FastpayRequest.EXTRA_PAYMENT_MESSAGE);
                   Log.d("payment_result", "Canceled : " + message);
               }
               break;
        }
      }
   }
   ```
 - Result from Call backUrl (Please use it onCreate or onResume)

 ```java
 String amount = getIntent().getData().getQueryParameter("amount");
 String orderId = getIntent().getData().getQueryParameter("order_id");
 String status = getIntent().getData().getQueryParameter("status");
 String transaction_id = getIntent().getData().getQueryParameter("transaction_id");
```
> :warning: **Be sure to use try catch**
## License

    Copyright (C) 2021 Fastpay Technologies

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.

