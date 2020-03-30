---
title: Értelmezze az Azure Active Directory naplósémáját az Azure Monitorban | Microsoft dokumentumok
description: Az Azure AD naplózási naplósémájának ismertetése az Azure Monitorban való használatra
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9f58b213e50a021651f35112a48d8f74ae59571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68987953"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Értelmezze az Azure AD naplózási naplóinak sémáját az Azure Monitorban (előzetes verzió)

Ez a cikk ismerteti az Azure Active Directory (Azure AD) naplózási naplóséma az Azure Monitorban. Minden egyes naplóbejegyzés szövegként van tárolva, és JSON-blobként van formázva, ahogy az a következő két példában látható: 

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

```json
{
    "records": [
    {
        "time": "2018-12-10T00:03:46.6161822Z",
        "resourceId": "/tenants/7918d4b5-0442-4a97-be2d-36f9f9962ece/providers/Microsoft.aadiam",
        "operationName": "Update policy",
        "operationVersion": "1.0",
        "category": "AuditLogs",
        "tenantId": "7918d4b5-0442-4a97-be2d-36f9f9962ece",
        "resultSignature": "None",
        "durationMs": 0,
        "callerIpAddress": "<null>",
        "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
        "identity": "MS-PIM",
        "level": "Informational",
        "properties": {
            "id": "Directory_VNXV4_28148892",
            "category": "Policy",
            "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
            "result": 0,
            "resultReason": "",
            "activityDisplayName": "Update policy",
            "activityDateTime": "2018-12-10T00:03:46.6161822+00:00",
            "loggedByService": "Core Directory",
            "operationType": "Update",
            "initiatedBy": {},
            "targetResources": [
            {
                "id": "5e7a8ae7-165d-44a4-a4f4-6141f8c8ef40",
                "displayName": "Default Policy",
                "type": "Policy",
                "modifiedProperties": []
            }
            ],
            "additionalDetails": []
        }
    }
    ]
}

```

## <a name="field-and-property-descriptions"></a>Mező- és tulajdonságleírások

| Mező neve | Leírás |
|------------|-------------|
| time       | A dátum és az idő (UTC). |
| operationName | A művelet neve. |
| operationVersion | Az ügyfél által kért REST API-verzió. |
| category | Jelenleg a *naplózás* az egyetlen támogatott érték. |
| tenantId | A bérlő i.GUID, amely a naplók társított. |
| resultType | A művelet eredménye. Az eredmény lehet *Sikeres* vagy *Sikertelen*. |
| resultSignature |  Ez a mező nincs leképezve, és nyugodtan figyelmen kívül hagyhatja. | 
| resultDescription | Az eredmény további leírása, amennyiben rendelkezésre áll. | 
| durationMs |  Ez a mező nincs leképezve, és nyugodtan figyelmen kívül hagyhatja. |
| callerIpAddress | A kérést küldő ügyfél IP-címe. | 
| correlationId | Az ügyfél által átadott opcionális GUID. Segíthet az ügyféloldali műveletek és a kiszolgálóoldali műveletek korrelációjában, és akkor hasznos, ha a szolgáltatásokat átszelő naplókat követi nyomon. |
| identity | A kérelem benyújtásakor bemutatott jogkivonat identitása. Az identitás lehet felhasználói fiók, rendszerfiók vagy egyszerű szolgáltatás. |
| szint | Az üzenet típusa. A naplók esetében a szint mindig *tájékoztató .* |
| location | Az adatközpont helye. |
| properties | A naplóhoz kapcsolódó támogatott tulajdonságok listája. További információt a következő táblázatban talál. | 

<br>

| Tulajdonság neve | Leírás |
|---------------|-------------|
| AuditEventKategória | A naplózási esemény típusa. Ez lehet *Felhasználókezelés*, *Alkalmazáskezelés*, vagy más típusú.|
| Identitás típusa | A típus lehet *Alkalmazás* vagy *Felhasználó*. |
| Művelettípus | A típus lehet *Hozzáadás,* *Frissítés*, *Törlés*. vagy *Más*. |
| Cél erőforrás típusa | Megadja azt a célerőforrás-típust, amelyen a műveletet végrehajtották. A típus lehet *Alkalmazás*, *Felhasználó*, *Szerep*, *Házirend* | 
| Célerőforrás neve | A célerőforrás neve. Ez lehet egy alkalmazás neve, egy szerepkör neve, egy egyszerű felhasználó neve vagy egy egyszerű szolgáltatás neve. |
| további célok | Az adott műveletekhez tartozó további tulajdonságok listája. Egy frissítési művelet esetén például a régi és az új értékek a *targetUpdatedProperties ( targetUpdatedProperties )* alatt jelennek meg. | 

## <a name="next-steps"></a>További lépések

* [A bejelentkezési naplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-sign-ins-log-schema.md)
* [Az Azure diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Gyakori kérdések és ismert hibák](concept-activity-logs-azure-monitor.md#frequently-asked-questions)