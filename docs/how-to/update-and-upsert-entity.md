# Update and Upsert Entity

Updating or Upserting an Entity are done the same way, on different methods, CRM.Update() CRM.Upsert()


## Update
|Method|Paramaters|Returnes|
:-|:-|:-|
|Update()|**string** entityName, **string** id, **Dictionary<string, object>** content|**void**|
|Update<T>()|**string** entityName, **string** id, **Dictionary<string, object>** content, **Select** select| Type **T**|

### Update()
Accepts the entityname as string (see [EntitySetNaming](#entitysetnaming)). Id of entity to update, and entity properties to update. 

```csharp
var CRM = new Api(configuraiton);

var content = new Dictionary<string, object>
   {
      {"emailaddress1", "this@guy.com"}
   };

await crm.Update("contact", "beedf6bd-a808-eb11-a813-000d3ab4653d", content);

/**
 * The would result in the contact would be updated. If no contact is found, an exception would be thrown.
 */
```

### Update&lt;T&gt;()
Accepts the entityname as string (see [EntitySetNaming](#entitysetnaming)). Id of entity to update, and entity properties to update, and a **Select** object to define what fields from the created entity should be returned (after it's created).
The function returns as an object containing the chosen field and values in the Select - object.
Define object to be returned as **T**. For example Update<JsonElement>().


```csharp
var content = new Dictionary<string, object>
   {
      {"emailaddress1", "this@guy.com"}
   };

var select = new Select("emailaddress1");

var result = await crm.Update<JsonElement>("contact", "beedf6bd-a808-eb11-a813-000d3ab4653d", content, select);

foreach (var item in result.EnumerateObject())
   {
      Console.WriteLine($"{item.Name}, {item.Value}");
   }

//Prints
/**
 * @odata.context, https://pplab13-kimb.crm4.dynamics.com/api/data/v9.1/$metadata#contacts(emailaddress1)/$entity
 * @odata.etag, W/"29735317"
 * emailaddress1, this@guy.com
 * contactid, beedf6bd-a808-eb11-a813-000d3ab4653d
 */
```

## Upsert
There are two upsert functions equivalent to the update functions. The difference is that if an entity with the provided id is not found in CRM, it will be created with the provided values (Upsert).
|Method|Paramaters|Returnes|
:-|:-|:-|
|Upsert()|**string** entityName, **string** id, **Dictionary<string, object>** content|**void**|
|Upsert<T>()|**string** entityName, **string** id, **Dictionary<string, object>** content, **Select** select| Type **T**|
