# Summary

Guards is a javascript validation plugin for jQuery.  It features
simple validation via chained methods, similar to how jQuery works.

# Example Usage

## Override defaults

All defaults can be overriden, but must be done before any guards have
been defined.  Below is an example of overriding all the important
defaults.

    $.guards.defaults.messageClass = "error";   // Defaults to "error-message"
    $.guards.defaults.grouped = true;           // Defaults to false
    $.guards.defaults.guard = "oneRequired";    // Defaults to "required"
    $.guards.defaults.invalidClass = "invalid"; // Defaults to "invalid-field"
    $.guards.defaults.tag = "div";              // Defaults to "span"

## Defining guards using built-in guards

A few guards come built in.  These guards can be accessed by their
string name.  They are located at $.guards.defaults.guards, with an
associated message at $.guards.defaults.messages.  Below is a list of
all the existing guards with a short description.

* **allow**: Only values found in the given list are considered valid.
  Anything else triggers a failure.

* **disallow**: If the value matches one of the given list, the value
  is considered invalid.

* **email**: Empty fields are valid, otherwise it must match a regex
  to ensure it looks like a valid email address.

* **float**: This accepts an optional argument specifying a min value,
  max value, or both min and max.  Empty fields are valid.  A number
  must be specified that is in range of the min and/or max if they are
  specified.

* **int**: This accepts an optional argument specifying a min value,
  max value, or both min and max.  Empty fields are valid.  A number
  must be specified that is in range of the min and/or max if they are
  specified.

* **moneyUS**: Empty fields are valid, otherwise it must match a regex
    to ensure it looks like a valid US currency value (such as 15.22,
    $1,233, $.22, -$23, etc).  This accepts an option argument that
    could specify a min or max value, of which the dollar amount is
    compared against.

* **oneRequired**: This should be used with grouped = true.  This
  specifies that at least 1 value exists (ie, is not null, undefined,
  or just whitespace).

* **phoneUS**: Empty fields are valid, otherwise it must match a regex
  to ensure it looks like a valid US phone number.

* **required**: Every field is required to exist.  This differs from
  oneRequired because oneRequired will pass when a single element has
  a value when grouped is true, while required will fail (as it
  requires every field to have a value).

* **string**: This requires an argument specifying a min value, max
  value, or both min and max.  The length of the string is validated
  with respect to the given min and/or max.

Examples:

    $.guard(".scheme").using("allow", ["http", "https", "ftp", "ftps"]);
    $.guard(".avoid-keywords").using("disallow", ["class", "def", "module"]);
    $.guard(".email").using("email");
    $.guard(".5plus").using("int", { min: 5 });
    $.guard(".noMoreThan10").using("int", { max: 10 });
    $.guard(".1to10").using("int", { min: 1, max: 10 });
    $.guard(".number").using("int");
    $.guard(".float").using("float");
    $.guard(".thirdToHalf").using("float", { min: (1.0 / 3.0), max: 0.5 });
    $.guard(".at-least-one").grouped().using("oneRequired");
    $.guard(".phone-number").using("phoneUS");
    $.guard(".required").using("required");
    $.guard(".long-password").using("string", { min: 10 });
    $.guard(".short-title").using("string", { max: 32 });
    $.guard(".title").using("string", { min: 1, max: 32 });

## Defining custom guards with functions

Besides using built in guards, you may specify functions as guards.
The functions should accept the value of the input and the element
being guarded.  If grouped is enabled on the guard, then instead it is
an array of all values and all elements (for those inputs that matched
the guarded selector).  The function should return true if the
value(s) passed the guard, or false if they failed.

Also note that custom guards should specify a message, as the default
message of $.guards.defaults.messages.undefined is used if none is
specified.  It should be specified after the call to using() though
(otherwise using() will override it to the undefined default
message).

Examples:

    $.guard(".avoid-keywords").using(function(value, element) {
        return $.inArray(value, ["bad", "invalid"]) == -1;
    }).message("Please don't use the keywords: bad, invalid.");

    $.guard(".require-special").grouped().using(function(values, elements) {
        return $.inArray("special", values) != -1;
    }).message("Please specify at least one 'special' value.");

## Overriding defaults on individual guards

If you want to change how a specific guard works, you can use the
various chainable setters to change the various defaults.

Examples:

    // Use the class 'error' on the message when there is an error
    $.guard(".required1").using("required").messageClass("error");

    // Group this guard
    $.guard(".required2").using("required").grouped();

    // Don't group this guard
    $.guard(".required3").using("required").grouped(false);

    // Mark invalid fields with the 'invalid' class
    $.guard(".required4").using("required").invalidClass("invalid");

    // Surround error messages with a div
    $.guard(".required5").using("required").tag("div");

## Guarding the form

Once you have specified your guards, you must block form submission if
the guard fails.  This can be done with the enableGuards function
applied to the forms you want to enable:

    $("#myForm").enableGuards();

Which is equivalent to:

    $("#myForm").submit(function() { return $(this).guard(); });

Or, if you want to invoke the guards manually, you can just call
guard() on the form directly and use the boolean result:

    var result = $("#myForm").guard();
    // Use result, which is true if the form is valid, and false otherwise.

# License

Guards is licensed under the [MIT license](http://github.com/on-site/Guards-Javascript-Validation/blob/master/MIT-LICENSE.txt)
