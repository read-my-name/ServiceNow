1. scheduled script
```
// Retrieve the value of the x_58872_needit.autoCloseOverdue Application Property
  var howLongToClose = gs.getProperty('x_58872_needit.autoCloseOverdue');
    
  // Query the database for NeedIt Task records with Due date field
  // values older the number of days set in the x_58872_needit.autoCloseOverdue
  // Application Property.  Only return NeedIt Task records have a State value of
  // Open or Work in progress.  Close the NeedIt Tasks and update
  // the NeedIt Task Work notes.
  var overdueNITasks = new GlideRecord('x_58872_needit_needit_task');
  overdueNITasks.addQuery('due_date','<=',gs.daysAgo(howLongToClose));
  overdueNITasks.addQuery('state','<',3);
    
  overdueNITasks.query();
  // Write a log message for each long overdue NeedIt Task Record.  
  // Add a note to the Work notes field.  Change the State to Closed Incomplete.
  // Update the NeedIt Task record. 
  while(overdueNITasks.next()){
    gs.info("Auto close NeedIt Task record = " + overdueNITasks.number);
    overdueNITasks.work_notes = "Record closed because it was too far past the Due date.";
    overdueNITasks.state = 4;
    overdueNITasks.update();
  }
```