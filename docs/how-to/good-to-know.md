# Good To Know
## Need to use the web api directly?
Do you need to to some operation this wrapper does not suport, and need to create a custom http request?
Use the **Client()** method to retrieve an authenticated (C#) **HttpClient**.
The client has the base address: [organization address]**/api/data/[Current/specified versionnumber]/**
```csharp
HttpClient client = await CRM.Client();

HttpResponseMessage accountResponse = await client.GetAsync("accounts");

// Do something with response
// ...
```
## Alternate Key
Anywhere this api can take an id of an Entity, the AlternateKey can be used. If the method takes the id of the entity as a string, just provide the method with the **AlternateKey.ToString()** method.

The alternate key represent the key you define in Dynamics
```csharp
var altKey = new AlternateKey();
//Add a key or partial key by using the AddKey() function.
//The first value is a string corresponing to the fields logical name, 
//the second value should correspond to the entity field value with a matching datatype (most likely int or string). //This has a TODO in source code for testing
altKey.AddKey("msdyn_externalaccountid","123");
//it is possible to add many alternative keys, as long as they are valid. 
//If you are sending more than one, CRM checks for combined field keys.

//Send the alternative key as a paramater to the Crm.get() function.
//The CRM.get() function only accepts alternative keys with a Select object.
var select = new Select("name");

Crm.Get("account", altKey, select)
```
