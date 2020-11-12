# Impersonate User
It is possible to interact with CRM "on behalf of" another CRM user ([Information about impersonation here](https://docs.microsoft.com/en-us/powerapps/developer/common-data-service/webapi/impersonate-another-user-web-api)).

|Method|Paramaters|Returnes|Note
:-|:-|:-|:-|
|Impersonate()|**string** imersonationId| void| Takes an Azure Active Directory (AAD) object id|
|Impersonate()|**Guid** impersonationId | void| Takes an Guid (systemuserid). The function tries to get the AAD object id to use insted. If it doesn't exist, it throws an error|
|RemoveImpersonate()||bool|Removes impersonation headers. Returnes true if impersonation headers existed. |

Best practice is to use Azure Active Directory object id to impersonate users. But as these are not always straight forward to get in crm, it is possible to impersonate using Guid of the systemuser Entity in crm. **The method will do additional calls to crm to find the AAD**, and impersonate user using that AAD.

When calling **impersonate()**, all subsequent "edits" using this api will be marked with the impersonated user. Calling **RemoveImpersonate()** will result in subsequent calls will be done normally. 

