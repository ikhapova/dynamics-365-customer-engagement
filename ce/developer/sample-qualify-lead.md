---
title: "Sample: Qualify a lead (Developer Guide for Dynamics 365 Customer Engagement) | MicrosoftDocs"
description: "The sample demonstrates how to qualify a lead and create an account, contact, or opportunity."
ms.custom: ""
ms.date: 10/31/2017
ms.reviewer: ""
ms.service: "crm-online"
ms.suite: ""
ms.tgt_pltfrm: ""
ms.topic: "samples"
applies_to: 
  - "Dynamics 365 (online)"
helpviewer_keywords: 
  - "sample for qualifying leads"
  - "qualifying leads, sample"
  - "creating lead-based accounts; contacts; or opportunities, sample"
  - "sample that creates lead-based accounts; contacts; or opportunities"
ms.assetid: fb89b821-ed96-4a24-bae9-3e217f2cf1dc
caps.latest.revision: 14
author: "JimDaly"
ms.author: "jdaly"
manager: "amyla"
---
# Sample: Qualify a lead

[!INCLUDE[](../includes/cc_applies_to_update_9_0_0.md)]

This sample code is for [!INCLUDE[pn_dynamics_crm_online](../includes/pn-dynamics-crm-online.md)]. Download the complete sample here [Business management samples](https://code.msdn.microsoft.com/Business-Management-Samples-6a482e62).  

## Prerequisites
[!INCLUDE[sdk-prerequisite](../includes/sdk-prerequisite.md)]
   
## Requirements  
[!INCLUDE[sdk_SeeConnectionHelper](../includes/sdk-seeconnectionhelper.md)]
  
## Demonstrates  
 This sample shows how to qualify a lead and create an account, contact, or opportunity based on the lead.  
  
## Example  
 [!code-csharp[BusinessManagement#WorkingWithLeads](../snippets/csharp/CRMV8/businessmanagement/cs/workingwithleads.cs#workingwithleads)]  
  
### See also  
 [Sales Entities (Lead, Opportunity, Competitor, Quote, Order, Invoice)](sales-entities-lead-opportunity-competitor-quote-order-invoice.md)   
 <xref:Microsoft.Crm.Sdk.Messages.QualifyLeadRequest>   
 [Lead Entity](lead-entity.md)