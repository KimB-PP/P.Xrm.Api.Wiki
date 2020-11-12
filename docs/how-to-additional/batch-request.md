# Batch Request
Batching request against crm is very flexible in the web API, but this wrapper api have limited it's functionality **some**. 
[Read about Batch requests in the official documentation.](https://docs.microsoft.com/en-us/powerapps/developer/common-data-service/webapi/execute-batch-operations-using-web-api) 

Batching means that you can send multiple request to crm in one http request. With this you can limit the amount of http request agains crm (does not circumvent the limitation of processing minutes).

While limiting the number of http requests against crm is good to have, the main reason for this implementation in this wrapper is to be able to have [transaction - like functionality](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/transactions-transact-sql?view=sql-server-ver15) in the http requests. This means using this wrapper to group a create and update requests, if one of the update requests fails, **All** other changes in that grouping will be reset, and crm would look like it did before the requests was handled.

## Basic
Let's say you want to create one account, and two contacts in one http request. Using the we api to to this is tedious, so let's use this wrapper api:

First: we define what should be created.
```csharp
var account = new Dictionary<string, object>
   {
      {"name", "My Big Company!" }
   };

var firstContact = new Dictionary<string, object>
   {
      {"lastname","Finn"},
      {"firstname", "Per Arne"},
   };
            
var secondContact = new Dictionary<string, object>
   {
      {"lastname","Olsen"},
      {"firstname", "Banden"},
   
```
Then let's initiate the Batch object, and start adding the different operations that we want to excecute in the batch job.
User the **.Add()** method to add the wanted request. 

```csharp
var batchJob = new Batch();

batchJob.Add(CRM.CreateRequest("account", account));
batchJob.Add(CRM.CreateRequest("contact", firstContact));
batchJob.Add(CRM.CreateRequest("contact", secondContact));
   
```
In this example we want to create entities, but the Add method takes any request type. To make these requests, the Api class has methods to create the requests:
|Method|Note|
:-|:-|
|CreateRequest|Builds a create request|
|UpdateRequest|Builds a updaterequest|
|UpsertRequest|Builds a upsert request|
|DeleteRequest|Builds a delete request|
|BuildRequest|Builds a request from httprequest and custom http content.|


To perform the batch job, run the **ExecuteBatch()** method. The method returns a **bool**, true if the batch job was successful, false otherwise. 

```csharp
var isSuccessfull = await CRM.ExecuteBatch(batchJob);
```

If any of the requests fails, all fail. At some point we may support having more controll on grouping requests in a batch, but that is not a priority at this point. If that is something you need, use the web api directly.

## Use Entity id from one batch item in another
Doing operations against crm you might want to create an account, and associate that account to a contact. Let's use the previous example, but now we want the created account to be the first created contact's parent customer. This is possible in a batch job, no need to do two requests against crm!
We will use the **Add()** method to add the create request, but we will store the returned object, a **BatchItem** object. This object can then be used as a reference to the entity that will be created.

```csharp
 var account = new Dictionary<string, object>
   {
       {"name", "My Big Company!" }
   };
            
BatchItem accountRequest = batchJob.Add(CRM.CreateRequest("account", account));
var firstContact = new Dictionary<string, object>
   {
      {"lastname","Finn"},
      {"firstname", "Per Arne"},
      {"parentcustomerid_account@odata.bind", accountRequest } // set the reference to the account as the BatchItem
   };

batchJob.Add(CRM.CreateRequest("contact", firstContact)); 

await CRM.ExecuteBatch(batchJob);
```

This will result with the contact having the account as the parentcustomer
