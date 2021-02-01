# Instnt JavaScript Form
This documentation covers the basics of Instnt's vanilla JavaScript implementation. For a more detailed look at Instnt's platform functionality, visit [Instnt's documentation library](https://support.instnt.org/hc/en-us/articles/360055345112-Integration-Overview)

## Rendering a Standard Signup Form with Instnt.js
Instnt's vanilla JavaScript implementation renders a standard form that includes the following fields:
* Email Address
* First Name
* Surname
* Mobile Number
* State
* Street Address
* Zip code
* City
* Country
* Submit My Form Button

When ready, enter the [following code](https://github.com/instnt-inc/instnt-js/blob/60967324d1a67b38cd3a1fca032d4431b2ec0238/examples/standard-form/index.html#L1) onto the webpage containing your signup workflow:

```jsx
  
<html>
  <head></head>
  <body>
    <h1>Instnt Standard SignUp Form</h1>
    <div class="insnt-form-container" style="text-align: center">
      <div id="instnt-form-generator" class="container"></div>
      <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
      <script>
        const formId = 'v879876100000';
        const serviceUrl = 'https://sandbox-api.instnt.org';
        $(document).ready(function () {
          $.get(serviceUrl + '/getformcodes/' + formId, function (data, status) {
            $('#instnt-form-generator').html(data.html);
          });
        });
      </script>
    </div>
  </body>
</html>
```
In the above snippet you may create your own form ID or use the one above as a quick example. The data is passed into the sandbox environment ready for pre-provisioned customers to be passed through for validation testing; this may be changed by editing the const serviceUrl field and entering api.instnt.org to send it to the live environment. 

## Building and Submitting Custom Forms using JavaScript SDK

1. Insert Instnt Code snippet with `hide_form_fields` query string parameter set to `true`

```jsx
  
    <div class="insnt-form-container" style="text-align: center">
      <div id="instnt-form-generator" class="container"></div>
      <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
      <script>
        const formId = 'v879876100000';
        const serviceUrl = 'https://sandbox-api.instnt.org';
        $(document).ready(function () {
          $.get(serviceUrl + '/getformcodes/' + formId + '?hide_form_fields=true', function (data, status) {
            $('#instnt-form-generator').html(data.html);
          });
        });
      </script>
    </div>
```

2. Collect data from the user

3. Upon custom form submission generate `instnt_token` using `window.instnt.getToken()` function

4. Submit data and Instnt Token to Instnt using either a helper function or API call

### Submit Form to Instnt using JavaScript helper function

```jsx
const submitMyForm = () -> {
  window.instnt.submitCustomForm(data);
};
```

### Submit Form to Instnt via API

This method can be utilized for submitting data from the front end OR from the backend. 

The general approach is to collect data from the applicant, then using Instnt SDK's method `window.instnt.getToken()` retrieve `instnt_token` that encapsulates Instnt system data along with applicant's device and behavioral information and then submit that all to Instnt.

In case of submission from the backend, `instnt_token` should be collected on the web app and transfered to the backend.

```jsx
  const submitFormViaAPI = () => {
    // 'data' contains user data fields
    // We need to get system information using window.instnt.getToken() and send it along with data using 'instnt_token' key
    const token = window.instnt.getToken();
    const dataWithToken = { ...data, instnt_token: token };

    fetch('https://sandbox2-api.instnt.org/public/submitformdata/v1.0', {
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
