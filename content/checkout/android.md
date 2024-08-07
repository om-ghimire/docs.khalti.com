# Khalti Android SDK

Khalti Payment Gateway SDK for Andriod with default payment interface, works out of the box without having to add any additional user interface.

---
<p align="center">
<img width="302.6" height="115" src="https://raw.githubusercontent.com/khalti/checkout-sdk-flutter/main/assets/khalti_logo.png" height="100" alt="Khalti Payment Gateway" />
</p>



![Maven Central](https://img.shields.io/maven-central/v/com.khalti/checkout-android?color=%235C2D91)
<a href="https://docs.khalti.com/"><img src="https://img.shields.io/badge/Khalti-Docs-blueviolet" alt="Khalti Docs"></a>
<a href="https://github.com/khalti/checkout-sdk-android/blob/main/LICENSE"><img src="https://img.shields.io/badge/License-BSD--3-informational" alt="BSD-3 License"></a>
<a href="https://github.com/khalti/checkout-sdk-android/issues"><img src="https://img.shields.io/github/issues/khalti/checkout-sdk-android" alt="GitHub issues"></a>
<a href="https://khalti.com"><img src="https://img.shields.io/website?url=https%3A%2F%2Fdocs.khalti.com" alt="Website"></a>
<a href="https://www.facebook.com/khalti.official"><img src="https://img.shields.io/badge/follow--000?style=social&logo=facebook" alt="Follow Khalti in Facebook"></a>
<a href="https://www.instagram.com/khaltiofficial"><img src="https://img.shields.io/badge/follow--000?style=social&logo=instagram" alt="Follow Khalti in Instagram"></a>
<a href="https://twitter.com/intent/follow?screen_name=khaltiofficial"><img src="https://img.shields.io/twitter/follow/khaltiofficial?style=social" alt="Follow Khalti in Twitter"></a>
<a href="https://www.youtube.com/channel/UCrXM4HqK9th3E2a04Z9Lh-Q"><img src="https://img.shields.io/youtube/channel/subscribers/UCrXM4HqK9th3E2a04Z9Lh-Q?label=Subscribe&style=social" alt="Subscribe Youtube Channel"></a>



<!-- Welcome to Khalti's checkout documentation -->
## Installation
Read the introduction [here](https://docs.khalti.com/).

## Getting Started
Integrating Khalti Payment Gateway requires merchant account. 
You can always  .
!!! tip

    A merchant account is required for integration.

!!! info "Access Information"

    > **For Sandbox Access**

    Signup from 
    [here](https://test-admin.khalti.com/#/join/merchant) as a merchant.

    Please use 987654 as login OTP for sandbox env.
    
    > **For Production Access**

    Please visit [here](https://admin.khalti.com)
    
!!! info "Test Credentials for sandbox environment"

    > **Test Khalti ID for**
    9800000000
    9800000001
    9800000002
    9800000003
    9800000004
    9800000005
    
    > **Test MPIN**
    1111
    
    > **Test OTP**
    987654

Read the steps to integrate Khalti Payment Gateway in details [here](https://docs.khalti.com/getting-started/).


#### Requirements

- Android 5.0 and above
- AndroidX
- Android Studio 3 and above
#### Configuration

Add `checkout-android` to your `build.gradle` dependencies

```
implementation "com.khalti:checkout-android:$latest_version"
```

Also add the following lines inside the `android` block of your `build.gradle` file

```
compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
   }
```

#### Fetching `pidx`

Please go through the <a target="_blank" href="https://docs.khalti.com/khalti-epayment/#initiating-a-payment-request">Initiating a Payment request</a> to learn how to request the `pidx`
#### Setup

##### Building Config

Create an instance of `KhaltiPayConfig` with `publicKey`, `pidx`,  `returnUrl`, `environment` as parameters.

```kotlin
val config = KhaltiPayConfig(  
	publicKey = "<your_public_key>",  
	pidx = "<your_pidx>",  
	environment = Environment.TEST  
)
```

> Note : `environment` has 2 options; `Environment.prod` & `Environment.Test`

##### Building Khalti

Create an instance of `Khalti`, using `init` function, with the above `config` as parameter along with the callbacks `onPaymentResult`, `onMessage` and `onReturn`. Here, `onReturn` is optional and can be skipped.

```kotlin
Khalti.init(  
    LocalContext.current,  // context
    config,  
    onPaymentResult = { paymentResult, khalti ->  
	     // your implementation here     
    },  
    onMessage = { payload, khalti ->  
	     // your implementation here     
    },  
    onReturn = { khalti ->  
	     // your implementation here     
    }  
)
```

> Note : Make sure the context you passed to `Khalti` can be used to open an activity.

##### Callbacks

| Callback                                                 | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| :------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `onPaymentResult(result: PaymentResult, khalti: Khalti)` | Invoked on completion of payment. Inside this callback you'll have access to `PaymentResult` object and `Khalti` object<br><br>After completion of payment process, `khalti` will internally trigger the verification API. It's result is then propagated through the `onPaymentResult` callback. You'll receive an object of `PaymentResult`.<br>                                                                                                                                                                                                                                                                                   |
| `onMessage(payload: OnMessagePayload, khalti: Khalti)`   | Invoked on failures, exceptions or to convey message at any point of time. Inside this callback you'll have access to `OnMessagePayload` and `Khalti` object<br><br>`OnMessagePayload` contains `onMessageEvent` that dictates what type of event triggered the callback. It also contains a flag `needsPaymentConfirmation` which if `true` indicates that you must verify the status of the payment. It can be done through the `Khalti` object passed to this callback. Use `khalti.verify()`.<br><br>`onMessage` should not be considered as an error by itself. Always consult the `OnMessageEvent` for further clarification. |
| `onReturn(khalti: Khalti)`                               | This is an optional callback that is invoked when `return_url`'s page is successfully loaded. Inside this callback you'll have access to `Khalti` object                                                                                                                                                                                                                                                                                                                                                                                                                                                                             

#### Schema
```
PaymentResult {
	status: String,
	payload: PaymentPayload,
}
```

```
PaymentPayload {
	pidx: String,
	totalAmount: Long,
	status: String,
	transactionId: String,
	fee: Long,
	refunded: Boolean
	purchaseOrderId: String,
	purchaseOrderName: String,
	extraMerchantParams: Map<String, Object>
}
```

```
OnMessagePayload {
	event: OnMessageEvent,
	message: String,
	throwable: Throwable,
	code: Number,
	needsPaymentConfirmation: Boolean
}
```

```
OnMessageEvent {
	KPGDisposed, ReturnUrlLoadFailure, NetworkFailure, PaymentLookUpFailure, Unknown
}
```
#### Public functions in `Khalti`

| Function                                                               | Description                                                                                                                                                                                                                                                                                                                                                                    |
| :--------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `init(Context, KhaltiPayConfig, OnPaymentResult, OnMessage, OnReturn)` | Creates an instance of `Khalti`. Use this function to create an object of `Khalti`<br><br>`val khalti = Khalti.init()`                                                                                                                                                                                                                                                         |
| `open()`                                                               | Opens the payment page. After creating an object of `Khalti`. Use the said object to open the payment page.<br><br>`khalti.open()`                                                                                                                                                                                                                                             |
| `verify()`                                                             | Looks up of the payment status. `Khalti` sdk internally verifies the status of the payment, but if required the status lookup can be triggered again. The result is propagated through the callbacks passed during `init()`.  Use this function to confirm the payment status if and when `needsPaymentConfirmation` is `true` in `OnMessagePayload`.<br><br>`khalti.verify()` |
| `close()`                                                              | Closes the payment page. `Khalti` does not close the payment page and it's sole responsibility lies on the merchant. Use this function to close the payment page when required.<br><br>`khalti.close()`                                                                                                                                                                        |

#### Sample Implementations

##### Method 1: With `onReturn`

Initialize the `Khalti` Object

```kotlin
val khalti = Khalti.init(  
    LocalContext.current,  
    KhaltiPayConfig(  
        publicKey = "<public_key>",  
        pidx = "<pidx>",  
        environment = Environment.TEST // Or Environment.Prod for production  
    ),  
    onPaymentResult = { paymentResult, khalti ->  
        // Your implementation  
    },  
    onMessage = { payload, khalti ->  
        // Your implementation  
    },  
    onReturn = { khalti ->  
        // Your implementation  
    } 
)
```

##### Method 2: Without `onReturn`

Initialize the `Khalti` Object

```kotlin
val khalti = Khalti.init(  
    LocalContext.current,  
    KhaltiPayConfig(  
        publicKey = "<public_key>",  
        pidx = "<pidx>",  
        environment = Environment.TEST // Or Environment.Prod for production  
    ),  
    onPaymentResult = { paymentResult, khalti ->  
        // Your implementation  
    },  
    onMessage = { payload, khalti ->  
        // Your implementation  
    } 
)
```

Use `khalti.open()`, `khalti.verify()`, `khalti.close()` wherever appropriate.

Check out the source for <a target="_blank" href="https://github.com/khalti/checkout-sdk-android">Khalti checkout on Github</a>.
#### <a href="https://github.com/khalti/checkout-sdk-android/blob/master/CHANGELOG.md" target="_blank">Changelog</a>