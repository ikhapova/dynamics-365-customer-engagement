---
title: "Retrieve and execute predefined queries (Developer Guide for Dynamics 365 Customer Engagement)| MicrosoftDocs"
description: "Dynamics 365 Customer Engagement provides a way for administrators to create system views that are available to all users. Read how you can compose a predefined query and use FetchXML to create a query string to retrieve data"
ms.custom: ""
ms.date: 10/31/2017
ms.reviewer: ""
ms.service: "crm-online"
ms.suite: ""
ms.tgt_pltfrm: ""
ms.topic: "article"
applies_to: 
  - "Dynamics 365 (online)"
ms.assetid: 3d771a18-3dc5-4372-a7c7-40b3b1f986d8
caps.latest.revision: 16
author: "JimDaly"
ms.author: "jdaly"
manager: "amyla"
---

# Retrieve and execute predefined queries

[!INCLUDE[](../../includes/cc_applies_to_update_9_0_0.md)]

[!INCLUDE[pn_dynamics_crm](../../includes/pn-dynamics-crm.md)] Customer Engagement provides a way for administrators to create system views that are available to all users. Individual users can save the advanced find queries for re-use in the application. Both of these represent predefined queries you can retrieve and execute using the Web API. You can also compose a query using FetchXml and use that to retrieve data.

<a name="bkmk_predefinedQueries"></a>

## Predefined queries

[!INCLUDE[pn_dynamics_crm](../../includes/pn-dynamics-crm.md)] allows you to define, save, and execute two types of queries as listed here.

|Query type|Description|
|----------------|-----------------|
|**Saved Query**|System-defined views for an entity. These views are stored in the <xref href="Microsoft.Dynamics.CRM.savedquery?text=savedquery EntityType" />. [!INCLUDE[proc_more_information](../../includes/proc-more-information.md)] [Customize Entity Views](../customize-dev/customize-entity-views.md)|
|**User Query**|Advanced Find searches saved by users for an entity. These views are stored in the <xref href="Microsoft.Dynamics.CRM.userquery?text=userquery EntityType" />. [!INCLUDE[proc_more_information](../../includes/proc-more-information.md)] [UserQuery (Saved View) Entity](../userquery-saved-view-entity.md)|

Records for both of these types of entities contain the FetchXML definition for the data to return. You can query the respective entity type to retrieve the primary key value. With the primary key value, you can execute the query by passing the primary key value. For example, to execute the **Active Accounts** saved query, you must first get the primary key using a query like this.

```http
GET [Organization URI]/api/data/v9.0/savedqueries?$select=name,savedqueryid&$filter=name eq 'Active Accounts'
```

You can then use the savedqueryid value and pass it as the value to the savedQuery parameter to the accounts entity set.

```http
GET [Organization URI]/api/data/v9.0/accounts?savedQuery=00000000-0000-0000-00aa-000010001002
```

Use the same approach to get the userqueryid and pass it as the value to the userQuery parameter to the entity set that matches the corresponding                  returnedtypecode of the saved query.

```http
GET [Organization URI]/api/data/v9.0/accounts?userQuery=121c6fd8-1975-e511-80d4-00155d2a68d1
```

### Apply a query to any collection of the appropriate type

In addition to simply applying the saved query to the main entity set collection, you can also use a saved query or user query to apply the same filtering on any collection of the appropriate type of entities. For example, if you want to apply a query against just the entities related to a specific entity, you can apply the same pattern. For example, the following URL will apply the **Open Opportunities** query against the opportunities related to a specific account via the `opportunity_parent_account` collection-valued navigation property.

```http
GET [Organization URI]/api/data/v9.0/accounts(8f390c24-9c72-e511-80d4-00155d2a68d1)/opportunity_parent_account/?savedQuery=00000000-0000-0000-00aa-000010003001
```

<a name="bkmk_useFetchXML"></a>

## Use custom FetchXML

FetchXML is a proprietary query language that provides capabilities to perform aggregation. The saved queries and user queries stored in <xref href="Microsoft.Dynamics.CRM.savedquery?text=savedquery EntityType" /> and <xref href="Microsoft.Dynamics.CRM.userquery?text=userquery EntityType" /> respectively both include a fetchxml property that defines the query. You can use FetchXML directly with the<xref:Microsoft.Xrm.Sdk.IOrganizationService>. <xref:Microsoft.Xrm.Sdk.IOrganizationService.RetrieveMultiple*> method or with the <xref:Microsoft.Xrm.Sdk.Messages.RetrieveMultipleRequest>. [!INCLUDE[proc_more_information](../../includes/proc-more-information.md)] [Build Queries with FetchXML](../org-service/build-queries-fetchxml.md)

You can pass URL encoded FetchXML as a query to the entity set corresponding to the root entity of the query using the FetchXML parameter to return the results from the Web API. For example, you can have the following FetchXML that has account as the entity.  

```xml  
<fetch mapping='logical'>
   <entity name='account'>
      <attribute name='accountid'/>
      <attribute name='name'/>
</entity>
</fetch>
```

The URL encoded value of this FetchXML is as shown here.

```text
%3Cfetch%20mapping='logical'%3E%3Centity%20name='account'%3E%3Cattribute%20name='accountid'/%3E%3Cattribute%20name='name'/%3E%3C/entity%3E%3C/fetch%3E
```

