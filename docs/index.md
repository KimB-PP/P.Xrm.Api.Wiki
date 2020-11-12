**Versioning Build:** [![Build Status](https://peterpilot.visualstudio.com/PP.Xrm/_apis/build/status/PP.Xrm%20Release?branchName=master)](https://peterpilot.visualstudio.com/PP.Xrm/_build/latest?definitionId=6&branchName=master) **Test Build:** [![Build Status](https://peterpilot.visualstudio.com/PP.Xrm/_apis/build/status/PP.Xrm?branchName=master)](https://peterpilot.visualstudio.com/PP.Xrm/_build/latest?definitionId=5&branchName=master)
# Documentation

- [EntitySetNaming](/entitysetnaming.md)
- Basics
  - [Connect To CRM](/how-to/connect-to-crm.md)
  - [Create Entity](/how-to/create-entity.md)
  - [Update And Upsert Entity](/how-to/update-and-upsert-entity.md)
  - [Get Entity](/how-to/get-entity.md)
  - [Query / Get Multiple](/how-to/get-multiple.md)
  - [Good To Know](/how-to/good-to-know.md)

## Get the nuget
The nuget is available on [nuget.org](https://www.nuget.org/packages/PP.Xrm.WebApi/).

or (.net CLI)
```powershell
dotnet add package PP.Xrm.WebApi --version x.x.x
```


## Introduction
In short this instance is a wrapper for a standard HttpClient that points to CRM Web API.
The wrapper contains several extensions for the most used scenarios which makes development with the CRM Web API less verbose and hopefully easier.
You can always use the HttpClient directly by specifying your requests manually.
[Read the CRM Wep API documentation here.](https://docs.microsoft.com/en-us/powerapps/developer/common-data-service/webapi/overview)

The wrapper is a .NET Standard 2.0 Class library and is a such compatible with both .NET Core and .NET Framework.

A huge misconception is that the OrganizationService will be deprecated and replaced by the CRM Web API.
Infact the OrganizationService will probably never be replaced, it's at the very core and any request passed
to the CRM Web Api will eventually be forwarded onto the OrganizationService.




Since this isn't predicatable and Microsoft has a long history of discontinuing things they implement I have chosen to not adhere to the plural naming of entities.
- The extensions in this Web API expects you to name the entities using their data attribute name - alway lowercase.

You can read more of the entity naming logic and how it is handled this web api here:
[EntitySetNaming](/ReadMe/EntitySetNaming)

## Default Return Object Type
The different methods are able to return objects of type <T>. If no object type is specified, the default return type is JsonElement. This object is a result of using System.Text.Json to handle de-serialization and serialization of objects.
[JsonElement class description/documentation.](https://docs.microsoft.com/en-us/dotnet/api/system.text.json.jsonelement?view=netcore-3.1)
[Read System.Text.Json documentation.](https://docs.microsoft.com/en-us/dotnet/standard/serialization/system-text-json-how-to) 

## What's next?
See how to [Connect to Dynamics using this api](/how-to/connect-to-crm.md).
Or jump right into [making request to Dynamics](/how-to/create-entity.md).









