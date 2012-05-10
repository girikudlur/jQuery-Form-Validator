The goal of this project is to create a feature rich jQuery plugin used for validating form data and that 
keeps the HTML code clean from javascript logic. Even though the plugin has **a wide range of validation functions**
it's going to be suitable for **mobile websites**. This is achieved by grouping together functions in "modules", making
it possible for the programmer to load **only those function that's needed** to validate a particular form.



*Usage example*

```html
<html>
<head>
  <script src="js/jquery.min.js"></script>
  <script src="js/formvalidator/jquery.formvalidator.min.js"></script>
  <script>
  	$.formUtils.loadModules('date,security');
  </script>
</head>
<form action="" onsubmit="return $(this).validate()">
  <p>
    Name (4 characters minimum):
    <input name="user" data-validation="validate_min_length length4" />
  </p>
  <p>
    Birthdate (yyyy-mm-dd):
    <input name="birth" data-validation="validate_birthdate" />
  </p>
  <p>
    Website:
    <input name="website" data-validation="validate_url" />
  </p>
  <p>
    <input type="submit" />
  </p>
</form>
...
```

## Features
### Default validators
 * **validate_url**
 * **validate_email**
 * **validate_domain** — *domain.com*
 * **validate_phone** — *atleast 7 digits only one hyphen and plus allowed*
 * **validate_float**
 * **validate_int**
 * **validate_date** — *yyyy-mm-dd (format can be customized, more information below)*
 * **validate_length** — *Validate that input length is in given range (length3-20)*
 * **required** — *no validation except that a value has to be given*
 * **validate_custom** - *Validate value against regexp (validate_custom regexp/^[a-z]{2} [0-9]{2}$/)

### Module: security
 * **validate_spamcheck**
 * **validate_confirmation**
 * **validate_strength** — *Validate the strength of a password (validate_strength strength3)*
 * **validate_backend** — *Validate value of input on backend*

### Module: date
 * **validate_time** — *hh:mm*
 * **validate_birthdate** — *yyyy-mm-dd, not allowing dates in the future or dates that's older than 122 years (format can be customized, more information below)*

### Module: sweden
 * **validate_swemob** — *validate that the value is a swedish mobile telephone number*
 * **validate_swesec** — *validate swedish social security number*

### Module: ukvat
 * **validate_ukvatnumber**

### Misc (part of core)
 * Show help information automatically when input is focused
 * Validate given values immediately when input is blurred.


## Writing a custom validator
You can use the function `$.formUtils.addValidator()` to add your own validation function. Here's an example of a validator
that checks if the input contains an even number

```html
<html>
<head>
  <script src="js/jquery.min.js"></script>
  <script src="js/formvalidator/jquery.formvalidator.min.js"></script>
  <script>
    $.formUtils.addValidator({
        name : 'validate_even',
        validate : function(value, $el, config, language, $form) {
            return parseInt(value, 10) % 2 === 0;
        },
        errorMessage : 'You have to give a even number',
        errorMessageKey: 'badEvenNumber'
    });
  </script>
</head>
<body>
 ...
 <form action="" method="post" onsubmit="return $(this).validate();">
 <p>
   <input type="text" data-validation="validate_even" />
 ...
```

### Required properties passed into $.formUtils.addValidator

*name* - The name of the validator, which is used in the validation attribute of the input element.

*validate* - callback function that validates the input. 

*errorMessageKey* - Name of language property that is used in case the value of the input is invalid 

*errorMessage* - An alternative error message that is used if errorMessageKey is left with an empty value


The validation function takes these five arguments:
- value — the value of the input thats being validated
- $el — jQuery object referring to the input element being validated
- config — Object containing the configuration of this form validation
- language — Object with error dialogs
- $form — jQuery object referring to the form element being validated

## Creating a custom module

A module is simply a script file containing one or more calls to `$.formUtils.addValidator()`. 



## Validate inputs on blur
It is now possible to show that the value of an input is incorrect immediately when the input gets blurred.

```html
<form action="" onsubmit="return $(this).validate();" id="my_form">
  <p>
	  <strong>Website:</strong>
	  <input type="text" name="website" data-validation="validate_url" />
	</p>
	...
</form>

<script>
	$('#my_form').validateOnBlur();
</script>
```

## Show help information
Since version 1.1 it is possible to display help information for each input. The information will fade in when input is focused and fade out when input is blurred.

```html
<form action="" onsubmit="return $(this).validate();" id="my_form">
	<p>
	  <strong>Why not:</strong>
	  <textarea name="why" data-help="Please give us some more information" data-validation="required"></textarea>
	</p>
	...
</form>

<script>
	$('#my_form').showHelpOnFocus();
</script>
```

