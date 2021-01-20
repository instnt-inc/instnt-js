# Instnt JavaScript Standard Form
This documentation covers the basics of Instnt's vanilla JavaScript implementation. For a more detailed look at Instnt's platform functionality, visit [Instnt's documentation library](https://support.instnt.org/hc/en-us)

# Rendering a Standard Signup Form with Instnt.js
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
This rendered form will be connected to Instnt's Sandbox environment, ready for pre-provisioned customers to be passed through for validation testing.
