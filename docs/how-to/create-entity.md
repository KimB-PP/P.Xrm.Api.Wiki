# Create Entity
There are two ways of Creating records with the function **Api.Create()** fuction.



|Method|Paramaters|Returnes|
:-|:-|:-|
|Create()|**string** entityName, **Dictionary<string, object>** content|**Guid** - The guid of the created object|
|Create<T>()|**string** entityName, **Dictionary<string, object>** content, **Select** select| Type **T**|

## Create()
Accepts the entityname as string (see [EntitySetNaming](#entitysetnaming))
The function returns the Guid of the created entity.

```csharp
var CRM = new Api(configuraiton);

var content = new Dictionary<string, object>
{
  {"lastname","Finn"},
  {"firstname", "Per Arne"},
};

var result = CRM.Create("contact",content).Result;

/**
 * Output of result would be a the Guid of the created entity:
 * gacba506-s2fe-4615-872d-88d147286c4d
 */
```

### Create&lt;T&gt;()
Takes entityname and a **Select** object to define what fields from the created entity should be returned (after it's created).
The function returns as an object containing the chosen field and values in the Select - object.
Define object to be returned as **T**. For example Create<JsonElement>().


```csharp
var content = new Dictionary<string, object>
{
  {"lastname","Finn"},
  {"firstname", "Elthon Garde"},
};

var select = new Select("fullname");

var result = api.Create<JsonElement>("contact", content,select).Result;

foreach (var item in result.EnumerateObject())
   {
      Console.WriteLine($"{item.Name}, {item.Value}");
   }

//Prints
/**
 * @odata.context, https://pplab13-kimb.crm4.dynamics.com/api/data/v9.1/$metadata#contacts(fullname)/$entity 
 * @odata.etag, W/"29735279"
 * fullname, Elthon Garde Finn
 * contactid, beedf6bd-a808-eb11-a813-000d3ab4653d
 */
```

