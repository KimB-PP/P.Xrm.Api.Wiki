# Get Entity

|Method|Paramaters|Returnes|
:-|:-|:-|
|Get()|**GetRequest** definition of what entity to get|**JsonElement**|
|Get<T>()|**GetRequest** definition of what entity to get|Type **T**|


The Get entity-methods are methods to retrieve <u>one</u> entity from crm (but you can retrieve that one entity with some related entiy. [See Get with Expand](#get-with-expand---example))


### Get - example
```csharp
var columns = new Select("name", "accountnumber");
var jAccount = await Crm.Get("account", "<guid>", columns);

//The above would yield a JObject, but you can have it return any type.
//Let's say we have the following Customer model:
public class Customer
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("accountnumber")]
    public string ClientRef { get; set; }
}

//You use the exact same method, just specify <T>
//Also, in this example the name and accountnumber is passed as
//params directly - just to show that it's possible.
var account = await Crm.Get<Customer>("account", "<some_guid>", "name", "accountnumber");
```

### Get with Expand - example
You can use expand to have your result include attributes from any belonging lookup.

```csharp
//Get(); 
//For instance say you want to retrieve a contact, and also the parent account of that contact:

var select = new Select("firstname", "lastname");
select.AddExpand("parentcustomerid_account", "name", "accountnumber");

var jContact = await Crm.Get("contact", "<guid>", select);

//The record guid is included by default, also for expands
//jContact:
//{  
//  "firstname": "Peter",
//  "lastname": "Skogsrud",
//  "contactid": "<guid>",
//  "parentcustomerid_account": {
//    "name": "Olabil AS",
//    "accountnumber": 654321,
//    "accountid": "<guid>"
//  }
//}


/

```

## GetValue()
A fast method when you just want to receive a specific value on some record.

```csharp
//Retrieve the emailaddress for contact with guid = <guid>
var email = await Crm.GetValue("contact", "<guid>", "emailaddress1");

//Get value via lookup:
//Retrieve the name of parent account for contact with guid = <guid>
var customerName = await Crm.GetValue("contact", "<guid>", "name", "parentcustomerid_account");

//By default GetValue() return string
//You can specify <T> if you want another DataType returned, i.e.:
```
