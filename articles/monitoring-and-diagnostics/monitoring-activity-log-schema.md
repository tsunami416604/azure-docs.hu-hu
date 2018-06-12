---
title: Azure tevékenységnapló esemény séma
description: Az esemény-sémát a tevékenység naplóba kibocsátott adatokról ismertetése
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 4/12/2018
ms.author: dukek
ms.component: activitylog
ms.openlocfilehash: f6f6c59195fdc79959a1964c1f2770c3b6a68b22
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264551"
---
# <a name="azure-activity-log-event-schema"></a>Azure tevékenységnapló esemény séma
A **Azure tevékenységnapló** , amely bármely történt az Azure-előfizetés szintű események betekintést biztosít a naplót. Ez a cikk ismerteti az adatok kategóriánként esemény séma.

## <a name="administrative"></a>Adminisztratív
Ez a kategória tartalmazza az összes rekord létrehozása, update, delete és művelet műveleteket Resource Manageren keresztül. Milyen típusú mutatunk be, ebbe a kategóriába tartozó eseményeket például "virtuális gép létrehozása" és "hálózati biztonsági csoport törlése" minden felhasználó vagy alkalmazás erőforrás-kezelő használatával végrehajtott műveletet egy adott erőforrástípus művelet van modellezve. Ha a művelet típusa írási, a Delete és a művelet, a rekordokat a kezdő és a sikeres vagy sikertelen lesz, hogy a művelet tárolja, amely a felügyeleti kategória. A felügyeleti kategória is a szerepköralapú hozzáférés-vezérlést módosításokat egy előfizetésben.

### <a name="sample-event"></a>Mintaesemény
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Tulajdonság leírása
| Elem neve | Leírás |
| --- | --- |
| Engedélyezési |Az esemény tulajdonságainak RBAC BLOB. Az "action", "szerepkör" és "hatókör" Tulajdonságok általában tartalmazza. |
| hívó |A művelet, a jogcím vagy a rendelkezésre állás alapján SPN jogcím végző felhasználó e-mail címe. |
| csatornák |A következő értékek egyikét: "Rendszergazda", "Művelet" |
| Jogcímek |A jwt-t az Active Directory hogy hitelesítésre használt token a felhasználó vagy alkalmazás sikerült végrehajtani a műveletet az erőforrás-kezelőben. |
| correlationId |Általában egy GUID Azonosítót a karakterlánc formátuma. Az eseményeket, amelyek megosztása a correlationId ugyanazon uber művelet tartozik. |
| leírás |Egy esemény statikus szöveges leírása. |
| eventDataId |Az esemény egyedi azonosítója. |
| httpRequest |A Http-kérelem leíró BLOB. Általában tartalmazza a "clientRequestId", "clientIpAddress" és "method" (HTTP-metódus. For example, amelyre az). |
| szint |Az esemény szintje. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés", "Tájékoztató" és "Részletes" |
| resourceGroupName |Az érintett erőforrás az erőforráscsoport neve. |
| resourceProviderName |Az erőforrás-szolgáltató az érintett erőforrás neve |
| resourceId |Erőforrás-azonosító az érintett erőforrás. |
| operationId |Az eseményeket, amelyek megfelelnek egy művelettel között megosztott GUID. |
| operationName |A művelet neve. |
| properties |Állítsa be a `<Key, Value>` az esemény részleteit leíró párok (Ez azt jelenti, hogy szótárában). |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori értékek a következők: folyamatban van, sikeres, sikertelen, aktív, a lépések megoldva. |
| a részállapot |Általában a megfelelő REST HTTP-állapotkód: hívja, de más a részállapot, például a gyakori értékek leíró karakterláncok is használható: OK (HTTP-állapotkód:: 200), létrehozott (HTTP-állapotkód:: 201-es), fogadja el (HTTP-állapotkód:: 202), nem tartalom (HTTP-állapotkód: 204), hibás kérés (HTTP-állapotkód:: 400), nem található (HTTP-állapotkód: 404-es), ütközés (HTTP-állapotkód: : 409), belső kiszolgálóhiba (HTTP-állapotkód: 500), a Service nem érhető el (HTTP-állapotkód: 503-as), átjáró időtúllépése (HTTP-állapotkód: 504). |
| eventTimestamp |Az esemény az esemény megfelelő a kérés feldolgozása az Azure-szolgáltatás által kiváltott idejét jelző időbélyegző. |
| submissionTimestamp |Az esemény váltak elérhetővé lekérdezése idejét jelző időbélyegző. |
| subscriptionId |Az Azure előfizetés-azonosító. |

