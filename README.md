# Instnt JavaScript Workflow
This documentation covers the basics of Instnt's vanilla JavaScript implementation. For a more detailed look at Instnt's platform functionality, visit [Instnt's documentation library](https://support.instnt.org/hc/en-us/articles/360055345112-Integration-Overview)

### Table of Contents
- [Rendering a Standard Signup Workflow with Instnt.js](https://github.com/instnt-inc/instnt-js#rendering-a-standard-signup-form-with-instntjs)
- [Building and Submitting Custom Workflows using JavaScript SDK](https://github.com/instnt-inc/instnt-js#building-and-submitting-custom-forms-using-javascript-sdk)
- [Submit Workflow to Instnt Using the JavaScript Helper Function](https://github.com/instnt-inc/instnt-js#submit-form-to-instnt-using-the-javascript-helper-function)
- [Submit Workflow to Instnt via API](https://github.com/instnt-inc/instnt-js#submit-form-to-instnt-using-the-javascript-helper-function)

## Rendering a Standard Signup Workflow with Instnt.js
Instnt's vanilla JavaScript implementation renders a standard workflow that includes the following fields:
* Email Address
* First Name
* Surname
* Mobile Number
* State
* Street Address
* Zip code
* City
* Country
* Submit My Workflow Button

When ready, enter the [following code](https://github.com/instnt-inc/instnt-js/blob/60967324d1a67b38cd3a1fca032d4431b2ec0238/examples/standard-form/index.html#L1) onto the webpage containing your signup workflow:

```jsx

<html>
  <head></head>
  <body>
    <h1>Instnt Standard SignUp Workflow</h1>
    <div class="insnt-form-container" style="text-align: center">
      <div id="instnt-form-generator" class="container"></div>
      <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
      <script>
        const formId = 'v879876100000';
        const serviceUrl = 'https://sandbox-api.instnt.org';
        $(document).ready(function () {
          $.get(serviceUrl + '/public/getformcodes/' + formId, function (data, status) {
            $('#instnt-form-generator').html(data.html);
          });
        });
      </script>
    </div>
  </body>
</html>
```
In the above snippet you may create your own workflow ID or use the one above as a quick example. The data is passed into the sandbox environment ready for pre-provisioned customers to be passed through for validation testing; this may be changed by editing the const serviceUrl field and entering api.instnt.org to send it to the live environment.

## Building and Submitting Custom Workflows using JavaScript SDK

1. Insert the prior Instnt Code snippet with the `hide_form_fields` query string parameter set to `true`

```jsx

    <div class="insnt-form-container" style="text-align: center">
      <div id="instnt-form-generator" class="container"></div>
      <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
      <script>
        const formId = 'v879876100000';
        const serviceUrl = 'https://sandbox-api.instnt.org';
        $(document).ready(function () {
          $.get(serviceUrl + '/public/getformcodes/' + formId + '?hide_form_fields=true', function (data, status) {
            $('#instnt-form-generator').html(data.html);
          });
        });
      </script>
    </div>
```

2. Collect data from the user.

3. Upon custom workflow submission generate an `instnt_token` using the `window.instnt.getToken()` function.

4. Submit both the data and the `instnt_token` to Instnt using either a helper function or API call as shown below.

## Submit Workflow to Instnt Using the JavaScript Helper Function

Instnt provides a helper function to submit the form data window.instnt.submitCustomForm(data: any, redirect: Boolean = true)

* `data` - JavaScript object with key:value user entred data

* `redirect` - if false, no automatic redirect will occur upon receiving a response

Developers can also set window.instnt.onResponse to an event handler/callback that will be invoked after the response is received from Instnt. The handler will be passed to parameters: onResponse(error, data). `error` will contain error information if one occurred.


```jsx
const submitMyForm = () -> {
  window.instnt.submitCustomForm(data);
};
```



## Submit Workflow to Instnt via API

This submission method can be utilized for submitting data from either the front end or the backend by collecting data from the applicant, using Instnt SDK's functionality `window.instnt.getToken()` to retrieve an `instnt_token` that encapsulates Instnt system data as well as the applicant's device and behavioral information, and then submitting all of the data to Instnt.

When submitting this data from the backend, the `instnt_token` should be collected on the web app and transferred to the backend.

### Sandbox

```jsx
  const submitFormViaAPI = () => {
    // 'data' contains user data fields
    // Get system information using window.instnt.getToken() and send it along with data using 'instnt_token' key
    const token = window.instnt.getToken();
    const dataWithToken = { ...data, instnt_token: token };

    fetch('https://sandbox-api.instnt.org/public/submitformdata/v1.0'), {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(dataWithToken),
    })
      .then((response) => response.json())
      .then((data) => {
        console.log(data);
      });
  };
```
