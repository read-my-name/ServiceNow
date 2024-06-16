Create a business rule
1. execute the rule when a record is inserted or updated

```
// rightnow stores the current time
  var rightnow = new GlideDateTime();
  // Create a GlideDateTime object for the When needed date
  var whenNeeded = new GlideDateTime(current.u_when_needed);
  
  // If the When needed date is before rightnow, do not write the record to the database
  // Output an error message to the screen
  if(whenNeeded.before(rightnow)){
    gs.addErrorMessage("When needed date cannot be in the past.  Your request has not been saved to the database.");
    current.setAbortAction(true);
  }
```

2. Script debugger
```
current.short_description = "This text set by the Debugging Business Rules business rule.";
var myNum = current.state;
  
// The function in this try/catch is not defined
try{
  thisFunctionDoesNotExist();
}
catch(err){
  gs.error("NeedIt App: a JavaScript runtime error occurred - " + err);
}

// This function is not defined and is not part of a try/catch
   thisFunctionAlsoDoesNotExist();

// getNum and setNum demonstrate JavaScript Closure
var x = 7;

function numFunc(){
  var x = 10;
  return{
    getNum: function(){
      return x;
    },
    setNum: function(newNum){
      x = newNum;
    }
  };
}
var callFunc = numFunc();
callFunc.getNum();
callFunc.setNum(2);
callFunc.getNum();
```

3. Validate email format
```
function validateEmailAddress(emailStr){
    // Use JavaScript coercion to guarantee emailStr is a string
    emailStr = emailStr + '';
    // Compare emailStr against the allowed syntax as specified in the regular expression
    // If emailStr has allowed syntax, return true, else return false
    if(emailStr.match(/^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/)){
      return true;
    }
    else {
     return false;
    }
  }
```

4. Extend GlideAjax
```
var GetEmailAddress = Class.create();
// Extend the global.AbstractAjaxProcessor class
GetEmailAddress.prototype = Object.extendsObject(global.AbstractAjaxProcessor,{
  // Define the getEmail function.  
  // Create a GlideRecord for the User table.
  // Use the sysparm_userID passed from the client side to retrieve a record from the User table.
  // Return the email address for the requested record
  getEmail: function() {
    var userRecord = new GlideRecord("sys_user");
    userRecord.get(this.getParameter('sysparm_userID'));
    return userRecord.email + '';
  },
  type: 'GetEmailAddress'
});
```

5. Populate email field
```
function onChange(control, oldValue, newValue, isLoading, isTemplate) {

  // Modified the if to return if the newValue == oldValue to avoid
  // unecessary trips to the server
  if (isLoading && !g_form.isNewRecord() || newValue === '' || newValue == oldValue) {
      return;
    }

  // Instantiate the GetEmailAddress Script Include 
  var getEmailAddr = new GlideAjax('GetEmailAddress');
  // Specify the getEmail method
  getEmailAddr.addParam('sysparm_name','getEmail');
  // Pass the Requested for sys_id
  getEmailAddr.addParam('sysparm_userID', g_form.getValue('u_requested_for'));
  // Send the request to the server
  getEmailAddr.getXML(populateEmailField);


  // When the response is back from the server
  function populateEmailField(response){
    // Extract the email address from the response, clear any value from the email field, 
    // set new value in the email field
    var emailFromScriptInclude = response.responseXML.documentElement.getAttribute("answer");
    g_form.clearValue('u_requested_for_email');
    g_form.setValue('u_requested_for_email',emailFromScriptInclude);
  }
}
```
