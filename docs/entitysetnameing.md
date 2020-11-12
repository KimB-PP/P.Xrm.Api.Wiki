## The ugly
Think of an entity in CRM. An entity is a table with columns, where each row is a record.
The table has a unique name and each column (attribute) are uniquely named within that table. 
We can then use these unique names when we want to target them.

Unfortunately, for some reason CRM operates with some devilish naming schemes making it a guessing game to determine how to target something.
Depending on the tool the underlying cell or table might have a different name. 

- The cleanest form is from plugins or form scripts. There you target an attribute the same way you named them originally, using the data attribute name - only using lowercase.

At some point CRM introduced an odata endpoint that required something called schamenames for entities and attributes.
To make things worse these were case sensitive, which resulted in attributes within the same table looking and only responding to this:
   - pp_BananaPieId
   - pp_applePieID
   - pp_Orangecakeid
   - pp_PlumPuddingID

The above were the custom ones, the standard attributes usually followed a CamelCase convention, but not always!
In some rare instances the schemaname for standard attributes didn't even match the letters of it's data attribute name.

Fortunately CRM has since scrapped the oData endpoint. Unfortunately some backwards schema-naming have been 
carried over to the Web Api. In short, this means that for the most part using lowercase will work. But if someone snook in an uppercase letter you have to adhere to it.

The naming of entities however is ruined.
- When you want to target a table/entity from the web api - you need to target it using it's plural name.
One of many problems is you don't know the plural name, even if you CREATED the table in CRM, you never got to give it a plural name? How could CRM know?

Usually CRM just adds an s to the end, but for a lot of custom tables that just end up as incorrect word altogether.
All of the hundreds of the standard tables doesn't even follow the same convention as your custom tables.
They still need to be targeted using a plural name, but the plural name could be:
- fruit[s] ?
- fruit[set] ?
- fruit[collection] ?
- fruit[ies]
- fruit[es]
- It could even be fruit in single form..

Logic is in place that let you operate on the actual entityname instead of its plural name.
For instance, you use account instead of accounts and so on.
Logic alone can't handle ALL the possible variations so there is also a hardcoded list in place for known discrepancies.
Per now this list is as follows:

| entityname | webapi plural name | 
|:-|:-|
|feedback|feedback|
|importdata|importdataset|
|knowledgearticlescategories|knowledgearticlecategories|
|knowledgearticleviews|knowledgearticleviews|
|languagelocale|languagelocale|
|msdyn_callablecontext_msdyn_playbooktemplate|msdyn_callablecontext_msdyn_playbooktemplateset|
|msdyn_odatav4ds|msdyn_odatav4ds|
|msdyn_wallsavedqueryusersettings|msdyn_wallsavedqueryusersettingses|
|adminsettingsentity|new_admin_settings_entities|
|solutionhistorydata|solutionhistories|
|subscriptionstatisticsoffline|subscriptionstatisticsofflineset|
|subscriptionstatisticsoutlook|subscriptionstatisticsoutlookset|
|subscriptionsyncentryoffline|subscriptionsyncentriesoffline|
|subscriptionsyncentryoutlook|subscriptionsyncentriesoutlook|
|webresource|webresourceset|
|principalobjectaccess|principalobjectaccessset|
|principalobjectattributeaccess|principalobjectattributeaccessset|




## Still unsure about entitynames? 
You can at any time run a function to check if ANY of the entitynames in you CRM environment will be translated to correct plural name:
This method takes 1-2 seconds and evaluates ALL entity names in your CRM.
It will respond with a list of entities that the naming logic would fail to handle correcty.

Typically this list contains zero to a couple of "never heard of entities", but it probably differs depending on environment.
```csharp
var result = await Crm.ValidateNamingConventions();

//This is an actual result, it mean all possible entities except these three would be handled correctly.
//----------------------------------
//result.ToString() :
// { "msdyn_aiodlabel_msdyn_aiconfiguration", "msdyn_aiodlabel_msdyn_aiconfigurationset" },
// { "msdyn_collabgraphresource", "msdyn_collabgraphresource" },
// { "msdyn_pendinggroupmembers", "msdyn_pendinggroupmemberses" },
//
// If you think that you will do work on any of these entities you can just make sure
// they are either added to the internal hardcoded precompiled list of discrepancies.
// Or, you can add them as follows:

EntitySetNaming.AddMapping("msdyn_pendinggroupmembers", "msdyn_pendinggroupmemberses");

//Ahh.. of course, no wonder it didn't work!
//one members, many memberses

```

As I wrote earlier in this document. It's ugly that have logic for using the actual entity name instead of the plural name what the standard WEb API expects.
But I guess I think the generated plural names are even uglier and something I strongly hope Microsoft withdraws.