## <a name="service-health"></a>Szolgáltatások állapota
Ez a kategória bármely Azure-ban történt szolgáltatás állapotának események adatait tartalmazza. Az ebbe a kategóriába tartozó mutatunk be eseménytípusra példa: "USA keleti régiója az SQL Azure tapasztal állásidő." Szolgáltatás állapotával kapcsolatos események térjen öt fajta: beavatkozás szükséges, támogatott helyreállítási, incidens, karbantartási, adatokat vagy biztonsági, és csak jelenik meg, ha egy erőforrást, amely akkor negatív hatással lehet az esemény az előfizetést.

### <a name="sample-event"></a>Mintaesemény
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Tekintse meg a [szolgáltatás állapotával kapcsolatos értesítésekre](./monitoring-service-notifications.md) cikk tulajdonságaiban tudnivalók dokumentációját.

## <a name="alert"></a>Riasztás
Ez a kategória valamennyi aktiváláshoz Azure riasztások adatait tartalmazza. Az ebbe a kategóriába tartozó mutatunk be eseménytípusra példa: "CPU % myVM már több mint 80 az elmúlt 5 percben." Az Azure rendszereket rendelkezik egy riasztási koncepció--valamiféle szabály megadása, és értesítést kaphat, ha feltételek egyeznek meg, hogy a szabály. Minden alkalommal, amikor egy támogatott Azure riasztástípus "aktiválja," vagy a feltételek értesítést létrehozásához, egy rekordot az aktiválás is leküldött ebbe a kategóriába a műveletnapló.

### <a name="sample-event"></a>Mintaesemény

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Tulajdonság leírása
| Elem neve | Leírás |
| --- | --- |
| hívó | Always Microsoft.Insights/alertRules |
| csatornák | Mindig "rendszergazda, a művelet" |
| Jogcímek | JSON-blob a SPN (egyszerű szolgáltatásnév), vagy az erőforrás típusát, a riasztás motor. |
| correlationId | A karakterlánc-formátum GUID azonosítója. |
| leírás |A figyelmeztetési esemény statikus szöveges leírása. |
| eventDataId |A riasztási eseménynek egyedi azonosítója. |
| szint |Az esemény szintje. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés", "Tájékoztató" és "Részletes" |
| resourceGroupName |Ha egy metrika riasztás az érintett erőforrás az erőforráscsoport neve. Más riasztástípusok ettől esetén ez a riasztás saját magát tartalmazó erőforráscsoport nevét. |
| resourceProviderName |Ha egy metrika riasztás az érintett erőforrás az erőforrás-szolgáltató neve. A más típusú Ez az erőforrás-szolgáltató magát a riasztás nevét. |
| resourceId | Az erőforrás-azonosító a metrika riasztások esetén érintett erőforrás neve. A más típusú Ez az erőforrás-azonosítója, a riasztás erőforrás magát. |
| operationId |Az eseményeket, amelyek megfelelnek egy művelettel között megosztott GUID. |
| operationName |A művelet neve. |
| properties |Állítsa be a `<Key, Value>` az esemény részleteit leíró párok (Ez azt jelenti, hogy szótárában). |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori értékek a következők: folyamatban van, sikeres, sikertelen, aktív, a lépések megoldva. |
| a részállapot | A riasztások általában null értékű. |
| eventTimestamp |Az esemény az esemény megfelelő a kérés feldolgozása az Azure-szolgáltatás által kiváltott idejét jelző időbélyegző. |
| submissionTimestamp |Az esemény váltak elérhetővé lekérdezése idejét jelző időbélyegző. |
| subscriptionId |Az Azure előfizetés-azonosító. |

### <a name="properties-field-per-alert-type"></a>Tulajdonságok mező szerepeljen riasztás típusa
A Tulajdonságok mező attól függően, hogy a riasztás esemény forrását különböző értékeket fogja tartalmazni. Két közös riasztási eseménynek szolgáltatók a következők: tevékenységnapló riasztások és metrika riasztásokat.

#### <a name="properties-for-activity-log-alerts"></a>A műveletnapló riasztások tulajdonságai
| Elem neve | Leírás |
| --- | --- |
| properties.subscriptionId | A tevékenység napló riasztási szabály által aktiválható okozó tevékenység napló esemény az előfizetés-azonosító. |
| properties.eventDataId | Az adatok Eseményazonosítójú tevékenység napló riasztási szabály által aktiválható okozó tevékenység napló esemény. |
| properties.resourceGroup | Az erőforráscsoport tevékenység napló riasztási szabály által aktiválható okozó tevékenység napló esemény. |
| properties.resourceId | A tevékenység napló riasztási szabály által aktiválható okozó tevékenység napló esemény az erőforrás-azonosító. |
| properties.eventTimestamp | A tevékenység eseményének tevékenység napló riasztási szabály által aktiválható okozó esemény időbélyegzője. |
| properties.operationName | A tevékenység napló riasztási szabály által aktiválható okozó tevékenység napló esemény művelet neve. |
| properties.status | A tevékenység napló riasztási szabály által aktiválható okozó tevékenység napló esemény állapotát.|

