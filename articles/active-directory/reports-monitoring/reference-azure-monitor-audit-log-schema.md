---
title: Az Azure Active Directory naplózási séma az Azure Monitor (előzetes verzió) értelmezése |} A Microsoft Docs
description: Az Azure Monitor (előzetes verzió) használata az Azure AD naplózási log sémáját leírása
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 8cbeeabfe254e40343632e9d6db3a55c3ea78bbb
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "42060799"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Az Azure Monitor (előzetes verzió) az Azure AD naplózási naplók séma értelmezése

Ez a cikk ismerteti az Azure Active Directory (Azure AD) naplózási séma az Azure monitorban. Minden egyes naplóbejegyzés szövegként tárolt és formázott JSON-blobként, a következő két példa látható módon: 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

## <a name="field-and-property-descriptions"></a>A mező és a tulajdonság leírása

| Mező neve | Leírás |
|------------|-------------|
| time       | Dátuma és időpontja (UTC). |
| operationName | A művelet neve. |
| operationVersion | Az ügyfél által kért REST API-verzió. |
| category | Jelenleg *naplózási* az egyetlen támogatott érték van. |
| tenantId | GUID, a naplók társított tenant. |
| resultType | A művelet eredményét. Az eredmény lehet *sikeres* vagy *hiba*. |
| resultSignature |  Ez a mező nem leképezett, és biztonságosan figyelmen kívül hagyhatja azt. | 
| resultDescription | Az eredmény további leírását, ha elérhetők. | 
| durationMs |  Ez a mező nem leképezett, és biztonságosan figyelmen kívül hagyhatja azt. |
| callerIpAddress | A kérést leadó ügyfél IP-címét. | 
| correlationId | Egy nem kötelező GUID, amely az ügyfél által átadott. Kiszolgálóoldali műveletek összevetését ügyféloldali műveletek megkönnyíti, és ez akkor hasznos, ha követi nyomon, amelyek szolgáltatások naplók. |
| identity | Az identitás a tokenben szereplő leadta a kérelmet. Az identitás lehet egy felhasználói fiókot, rendszerfiók vagy egyszerű szolgáltatást. |
| szint | Az üzenet típusa. A vizsgálati naplók, szintje mindig *tájékoztató*. |
| location | Az Adatközpont helyét. |
| properties | Az auditnapló kapcsolódó támogatott tulajdonságok listája. További információkért tekintse meg a következő táblázat. | 

<br>

| Tulajdonság neve | Leírás |
|---------------|-------------|
| AuditEventCategory | A naplózási esemény típusa. Ez lehet *felhasználókezelés*, *Alkalmazáskezelés*, vagy más típusú.|
| Azonosító típusa | A típus lehet *alkalmazás* vagy *felhasználói*. |
| Művelettípus | A típus lehet *Hozzáadás*, *frissítés*, *törlése*. vagy *más*. |
| Célerőforrás típusa | Megadja a célerőforrás típusa, amely a művelet meg lett végrehajtva. A típus lehet *alkalmazás*, *felhasználói*, *szerepkör*, *házirend* | 
| Cél erőforrás neve | A célként megadott erőforrás neve. Az alkalmazás nevét, a szerepkör nevét, egy egyszerű felhasználónév vagy szolgáltatásnév lehet. |
| additionalTargets | Megjeleníti az adott műveletek esetében minden egyéb tulajdonságot. Ha például egy frissítési művelet a régi és az új értékek vannak felsorolva *targetUpdatedProperties*. | 

## <a name="next-steps"></a>További lépések

* [A bejelentkezési naplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-sign-ins-log-schema.md)
* [További információ az Azure-beli diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Gyakori kérdések és ismert hibák](overview-activity-logs-in-azure-monitor.md#frequently-asked-questions)
