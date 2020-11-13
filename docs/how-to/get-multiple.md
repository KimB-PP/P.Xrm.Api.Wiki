# Queries | Get Multiple Entities
### GetList() - example
|Method|Paramaters|Returnes|
:-|:-|:-|
|Get()|**GetRequest** definition of what entity to get|**JsonElement**|
|Get<T>()|**GetRequest** definition of what entity to get|Type **T**|

**RetrieveMultiple**
- Returns a list of records
- Takes a query as parameter
- Capped at max 5000 records per request


```csharp
//Query 1:
//Structured approach, using query.Filter:
var query1 = new Query("account");
query1.Select = new Select("name", "accountnumber");
query1.Filter
    .Where("statecode", Operator.Equal, 0)
    .And("name", Operator.EndsWith, "Inc.");


//Query 2:
//For a more "free" approach, use query.CustomFilter.
//CustomFilter is just a string. In this example we
//build a string by inputting conditions
var query2 = new Query("account");
query2.Select = new Select("name", "accountnumber");
query2.CustomFilter = $@"
        {new Condition("statecode", Operator.Equal, 0)}
    and {new Condition("name", Operator.EndsWith, "Inc.")}";


//Query 3:
//CustomFilter with just vanilla filter query input:
var query3 = new Query("account");
query3.Select = new Select("name", "accountnumber");
query3.CustomFilter = "statecode eq 0 and endswith(name,'Inc.')";


//See the produced query by using query.toString()
//As you will see, all above exmaples produce the exact same request
//query1.ToString : "accounts?$select=name,accountnumber&$filter=statecode eq 0 and endswith(name,'Inc.')"                   
//query2.ToString : "accounts?$select=name,accountnumber&$filter=statecode eq 0 and endswith(name,'Inc.')"
//query3.ToString : "accounts?$select=name,accountnumber&$filter=statecode eq 0 and endswith(name,'Inc.')"

//Get result as List<JObject>
var jResult = await Crm.GetList(query1);

//for typed response do:
var customers = await Crm.GetList<Customer>(query1);
```


#### Expand also works while retrieving multiple 
Read about expand in the [Get section](/How-To-|-Basics/Get-Entity#get-with-expand---example)
```csharp
/GetList()

var select = new Select("firstname", "lastname");
select.AddExpand("parentcustomerid_account", "name", "accountnumber");

var query = new Query("contact", 3); //Limit to max 3 returned records
query.Select = select; //Defined in example above
//query.Filter //No filter specified
var jResult = await Crm.GetList(query);

//jResult :
//[{
//    "firstname": "Peter",
//    "lastname": "Skogsrud",
//    "contactid": "<guid>",
//    "parentcustomerid_account": {
//      "name": "Olabil AS",
//      "accountnumber": "654321",
//      "accountid": "<guid>"
//    }
//  },
//{
//  "firstname": "Stig",
//  "lastname": "Aatangen",
//  "contactid": "<guid>",
//  "parentcustomerid_account": {
//    "name": "Skismøring AS",
//    "accountnumber": "123456",
//    "accountid": "<guid>"
//  }
//},
//{
//  "firstname": "Younes",
//  "lastname": "Hajji",
//  "contactid": "<guid>",
//  "parentcustomerid_account": null
//}]

//You can alway cast to <T> instead of using default JObject:
var contacts = await<Contact> Crm.GetList(query);

```


## GetNextList()
When using GetList(), there may be more entities that has a result from the query -> Because the maxpagesize header is set to a low value, or the request returnes a maximum of 5000 entites.

If you for example want to retrieve only two and two entities for a given query, set maxpagesize to 2, and the getGetList() to retrieve the entities. 
By calling getNextList(), the next two entites in the query will be returned.
If there exist NO more entitites, The result of getNextList() will be a normal GetList() with with the content of that query.

It is possible to get different size lists returned by changing the query maxpagesize value betweeen calling the GetNextList() fuction. 

```csharp
//Gets a list of some entities 

var query = new Query(...);
query.MaxPageSize = 2;
var result = Crm.GetList(query);
//Only gets 2 results

```

To check if a query has more entities to be returned, check if IQuery.NextLink has a value. 
Example:
```csharp
Query testQuery = new Query(....);

var result = Crm.GetList(testQuery);
if(testQuery.HasNext())
   var nextResult = Crm.GetNextList(testQuery);
```
If GetNextList() is called with an IQuery object that does not contain  a NextList, the defailt query will be executed (the first result will be returned again). 