#### <a name="properties-for-metric-alerts"></a>A metrika riasztások tulajdonságai
| Elem neve | Leírás |
| --- | --- |
| properties.RuleUri | Erőforrás-azonosító a metrika riasztási szabály saját magát. |
| properties.RuleName | A metrika riasztási szabály neve. |
| properties.RuleDescription | A metrika riasztási szabály (meghatározottak szerint a riasztási szabály) leírása. |
| properties.Threshold | A küszöbérték, a metrika riasztási szabály kiértékelésekor. |
| properties.WindowSizeInMinutes | Az ablak mérete a metrika riasztási szabály kiértékelésekor. |
| properties.Aggregation | Az összesítési típusát a metrika riasztási szabályban definiált. |
| tulajdonságok. Operátor | A metrika riasztási szabály értékeléséhez feltételes operátort. |
| properties.MetricName | A metrika a metrika riasztási szabály értékeléséhez metrika neve. |
| properties.MetricUnit | A metrika egység a metrika a metrika riasztási szabály kiértékelésekor. |

## <a name="autoscale"></a>Automatikus méretezés
Ez a kategória tartalmazza a rekord a a művelethez az automatikus skálázás motor bármely adta meg az előfizetés automatikus skálázás beállításai alapján a kapcsolódó események. Az ebbe a kategóriába tartozó mutatunk be eseménytípusra példa, hogy "Automatikus skálázás felskálázott művelete nem sikerült." Használja az automatikus skálázás, automatikusan horizontális felskálázás vagy méretezni támogatott erőforrástípus található példányok száma a nap és/vagy terhelés (mérték) adatok az automatikus skálázási beállítás használatával ideje alapján. Ha a feltételek teljesül méretezése felfelé vagy lefelé a start és sikeres vagy sikertelen események rögzítése ebbe a kategóriába tartozó.

### <a name="sample-event"></a>Mintaesemény
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Tulajdonság leírása
| Elem neve | Leírás |
| --- | --- |
| hívó | Always Microsoft.Insights/autoscaleSettings |
| csatornák | Mindig "rendszergazda, a művelet" |
| Jogcímek | JSON-blob a SPN (egyszerű szolgáltatásnév), vagy az erőforrás típusát, az automatikus skálázás motor. |
| correlationId | A karakterlánc-formátum GUID azonosítója. |
| leírás |Az automatikus skálázás esemény statikus szöveges leírása. |
| eventDataId |Az automatikus skálázás esemény egyedi azonosítója. |
| szint |Az esemény szintje. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés", "Tájékoztató" és "Részletes" |
| resourceGroupName |Az automatikus skálázási beállítás az erőforráscsoport neve. |
| resourceProviderName |Az automatikus skálázási beállítás az erőforrás-szolgáltató neve. |
| resourceId |Erőforrás-azonosító automatikus skálázási beállítás. |
| operationId |Az eseményeket, amelyek megfelelnek egy művelettel között megosztott GUID. |
| operationName |A művelet neve. |
| properties |Állítsa be a `<Key, Value>` az esemény részleteit leíró párok (Ez azt jelenti, hogy szótárában). |
| properties.Description | Az automatikus skálázás motor tevékenységeit részletes leírása. |
| properties.ResourceName | Erőforrás-azonosító az érintett erőforrás (az erőforrás, amelyen a skálázási művelet végrehajtása) |
| properties.OldInstancesCount | Az automatikus skálázási művelet előtt példányok alkalmaztak. |
| properties.NewInstancesCount | Miután az automatikus skálázási művelet végrehajtásának hatása példányok száma. |
| properties.LastScaleActionTime | Ha az automatikus skálázási művelet történt időbélyegzője. |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori értékek a következők: folyamatban van, sikeres, sikertelen, aktív, a lépések megoldva. |
| a részállapot | Az automatikus skálázás általában null értékű. |
| eventTimestamp |Az esemény az esemény megfelelő a kérés feldolgozása az Azure-szolgáltatás által kiváltott idejét jelző időbélyegző. |
| submissionTimestamp |Az esemény váltak elérhetővé lekérdezése idejét jelző időbélyegző. |
| subscriptionId |Az Azure előfizetés-azonosító. |

## <a name="security"></a>Biztonság
Ez a kategória tartalmazza azt a bejegyzést, az Azure Security Center által előállított riasztások. Az ebbe a kategóriába tartozó mutatunk be eseménytípusra példa, hogy "gyanús dupla bővítményfájl végrehajtása."

