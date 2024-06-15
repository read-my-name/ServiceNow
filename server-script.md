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


