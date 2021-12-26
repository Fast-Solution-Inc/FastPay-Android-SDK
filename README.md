# FastPay Merchant SDK 
[![Maintenance](https://img.shields.io/badge/Maintained%3F-yes-green.svg)]()
[![Website shields.io](https://img.shields.io/website-up-down-green-red/http/shields.io.svg)](http://shields.io/)
[![PyPI license](https://img.shields.io/pypi/l/ansicolortags.svg)](https://pypi.python.org/pypi/ansicolortags/)
[![Generic badge](https://img.shields.io/badge/Version-v1.0.0-red.svg)](https://shields.io/)


This is official documentation for fastpay payment SDK.

![alt text](https://cdn.fast-pay.cash/assets/images/logo/logo_color.png)

## Scaffolding Provided

This repository provides the following components that are common to our open source projects:

- FastpaySDK.aar
- Usermanual.pdf (Documentation)

## Steps

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
    * Store ID : Merchant’s Store Id to initiate transaction
    * Store Password: Merchant’s Store password to initiate transaction
    * Order ID/ Bill No : Order ID/Bill number for the transaction, this value should be unique in every transaction
    * Amount : Payable amount in the transaction ex: “1000”
    * Currency : Payment currency in the transaction (Default: IQD)
    * Environment : Payment Environment to initiate transaction (SANDBOX for test & PRODUCTION for real life transaction)
 ```java
 FastpayRequest request = new FastpayRequest(this, "1111_1111", "password1234",amount, orderId, FastpaySDK.SANDBOX);
 startActivityForResult(request.getIntent(), FASTPAY_REQUEST_CODE);
```
 - Receive Payment result 
 Implement `onActivityResult()` overridden method to get transaction `success` & `failure` result using result code.
 Transaction success result can be receive from `FastpayResult` parcelable model using `FastpayResult.EXTRA_PAYMENT_RESULT` key and failure message can be receive as string using     `FastpayRequest.EXTRA_PAYMENT_MESSAGE` key.
 - Payment Success Data definition
  *  *Transaction Status:* Payment status weather it is success / failed.
  *  *Transaction ID:* If payment is successful then a transaction id will be available.
  *  *Order ID:* Unique Order ID/Bill number for the transaction which was passed at initiation time.
  *  *Payment Amount:* Payment amount for the transaction.
  *  *Payment Currency:* Payment currency for the transaction. 
  *  *Payee Name:* Payee name for a successful transaction.
  *  *Payee Mobile Number:* Payee name for a successful transaction.
  *  *Payment Time:* Payment occurrence time as the timestamp.

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
   ```


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