### <a name="sample-event"></a>Mintaesemény
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Tulajdonság leírása
| Elem neve | Leírás |
| --- | --- |
| csatornák | Mindig "művelet" |
| correlationId | A karakterlánc-formátum GUID azonosítója. |
| leírás |A biztonsági esemény statikus szöveges leírása. |
| eventDataId |A biztonsági esemény egyedi azonosítója. |
| EventName |A biztonsági események rövid neve. |
| id |A biztonsági esemény egyedi erőforrás-azonosítója. |
| szint |Az esemény szintje. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés", "Tájékoztató" vagy a "Részletes" |
| resourceGroupName |Az erőforrás az erőforráscsoport neve. |
| resourceProviderName |Az Azure Security Center az erőforrás-szolgáltató neve. Always "Microsoft.Security". |
| resourceType |Az erőforrás által generált biztonsági esemény, például a "Microsoft.Security/locations/alerts" típusát |
| resourceId |A biztonsági figyelmeztetés erőforrás-azonosító. |
| operationId |Az eseményeket, amelyek megfelelnek egy művelettel között megosztott GUID. |
| operationName |A művelet neve. |
| properties |Állítsa be a `<Key, Value>` az esemény részleteit leíró párok (Ez azt jelenti, hogy szótárában). Ezek a Tulajdonságok biztonsági riasztás típusától függően változhat. Lásd: [ezen a lapon](../security-center/security-center-alerts-type.md) a riasztástípusokat, amelyekről a Security Center származhat leírását. |
| properties.Severity |A súlyossági szintet. A lehetséges értékek: "Felső", "Közepes" vagy "Alacsony". |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori értékek a következők: folyamatban van, sikeres, sikertelen, aktív, a lépések megoldva. |
| a részállapot | Biztonsági események rendszerint null értékű. |
| eventTimestamp |Az esemény az esemény megfelelő a kérés feldolgozása az Azure-szolgáltatás által kiváltott idejét jelző időbélyegző. |
| submissionTimestamp |Az esemény váltak elérhetővé lekérdezése idejét jelző időbélyegző. |
| subscriptionId |Az Azure előfizetés-azonosító. |

## <a name="recommendation"></a>Ajánlás
Ez a kategória bármely új ajánlást kapott, amelyek akkor jönnek létre, a szolgáltatások adatait tartalmazza. Erre példa ajánlás lehet "használata rendelkezésre állási készletek továbbfejlesztett hibatűréshez." 4 különböző típusú javaslat események létrehozható: magas rendelkezésre állás, a teljesítmény, a biztonsági és a költségek optimalizálás. 

### <a name="sample-event"></a>Mintaesemény
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Tulajdonság leírása
| Elem neve | Leírás |
| --- | --- |
| csatornák | Mindig "művelet" |
| correlationId | A karakterlánc-formátum GUID azonosítója. |
| leírás |A javaslat esemény statikus szöveges leírása |
| eventDataId | A javaslat esemény egyedi azonosítója. |
| category | Mindig "ajánlás" |
| id |A javaslat esemény egyedi erőforrás-azonosítója. |
| szint |Az esemény szintje. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés", "Tájékoztató" vagy a "Részletes" |
| operationName |A művelet neve.  Mindig "Microsoft.Advisor/generateRecommendations/action"|
| resourceGroupName |Az erőforrás az erőforráscsoport neve. |
| resourceProviderName |Az erőforrás, amelyekre ez a javaslat vonatkozik, például a "MICROSOFT.COMPUTE" az erőforrás-szolgáltató neve |
| resourceType |Az erőforrástípus neve, amelyre ez a javaslat vonatkozik, például a "MICROSOFT.COMPUTE/virtualmachines" erőforrás |
| resourceId |Az erőforrás érvényes a javaslat erőforrás-azonosító |
| status | Mindig "aktív" |
| submissionTimestamp |Az esemény váltak elérhetővé lekérdezése idejét jelző időbélyegző. |
| subscriptionId |Az Azure előfizetés-azonosító. |
| properties |Állítsa be a `<Key, Value>` az ajánlás részleteit leíró párok (Ez azt jelenti, hogy szótárában).|
| properties.recommendationSchemaVersion| A javaslat tulajdonságainak sémaverzió közzétett tevékenységnapló bejegyzésben |
| properties.recommendationCategory | A javaslat kategóriáját. Lehetséges értékek: "Magas rendelkezésre állású", "Teljesítmény", "Biztonság" és "Költség" |
| properties.recommendationImpact| A javaslat hatását. Lehetséges értékek: "Magas", "Közepes", "Alacsony" |
| properties.recommendationRisk| A javaslat kockázatát. A lehetséges értékek: "Error", "Figyelmeztetés", "None" |



## <a name="next-steps"></a>További lépések
* [További információ a tevékenységnapló (korábbi nevén naplók)](monitoring-overview-activity-logs.md)
* [Az Event hubs Azure tevékenységnapló adatfolyam](monitoring-stream-activity-logs-event-hubs.md)