## Fully customizable
```javascript
var myConf = {
	// Name of element attribute holding the validation rules (default is data-validation)
	validationRuleAttribute : 'class',

	// Names of inputs not to be validated even though the element attribute containing
	// the validation rules tells us to
	ignore : ['som-name', 'other-name'],

	// Class that will be put on elements which value is invalid (default is 'error')
	errorElementClass : 'error',

	// Border color of elements which value is invalid, empty string to leave border
	// color as it is
	borderColorOnError : '#FFF',

	// Class of div container showing error messages (defualt is 'error_message')
	errorMessageClass : 'error_message',

	// Position of error messages. Set the value to "top" if you want the error messages
	// to be displayed in the top of the form. Otherwise you can set the value to
	// "element", each error message will then be displayed beside the input field that
	// it is refering to (default is 'top')
	errorMessagePosition : 'element',

	// Date format used when validating dates and birthdate. (default is yyyy-mm-dd)
	dateFormat : 'dd/mm/yyyy',

	// Window automatically scrolls to the top of the form when submitted data is
	// invalid (default is true)
	scrollToTopOnError : false
};

var myLang = {
	errorTitle : 'Något gick fel',
	requiredFields : 'Du fyllde inte i alla fält markerade med *'
};

$('#my_form')
  .showHelpOnFocus()
  .validateOnBlur(myLang, myConf)
  .submit(function() {
    return $(this).validate(myLang, myConf);
  });
```

## Localization
All error dialogs can be overwritten by passing an object into the validation function.

```javascript
var jQueryFormLang = {
    errorTitle : 'Form submission failed!',
    requiredFields : 'You have not answered all required fields',
    badTime : 'You have not given a correct time',
    badEmail : 'You have not given a correct e-mail address',
    badTelephone : 'You have not given a correct phone number',
    badSecurityAnswer : 'You have not given a correct answer to the security question',
    badDate : 'You have not given a correct date',
    tooLongStart : 'You have given an answer longer than ',
    tooLongEnd : ' characters',
    tooShortStart : 'You have given an answer shorter than ',
    tooShortEnd : ' characters',
    badLength : 'You have to give an answer between ',
    notConfirmed : 'Values could not be confirmed',
    badDomain : 'Incorrect domain value',
    badUrl : 'Incorrect url value',
    badFloat : 'Incorrect float value',
    badCustomVal : 'You gave an incorrect answer',
    badInt : 'Incorrect integer value',
    badSecurityNumber : 'Your social security number was incorrect',
    badUKVatAnswer : 'Incorrect UK VAT Number'
};
```

```html
<html>
<head>
    <script src="scripts/jquery.formvalidator.min.js"></script>
    <script src="scripts/locale.en.js"></script>
    ...
<head>
<body>
    ...
    <form action="script.php" onsubmit="return $(this).validate(jQueryFormLang);">
    ...
```


## Simple captcha example
```php
<?php
session_start();
if( isset($_POST['captcha']) && isset($_SESSION['captcha'])) {
  if($_POST['captcha'] != ($_SESSION['captcha'][0]+$_SESSION['captcha'][1]))
    die('Invalid captcha answer');  // client does not have javascript enabled
}

$_SESSION['captcha'] = array( mt_rand(0,9), mt_rand(1, 9) );

?>
<html>
....
<form action="" onsubmit="return $(this).validate();">
  <p>
    What is the sum of <?=$_SESSION['captcha'][0]?> + <?=$_SESSION['captcha'][1]?>? (security question)
    <input name="captcha" data-validation="validate_spamcheck captcha<?=( $_SESSION['capthca'][0] + $_SESSION['captcha'][1] )?>"
  </p>
  <p><input type="submit" /></p>
</form>
...
</html>
```

## Input length restriction
```html
<p>
  History (<span id="maxlength">50</span> characters left)
  <textarea rows="3" id="area"></textarea>
</p>
<script type="text/javascript">
  $('#area').restrictLength($('#maxlength'));
</script>
```

## Password confirmation example
```html
  <p>Password: <input type="password" name="pass" data-validation="validate_confirmation" /></p>
  <p>Confirm password: <input type="password" name="pass_confirmation" /></p>
```

## Contributors
[Joel Sutherland](https://github.com/robamaton) (contributor)<br />
[Steve Wasiura](https://github.com/stevewasiura) (contributor)<br />
[Matt Clements](https://github.com/mattclements) (contributor)<br />
[dfcplc](https://github.com/dfcplc) (contributor)<br />
[Darren Mason](http://www.mypocket-technologies.com) (Password strength meter)<br />
[Scott Gonzales](http://projects.scottsplayground.com/iri/) (URL regexp)