Most programming languages include a function to URL encode a string. For example, in [!INCLUDE[pn_JavaScript](../../includes/pn-javascript.md)] you use the                  [encodeURI](http://www.ecma-international.org/ecma-262/5.1/) function. You should URL encode any request that you send to any RESTful web service. If you paste a URL into the address bar of your browser it should URL encode the address automatically. The following example shows a GET request using the FetchXML shown previously using the entity set path for accounts.

**Request**

```http
GET [Organization URI]/api/data/v9.0/accounts?fetchXml=%3Cfetch%20mapping='logical'%3E%3Centity%20name='account'%3E%3Cattribute%20name='accountid'/%3E%3Cattribute%20name='name'/%3E%3C/entity%3E%3C/fetch%3E HTTP/1.1
Accept: application/json
OData-MaxVersion: 4.0
OData-Version: 4.0
```

**Response**

```http
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal
OData-Version: 4.0

{  
  "@odata.context":"[Organization URI]/api/data/v9.0/$metadata#accounts(accountid,name)","value":[  
    {  
      "@odata.etag":"W/\"506678\"","accountid":"89390c24-9c72-e511-80d4-00155d2a68d1","name":"Fourth Coffee (sample)"  
    },{  
      "@odata.etag":"W/\"502172\"","accountid":"8b390c24-9c72-e511-80d4-00155d2a68d1","name":"Litware, Inc. (sample)"  
    },{  
      "@odata.etag":"W/\"502174\"","accountid":"8d390c24-9c72-e511-80d4-00155d2a68d1","name":"Adventure Works (sample)"  
    },{  
      "@odata.etag":"W/\"506705\"","accountid":"8f390c24-9c72-e511-80d4-00155d2a68d1","name":"Fabrikam, Inc. (sample)"  
    },{  
      "@odata.etag":"W/\"506701\"","accountid":"91390c24-9c72-e511-80d4-00155d2a68d1","name":"Blue Yonder Airlines (sample)"  
    },{  
      "@odata.etag":"W/\"502180\"","accountid":"93390c24-9c72-e511-80d4-00155d2a68d1","name":"City Power & Light (sample)"  
    },{  
      "@odata.etag":"W/\"502182\"","accountid":"95390c24-9c72-e511-80d4-00155d2a68d1","name":"Contoso Pharmaceuticals (sample)"  
    },{  
      "@odata.etag":"W/\"506704\"","accountid":"97390c24-9c72-e511-80d4-00155d2a68d1","name":"Alpine Ski House (sample)"  
    },{  
      "@odata.etag":"W/\"502186\"","accountid":"99390c24-9c72-e511-80d4-00155d2a68d1","name":"A. Datum Corporation (sample)"  
    },{  
      "@odata.etag":"W/\"502188\"","accountid":"9b390c24-9c72-e511-80d4-00155d2a68d1","name":"Coho Winery (sample)"  
    },{  
      "@odata.etag":"W/\"504177\"","accountid":"0a3238d4-f973-e511-80d4-00155d2a68d1","name":"Litware, Inc."  
    }  
  ]  
}  
```

<a name="bkmk_WebAPIFetchPaging"></a>

### Paging with FetchXML

With fetchXML you can apply paging by setting the `page` and `count` attributes of the `fetch` element. For example, to set a query for accounts and limit the number of entities to 2 and to return just the first page, the following fetchXML:

```xml
<fetch mapping="logical" page="1" count="2">  
 <entity name="account">  
  <attribute name="accountid" />  
  <attribute name="name" />  
  <attribute name="industrycode" />  
 <order attribute="name" />  
 </entity>  
</fetch>
```

With a request using fetchXML you can also request a paging cookie and include it with your query. [!INCLUDE[proc_more_information](../../includes/proc-more-information.md)] [Page large result sets with FetchXML](../org-service/page-large-result-sets-with-fetchxml.md)  

A paging cookie must be requested as an annotation. Set the `odata.include-annotations` preference to use (or include)                          `Microsoft.Dynamics.CRM.fetchxmlpagingcookie` and a `@Microsoft.Dynamics.CRM.fetchxmlpagingcookie` property will be returned with the result.

### See also

 [Web API Query Data Sample (C#)](web-api-query-data-sample-csharp.md)<br />
 [Web API Query Data Sample (Client-side JavaScript)](web-api-query-data-sample-client-side-javascript.md)<br />
 [Perform operations using the Web API](perform-operations-web-api.md)<br />
 [Compose Http requests and handle errors](compose-http-requests-handle-errors.md)<br />
 [Query Data using the Web API](query-data-web-api.md)<br />
 [Create an entity using the Web API](create-entity-web-api.md)<br />
 [Retrieve an entity using the Web API](retrieve-entity-using-web-api.md)<br />
 [Update and delete entities using the Web API](update-delete-entities-using-web-api.md)<br />
 [Associate and disassociate entities using the Web API](associate-disassociate-entities-using-web-api.md)<br />
 [Use Web API functions](use-web-api-functions.md)<br />
 [Use Web API actions](use-web-api-actions.md)<br />
 [Execute batch operations using the Web API](execute-batch-operations-using-web-api.md)<br />
 [Impersonate another user using the Web API](impersonate-another-user-web-api.md)<br />
 [Apply optimistic concurrency using the Web API](perform-conditional-operations-using-web-api.md)<br />