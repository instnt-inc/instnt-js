# Instnt JavaScript SDK
This documentation covers the basics of Instnt's vanilla JavaScript implementation. For a more detailed look at Instnt's platform functionality, visit [Instnt's documentation library](https://support.instnt.org/hc/en-us/articles/360055345112-Integration-Overview)

### Table of Contents
- [Prerequisites](#prerequisites)
- [Getting started](#getting-started)
    * [Initiating a transaction](#initiating-a-transaction)
- [Document verification](#document-verification)
    * [Document verification prerequisites](#document-verification-prerequisites) 
    * [Document verification functions](#document-verification-functions)
- [OTP verification](#otp-one-time-passcode)
  * [OTP workflow ](#otp-flow )
- [Submit data to Instnt using the JavaScript helper function](#submit-data-to-instnt-using-the-javascript-helper-function)
- [Instnt core library objects, functoins and events](#instnt-core-library-objects-functoins-and-events)
- [Assertion Response Payload](#assertion-response-payload)
- [Resource links](#resource-links)

# Prerequisites

* Sign in to your account on the Instnt Accept's dashboard and create a customer signup workflow that works for your company. Refer [Quick start guide](https://support.instnt.org/hc/en-us/articles/4408781136909) and [Developer guide, ](https://support.instnt.org/hc/en-us/articles/360055345112-Integration-Overview) for more information.

* The integration of SDK depends on your workflow; read the [Instnt Accept integration process,](https://support.instnt.org/hc/en-us/articles/4418538578701-Instnt-Accept-Integration-Process) to understand the functionalities provided by Instnt and how to integrate SDK with your application.

# Getting started

## Initiating a transaction

Instnt’s Javascript SDK (referenced as 'JS SDK' here after) is a thin wrapper over Instnt’s core JavaScript library described [here](https://support.instnt.org/hc/en-us/articles/4997119804301-Instnt-s-Core-JavaScript-Library-), which also serves as a backbone of Instnt’s other web SDKs like React and Angular SDKs. This approach helps Instnt to maintain a common codebase across its web SDKs, avoiding duplication and maintenance overhead. The core JS library also lives on instant’s cloud and only gets streamed to the user’s browser on demand when you initiate a transaction session with Instnt. Once that step is completed, your code can use Instnt’s JS SDK features.

This helps Instnt keep the Javascript SDK up to date without having you explicitly update the SDK version. The SDK version followed Instnt’s [API versioning](https://api.instnt.org/doc/swagger/) and the release cycles to that effect and like is kept backward compatible.  

## Setup

As described above, to access this SDK, follow these steps:

* Instnt JS SDK generates events through our its lifetime interacting with your application in the browser. In order to react to these events create an event handler function and set it to globally avilable (window bound) variable onInstntEvent

* Initialize a transaction session with Instnt by directly calling the corresponding Instnt API endpoint as described [here](https://support.instnt.org/hc/en-us/articles/4418538578701#heading-1).

* Grab the response of the API and add it to a designated DOM element in your application. This will initialize the SDK, start the transaction session, and make the instnt JS library object available. You can then access this globally available (window bound) instnt object and all its properties and functions. It is recommended to store this instnt object in your application context for further access.

**Sample code**

```jsx
// set a global event handler that instnt SDK will use for event callback
window.onInstntEvent = <<your event handler function>>

//call instnt begin transaction endpoint
const payload = {
  form_key: this.formId,
  hide_form_fields: this.hideFormFields,
  redirect: this.redirect,
}
if (this.serviceURL) {
  this.http.post(this.serviceURL + '/public/transactions', payload).subscribe({
    next: (res: any) => {
      const fragment = document.createRange().createContextualFragment(jQueryFragment);
      this.innerDivElement.nativeElement.appendChild(fragment);
      // optionally store instnt object to your application context
      this.service.instnt.next((window as any).instnt)
    },
    error: (err) => { console.error(err)}
  });
}
```


# Document verification
The document verification feature comes into the picture if you have enabled it during the workflow creation.

When this feature is enabled, the physical capture and verification of selfies and Government-issued identification documents such as Passports and Driver's Licenses are available.

Read the [Document Verification](https://support.instnt.org/hc/en-us/articles/4408781136909#heading-6) section of the Quickstart guide to understand better how to enable the feature.

## Document verification pre-requisites

* Web applications running on mobile-react can utilize Document Verification.
 
* iOS and Android mobile devices with Chrome or Safari browsers are supported for document verification.
 
* Desktop devices (laptops, PCs) are unsupported due to the poor quality of embedded cameras and lack of gyroscopes for orientation detection. While the feature will work on devices running Chrome or Safari browsers, the experience can vary.
 
* Do not include HTML tags with IDs containing the prefix' aid.' e.g. `<div id=’aidFooter'>` in your web app as this prefix is reserved to be used by the toolkit. 

* Document verification requires end-to-end communication over SSL to get permission to use the device camera.


## Document verification functions

### The first step is to capture the images. 

You can use this function multiple times to capture both the front and back sides of the license but front side is a must for driver's license. For Passport the first page is required.

The parameters to be passed are:
* documentType: The document type that is captured, for example: License, Passport.
* documentSide: The side of the document that is being captured, for example: front, back.
* captureMode: The capture mode of the document. The Options are Auto, Manual with Auto being default.
* autoUpload: If autoUpload is true, then the documents are uploaded automatically. This feature is turned on by default.

```jsx
if(instntObject) {
  instntObject.captureDocument("license","front","Auto",true);
}

```

For selfies, the parameters to be passed are :
* captureMode: The capture mode of the document. The Options are Auto, Manual with Auto being default.
* autoUpload: If autoUpload is true, then the documents are uploaded automatically. This feature is turned on by default. 

Selfie capture is optional but recommended.

```jsx
if(instntObject) {
  instntObject.CaptureSelfie("Auto",true);
}

```

Once you capture the document or selfie, you get a callback event depending on the success or error:

  * document.captured
  * document.uploaded (if autoUpload = true)
  * document.capture-cancelled
  
### Next, you upload the captured document.

The parameters to be passed are :
* attachment: The attachment to upload.
* documentSide: The side of the document that is being uploaded.
* isSelfie = false: If the document being uploaded is not a selfie, then it's false. 

```jsx
if(instntObject) {
  instntObject.uploadAttachment(attachment, "F",false);
}
```
When you upload a document or selfie, you get a callback event depending on the settings:

* document.uploaded
* document.error

3. After all images are uloaded, next you verify the document by submitting them to Instnt. 

>**NOTE:**
>You will not get the decision at this step, but you will get a response of confirmation. The final decision is provided when you submit all the user entered data.

```jsx
if(instntObject) {
  instntObject.verifyDocuments(documentType);
}

```

Once you invoke the function you get a callback event depending on the success or error:

* document.verification-initiated
* document.error

# OTP (One-Time Passcode)

OTP functionality can be enabled by logging in Instnt dashboard and enabling OTP in your workflow. Refer to the [OTP](https://support.instnt.org/hc/en-us/articles/4408781136909#heading-5) section of the Quickstart guide for more information.

## OTP flow

* User enters mobile number as part of the signup screen.
* Your app calls send OTP() SDK function and pass the mobile number.
* Instnt SDK calls Instnt API and returns the response upon successful OTP delivery.
* Your app shows the user a screen to enter the OTP code.
* User enters the OTP code which they received.
* Your app calls verify the OTP() SDK function to verify the OTP and pass mobile number and OTP code.
* Instnt SDK calls Instnt API and returns the response upon successful OTP verification

Instnt SDK provides two Javascript library functions to enable OTP.
1. Send OTP
```jsx
if(instntObject) {
  instntObject.sendOTP (mobileNumber);
}

```

Once you send the OTP to the user's mobile number that was passed, you get a callback event depending on the success or error: 
* otp.sent
* otp.error

2. Verify OTP

```jsx
if(instntObject) {
  instntObject.verifyOTP(mobileNumber, otpCode);
}

```
Once you verify the OTP to was entered by the user, you get a callback event depending on the successs or error:
* otp.verified
* otp.error

# Submit data to Instnt

To submit the form data use submitData() function of instnt object. The parameter is a JavaScript object with a key:value pair of user-entered data.

```jsx
if(instntObject) {
  instntObject.submitData(JSObject);
}
```

Once you submit the user entered data along with the documents, you get a callback event depending on the success or error:
* transaction.processed
* transaction.error

# Instnt's core library objects, functions, and events

>**NOTE:**
>Please refer to [Instnt's Core JavaScript Library ](https://support.instnt.org/hc/en-us/articles/4997119804301) for details regarding the Instnt's core Javascript library objects, functions, and events.

# Resource links 
- [Quick start guide](https://support.instnt.org/hc/en-us/articles/4408781136909)
- [Developer guide](https://support.instnt.org/hc/en-us/articles/360055345112-Integration-Overview)
- [Instnt API endpoints](https://swagger.instnt.org/)
- [Instnt support](https://support.instnt.org/hc/en-us)