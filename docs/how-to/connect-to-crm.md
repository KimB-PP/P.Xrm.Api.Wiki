# Connect to CRM

You connect to Crm by creating an instance of the class PP.Xrm.WebApi.Api

#### Api parameters
```csharp
public Api(WebApiConfig config, int timeoutSeconds, string version)
```

| Parameter | Required | Description |
|:-|:-|:-|
|config|yes|the connection details|
|timeoutSeconds|no|Defaults to 135 seconds which gives a 15 sec headroom for CRM's internal 120 second timeout.|
|version|no|Defaults to "v9.1" - you can input a valid string version manually|


```csharp
//Set up config for access to CRM
var config = new WebApiConfig()
{
   //You set config properties here
   //from azure config og app.config etc..
};

//Connect to CRM
var Crm = new Api(config);
```



#### Connect using Application user and Secret
```csharp
var config = new WebApiConfig
{
   CrmAdTenantId = "value1",
   CrmEndpoint = "value2",
   CrmAppId = "value3",

   CrmAppSecret = "value4"
};

var Crm = new Api(config);
```
