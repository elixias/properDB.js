**What is properDB.js?**

properDB.js is a JS library for managing HTML5 webSQL databases. It wraps the native storage calls into an easy to use properDB object.

See README.md for all functions and usage.
Supports raw SQL commands, adding updating by arguments or objects, and querying with results using callback functions.

See https://github.com/elixias/properDB.js/blob/master/index.html for actual implementation.

**Include properDB.js into your .html page.**
```
	<script type="text/javascript" src="properDB.js"></script>
```
**Initialize a new Database**
```
        var myDB = new properDB(); //create a new DB with default settings
        //OR
        myDB = new properDB("DB_Name",1,"DB_Display Name",50000); //create a new DB with your settings
```
**Using Database.execute()**
```
        //Database execute(), if you want to manually execute commands without using other properDB.js inbuilt functions
        myDB.execute("DROP TABLE IF EXISTS commandtable");
        myDB.execute("CREATE TABLE IF NOT EXISTS commandtable (P_ID INTEGER PRIMARY KEY, DATA VARCHAR(10))");
        myDB.execute("INSERT INTO commandtable VALUES(null,'This is test data from database mgr.')");
        myDB.execute("SELECT * FROM commandtable"); //will resolve with default callback (prints results using console.log)
                                                    //you can overwrite the callback <database>.getExecResults but not recommended
        var callback = function(result){ //specify your own callback
            console.log("This is the default callback behavior! "+JSON.stringify(result));
        }
        myDB.execute("SELECT * FROM commandtable", callback); //specify a callback function to catch results
```
**Adding a Table**
```
        //adding a table to the DB, primary keys are auto created for you
        //addTable(<tablename>, "<col1> [mySQL options], <col2> [mySQL options],...")
        myDB.addTable("testtable","T_COL1 VARCHAR(100), T_COL2 VARCHAR(100)");
```
```
        //create, delete or recreate the table
        
        var myTable = myDB["testtable"]; 
        
        myTable.drop();     //you will lose all entries in table
        myTable.create();   //if you want to create after dropping
        myTable.recreate(); //calls drop() then create()
```
**Adding records to table**

_As arguments_
```
        //Add new entries to tables. you can cascade function calls.
        myDB["testtable"].add("entry 1","lorem ipsum")
        	.add("entry 2","dorsa latin")
        	.add("entry 3","third entry");
```        	
_As object_  
```
        //add new entries as an object, you can cascade function calls (not shown)
        var new_entry = {"T_COL1":"entry X","T_COL2":"data X"};
        myDB["testtable"].add(new_entry);
```        
**Selecting records**

_Selecting records with default behavior (prints to console.log)_
```
	//selecting entries
        myDB["testtable"].get();  //gets all records
        myDB["testtable"].get(3); //gets record with id=3
        myDB["testtable"].get("T_COL1='entry 2'"); //gets record where T_COL='entry 2'

        //gets record with id=3, specify callback function to handle the result (returned as an Array Object)
        myDB["testtable"].get(3, function(results){ 
            console.log("Final results:"+JSON.stringify(results));   
        });
```
**Deleting records**
```
        //deleting entries
        myDB["testtable"].delete(1); //delete entry with ID 1
        myDB["testtable"].delete("T_COL1='entry 3'"); //DELETE entry WHERE T_DATA='entry 3'
```
**Updating records** 

_Using arguments_       
```
        //updating entries
        myDB["testtable"].update(2,"entry 2 updated","lorem ipsum lorem ipsum"); //update ID 2
```
_Using an object_
```
        //update ID 2 using object, uses P_ID as the WHERE clause
        var obj={"P_ID":2,"T_COL1":"entry 2 changed","T_COL2":"entry 2 data changed"} ; 
        myDB["testtable"].update(obj);
```
**Example - Modifying existing data entry using get() and update()**		
```
        // 1) Set callbacks first

        var myCB2 = function(results){ 
            console.log("Final results:"+JSON.stringify(results));   
        }

        var myCB = function(results){
            var obj = results[0];               //take the first result entry
            obj["T_COL1"] = "WE CHANGED THIS!"; //change variable this way
            obj.T_COL2 = "And this too.";       //or this way
            myDB["testtable"].update(obj);
            myDB["testtable"].get(4, myCB2);    //display result using myCB2 callback
        }

        // 2) Get entry ID 4 and display result using myCB callback
        myDB["testtable"].get(4, myCB);
```
