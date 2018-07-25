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
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e1ae8e2a4dc9ef9c21300ebfc4df8c0f1c5819f2
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239935"
---
# <a name="interpret-the-azure-active-directory-audit-logs-schema-in-azure-monitor-preview"></a>Az Azure Monitor (előzetes verzió) az Azure Active Directory naplózási naplók séma értelmezése

Ez a cikk ismerteti az Azure AD naplózási séma az Azure monitorban. Minden egyes naplóbejegyzés tárolva, szöveg-, formázott JSON-blobként, ahogyan az alábbi két példa. 

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

| Mező neve | Leírás |
|------------|-------------|
| time       | Dátum és idő (UTC) |
| operationName | A művelet neve |
| operationVersion | Az ügyfél által kért REST API-verzió |
| category | Jelenleg *naplózási* egyetlen érték használható |
| tenantId | A naplók társított bérlő Guid |
| resultType | A művelet eredményeként, lehet *sikeres* vagy *hiba* |
| resultSignature |  Ez nem leképezett, és biztonságosan figyelmen kívül hagyhatja ezt a mezőt. | 
| resultDescription | Az eredmény további leírása, ha elérhetők | 
| durationMs |  Ez nem leképezett, és biztonságosan figyelmen kívül hagyhatja ezt a mezőt. |
| callerIpAddress | A kérést leadó ügyfél IP-címe | 
| correlationId | Az ügyfél által átadott nem kötelező GUID azonosítója. Ez segíthet a kiszolgálóoldali műveletek összevetését ügyféloldali művelet, és akkor hasznos, ha a nyomkövetési naplók, amelyek különböző szolgáltatásokat. |
| identity | A tokenben szereplő kérvényező identitás. Lehetnek egy felhasználói fiókot, a rendszer fiók vagy a szolgáltatás egyszerű. |
| szint | Üzenet típusa. Auditnaplók, ez nem mindig *tájékoztató* |
| location | Az adatközpontok |
| properties | Az auditnapló kapcsolatos támogatott tulajdonságok listája. További információ: az alábbi táblázatban. | 


| Tulajdonság neve | Leírás |
|---------------|-------------|
| AuditEventCategory | Naplózási esemény típusa. Lehet *felhasználókezelés*, *Alkalmazáskezelés* stb.|
| Azonosító típusa | *Alkalmazás* vagy *felhasználó* |
| Művelettípus | Lehet *Hozzáadás*, *frissítés*, *törlése* vagy *más* |
| Célerőforrás típusa | Megadja a célerőforrás típusa, amely a művelet meg lett végrehajtva. Lehet *alkalmazás*, *felhasználói*, *szerepkör*, *házirend* | 
| Cél erőforrás neve | A célként megadott erőforrás neve. Például ez lehet az alkalmazás nevét, a szerepkör nevét, egy egyszerű felhasználónév vagy szolgáltatásnév |
| additionalTargets | Megjeleníti az adott műveletek esetében minden egyéb tulajdonságot. Ha például egy frissítési művelet a régi és az új értékek vannak felsorolva *targetUpdatedProperties* | 

## <a name="next-steps"></a>További lépések

* [Az Azure monitor séma bejelentkezési naplók értelmezése](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [További információ az Azure diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [– Gyakori kérdések és ismert problémák](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)