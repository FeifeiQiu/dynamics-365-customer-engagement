---
title: "Use the OrganizationServiceContext class (Developer Guide for Dynamics 365 Customer Engagement)| MicrosoftDocs"
description: "The OrganizationServiceContext class lets you track changes, manage identities and relationships, and gives you access to the Dynamics 365 LINQ provider. This class also contains a OrganizationServiceContext.SaveChanges method that you use to submit the changes to data that the context is tracking"
ms.custom: ""
ms.date: 12/15/2017
ms.reviewer: ""
ms.service: "crm-online"
ms.suite: ""
ms.tgt_pltfrm: ""
ms.topic: "article"
applies_to: 
  - "Dynamics 365 (online)"
ms.assetid: d63562dd-c4fb-40de-82d4-25d7fb4d7553
caps.latest.revision: 51
author: "JimDaly"
ms.author: "jdaly"
manager: "amyla"
---
# Use the OrganizationServiceContext class

[!INCLUDE[](../../includes/cc_applies_to_update_9_0_0.md)]

In [!INCLUDE[pn_dynamics_crm_online](../../includes/pn-dynamics-crm-online.md)] Customer Engagement, you can use the <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceProxy> class to access the Web services. Alternatively, you can use the <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext> generated by the code generation tool to gain access to additional functionality. The `OrganizationServiceContext` class lets you track changes, manage identities and relationships, and gives you access to the [!INCLUDE[pn_dynamics_crm](../../includes/pn-dynamics-crm.md)] LINQ provider. This class also contains a <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext>.<xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.SaveChanges> method that you use to submit the changes to data that the context is tracking. This class is based on the same concept as the [DataServiceContext](https://msdn.microsoft.com/library/cc679618.aspx) class in [!INCLUDE[pn_WCF_long](../../includes/pn-wcf-long.md)] Data Services.  

 To generate this class, provide a value for the `/serviceContextName` parameter when you generate early bound types. The code generation tool uses this name as the name of the generated class. For more information about how to use the code generation tool, see [Use the Code Generation Utility (CrmSvcUtil.exe)](create-early-bound-entity-classes-code-generation-tool.md). You can use the organization service context when you develop applications, plug-ins, and workflow activities.  

<a name="how_to_use"></a>
## How to use the OrganizationServiceContext class  
 To instantiate the context class, you must pass the class constructor an object that implements the <xref:Microsoft.Xrm.Sdk.IOrganizationService> interface. One option is to pass an instance of the <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceProxy> class. For more information about the<xref:Microsoft.Xrm.Sdk.IOrganizationService> interface, see [Use IOrganizationService for Your Organization](use-organization-service-read-write-data-metadata.md).

 The following code example shows how to create a new instance of the context class. In this example, the context class was named AdventureWorksCycleServiceContext by specifying the name using the `/serviceContextName` parameter on the code generation tool:  

```csharp  
//For early bound types to work correctly, they have to be enabled on the proxy.  
_serviceProxy.EnableProxyTypes();  
AdventureWorksCycleServiceContext context = new AdventureWorksCycleServiceContext(_serviceProxy);  
```  

 After you create the organization service context object, you can begin to track create, modify, or delete entities. For example, the following code example shows how to instantiate a new contact, and then save it to a [!INCLUDE[pn_dynamics_crm](../../includes/pn-dynamics-crm.md)] server by using the service context object.  

```csharp  
//  Create a new contact record;  
AdventureWorksCycleServiceContext context = new AdventureWorksCycleServiceContext (_serviceProxy);  
Contact contact = new Contact()   
 {  
   FirstName = "Pamela",  
   LastName = "Brown",  
   Address1_Line1 = "123 Easy St.",  
   Address1_City = "Atlanta",  
   Address1_StateOrProvince = "GA",  
   Address1_PostalCode = "32254",  
   Telephone1 = "425-555-5678"   };  
context.AddObject(contact);  
context.SaveChanges();  
```  

 There are several points to note in the previous code example. First, after a new contact is instantiated, you pass that contact object to the <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext>.<xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.AddObject(Microsoft.Xrm.Sdk.Entity)> method so the context can begin tracking the object. The second point to note is that the new object is saved to the server by using the <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext>.<xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.SaveChanges> method.  

 The organization service context must track any entity or relationship that you want to submit to [!INCLUDE[pn_dynamics_crm](../../includes/pn-dynamics-crm.md)]. For example, you could retrieve a record with a LINQ query and the context would track that entity or you could use the <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext>.<xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.Attach(Microsoft.Xrm.Sdk.Entity)> method to cause the context to begin tracking the entity. You can work with data in a client application and create new entities, create related entities, and modify existing entities, but you must call the `SaveChanges` method on tracked entities to commit changes to the [!INCLUDE[pn_dynamics_crm](../../includes/pn-dynamics-crm.md)] server.  

<a name="track_changes"></a>   
## Track changes with the OrganizationServiceContext class  
 To determine how an entity is tracked by the context, you can check the <xref:Microsoft.Xrm.Sdk.Entity.EntityState> property on the entity instance. You must notify the organization service context to track an entity from [!INCLUDE[pn_dynamics_crm](../../includes/pn-dynamics-crm.md)] by calling various methods or by using a LINQ query. All entities returned from a [!INCLUDE[pn_LINQ](../../includes/pn-linq.md)] query are tracked by the service context.  

 You can add objects to the service context by calling one of the following methods in <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext>.  

|Method|Use|  
|------------|---------|  
|<xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.AddObject(Microsoft.Xrm.Sdk.Entity)>|Adds an entity to the set of entities the organization service context is tracking. The status of the entity in the context is set to `Created`. If the <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.SaveChanges> method is called, this record will be created or added to the server.|  
|<xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.Attach(Microsoft.Xrm.Sdk.Entity)>|Adds an entity to the set of entities the organization service context is tracking. The status of the entity in the context is set to `Unchanged`. If the <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.SaveChanges> method is called, this entity will not be sent to the server unless its status changes.|  
|<xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.CreateQuery(System.String)>|Adds the results of a query to the set of entities the organization service context is tracking.|  

<a name="track_related"></a>   
## Track related objects with the OrganizationServiceContext class  
 In [!INCLUDE[pn_crm_shortest](../../includes/pn-crm-shortest.md)] and [!INCLUDE[pn_crm_online_shortest](../../includes/pn-crm-online-shortest.md)], the organization service context lets you create and update relationships between entities. The navigation properties generated by the [!INCLUDE[sdk_CodeGenUtility](../../includes/sdk-codegenutility.md)] tool and located in the early-bound classes let you access and change related entity properties and relationships. The organization service context must be tracking the related entity for the related entity to be available to be updated on the server.  

 Use the following methods in <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext> to work with related entities and to add the entity to the service context:  

|Method|Use|  
|------------|---------|  
|<xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.AddRelatedObject(Microsoft.Xrm.Sdk.Entity,Microsoft.Xrm.Sdk.Relationship,Microsoft.Xrm.Sdk.Entity)>|Adds the target to the context. Calls the <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.Attach(Microsoft.Xrm.Sdk.Entity)> method on the target entity and then calls the <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.AddLink(Microsoft.Xrm.Sdk.Entity,Microsoft.Xrm.Sdk.Relationship,Microsoft.Xrm.Sdk.Entity)> method between the source entity and the target (related) entity.|  
|<xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.AttachLink(Microsoft.Xrm.Sdk.Entity,Microsoft.Xrm.Sdk.Relationship,Microsoft.Xrm.Sdk.Entity)>|Adds the related entity to the context for tracking. The status of the entity in the context is set to `Unchanged`.|  
|<xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.AddLink(Microsoft.Xrm.Sdk.Entity,Microsoft.Xrm.Sdk.Relationship,Microsoft.Xrm.Sdk.Entity)>|Creates a relationship between the source and target entities. Adds the target to the context. The status of the target entity in the context is set to `Created`.|  
|<xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.LoadProperty(Microsoft.Xrm.Sdk.Entity,System.String)>|Loads the related entity set for the specified relationship. Gives access to related entities by using the navigation property. Call the <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.AddObject(Microsoft.Xrm.Sdk.Entity)> method on the related entity after you have accessed the entity by using a navigation property on the parent entity.|  
|<xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.UpdateObject(Microsoft.Xrm.Sdk.Entity)>|Changes the state of the specified entity in the `OrganizationServiceContext` to Modified.|  
|<xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.DeleteObject(Microsoft.Xrm.Sdk.Entity)>|Changes the state of the specified entity to be deleted in the `OrganizationServiceContext`.|  

<a name="BKMK_LoadRelatedEntities"></a>   
### Load related entities using navigation properties  
 Related entities for entities you have retrieved using LINQ will be null until you use <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.LoadProperty(Microsoft.Xrm.Sdk.Entity,Microsoft.Xrm.Sdk.Relationship)> to retrieve them. The following code sample shows how to access Task records associated with a specific Contact record.  

```csharp  
Contact pam = context.ContactSet.Where(c => c.FirstName == "Pamela").FirstOrDefault();  
if (pam != null)  
{  
// pam.Contact_Tasks is null until you use LoadProperty  
    context.LoadProperty(pam, "Contact_Tasks");  
    Task firstTask = pam.Contact_Tasks.FirstOrDefault();  
}  
```  

<a name="save_changes"></a>   
## Save changes with the OrganizationServiceContext class  
 The organization service context holds a graph of the entities it is tracking. The order in which the organization service context processes entity changes and submits them to the server is important. Updates to primary entity are processed, and then related entities are processed. If a value is set on the primary entity by the related entity, that value is used when updating data on the server.  

 If an error occurs when entity information is being saved, a new exception type that contains the <xref:Microsoft.Xrm.Sdk.SaveChangesResult> is thrown by the <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext>.<xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.SaveChanges> method, regardless of the value of the <xref:Microsoft.Xrm.Sdk.Client.SaveChangesOptions> parameter that is passed into the method.  

<a name="virtual"></a>   
## Use virtual methods when the context is changed  
 Sometimes it may be necessary to take actions based on changes in the <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext>. To facilitate this, virtual methods are provided to allow you to intercept or be notified of an operation. To take advantage of these methods, you either have to derive from <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext> or change the generated organization service context.The following table lists the virtual methods.  


|                                                                              Method                                                                              |                                                                                       Description                                                                                        |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                            <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.OnBeginEntityTracking(Microsoft.Xrm.Sdk.Entity)>                            |                                                         Called after an entity is attached to the `OrganizationServiceContext`.                                                          |
| <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.OnBeginLinkTracking(Microsoft.Xrm.Sdk.Entity,Microsoft.Xrm.Sdk.Relationship,Microsoft.Xrm.Sdk.Entity)> |                                                           Called after a link is attached to the `OrganizationServiceContext`.                                                           |
|                             <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.OnEndEntityTracking(Microsoft.Xrm.Sdk.Entity)>                             |                                                        Called after an entity is detached from the `OrganizationServiceContext`.                                                         |
|                             <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.OnEndEntityTracking(Microsoft.Xrm.Sdk.Entity)>                             |                                                          Called after a link is detached from the `OrganizationServiceContext`.                                                          |
|                          <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.OnExecuting(Microsoft.Xrm.Sdk.OrganizationRequest)>                           |                          Called immediately before a request is submitted to the [!INCLUDE[pn_microsoftcrm_server](../../includes/pn-microsoftcrm-server.md)].                           |
|        <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.OnExecute(Microsoft.Xrm.Sdk.OrganizationRequest,Microsoft.Xrm.Sdk.OrganizationResponse)>        | Called immediately after a request is submitted to the [!INCLUDE[pn_microsoftcrm_server](../../includes/pn-microsoftcrm-server.md)], regardless of whether an exception occurred or not. |
|                     <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.OnSavingChanges(Microsoft.Xrm.Sdk.Client.SaveChangesOptions)>                      |                                                            Called before any operations occur after a call to `SaveChanges`.                                                             |
|                     <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext.OnSaveChanges(Microsoft.Xrm.Sdk.SaveChangesResultCollection)>                      |                                        Called when all of the operations for a call to `SaveChanges` have completed, or when there is a failure.                                         |

### See also  
 [Use the Early Bound Entity Classes in Code](use-early-bound-entity-classes-code.md)   
 [Sample: LINQ Query Examples for Dynamics 365](sample-complex-linq-queries.md)   
 [Using the Code Generation Utility (CrmSvcUtil.exe)](create-early-bound-entity-classes-code-generation-tool.md)   
 [Use the Early Bound Entity Classes for Create, Update and Delete](use-early-bound-entity-classes-create-update-delete.md)   
<xref:Microsoft.Xrm.Sdk.IOrganizationService>   
 <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceContext>