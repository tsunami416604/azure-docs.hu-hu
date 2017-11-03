---
title: "Az Azure műveletnapló esemény séma |} Microsoft Docs"
description: "Az esemény-sémát a tevékenység naplóba kibocsátott adatokról ismertetése"
author: johnkemnetz
manager: robb
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: johnkem
ms.openlocfilehash: 91129da9ef7791a506292d9e13e386a25ee341a8
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2017
---
# <a name="azure-activity-log-event-schema"></a>Azure tevékenységnapló esemény séma
A **Azure tevékenységnapló** , amely bármely történt az Azure-előfizetés szintű események betekintést biztosít a naplót. Ez a cikk ismerteti az adatok kategóriánként esemény séma.

## <a name="administrative"></a>Felügyeleti
Ez a kategória tartalmazza az összes rekord létrehozása, update, delete és művelet műveleteket Resource Manageren keresztül. Milyen típusú mutatunk be, ebbe a kategóriába tartozó eseményeket például "virtuális gép létrehozása" és "hálózati biztonsági csoport törlése" minden felhasználó vagy alkalmazás erőforrás-kezelő használatával végrehajtott műveletet egy adott erőforrástípus művelet van modellezve. Ha a művelet típusa írási, a Delete és a művelet, a rekordokat a kezdő és a sikeres vagy sikertelen lesz, hogy a művelet tárolja, amely a felügyeleti kategória. A felügyeleti kategória is a szerepköralapú hozzáférés-vezérlést módosításokat egy előfizetésben.

### <a name="sample-event"></a>Mintaesemény
```json
{
  "authorization": {
    "action": "microsoft.support/supporttickets/write",
    "role": "Subscription Admin",
    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
  },
  "caller": "admin@contoso.com",
  "channels": "Operation",
  "claims": {
    "aud": "https://management.core.windows.net/",
    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
    "iat": "1421876371",
    "nbf": "1421876371",
    "exp": "1421880271",
    "ver": "1.0",
    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
    "puid": "20030000801A118C",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
    "name": "John Smith",
    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
    "appidacr": "2",
    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
    "http://schemas.microsoft.com/claims/authnclassreference": "1"
  },
  "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "description": "",
  "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
  "eventName": {
    "value": "EndRequest",
    "localizedValue": "End request"
  },
  "httpRequest": {
    "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
    "clientIpAddress": "192.168.35.115",
    "method": "PUT"
  },
  "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
  "level": "Informational",
  "resourceGroupName": "MSSupportGroup",
  "resourceProviderName": {
    "value": "microsoft.support",
    "localizedValue": "microsoft.support"
  },
  "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
  "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "operationName": {
    "value": "microsoft.support/supporttickets/write",
    "localizedValue": "microsoft.support/supporttickets/write"
  },
  "properties": {
    "statusCode": "Created"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": "Created",
    "localizedValue": "Created (HTTP Status Code: 201)"
  },
  "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
  "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
  "subscriptionId": "s1"
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
| erőforráscsoport-név |Az érintett erőforrás az erőforráscsoport neve. |
| resourceProviderName |Az erőforrás-szolgáltató az érintett erőforrás neve |
| resourceId |Erőforrás-azonosító az érintett erőforrás. |
| OperationID azonosítójú |Az eseményeket, amelyek megfelelnek egy művelettel között megosztott GUID. |
| operationName |A művelet neve. |
| properties |Állítsa be a `<Key, Value>` az esemény részleteit leíró párok (Ez azt jelenti, hogy szótárában). |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori értékek a következők: folyamatban van, sikeres, sikertelen, aktív, a lépések megoldva. |
| a részállapot |Általában a megfelelő REST HTTP-állapotkód: hívja, de más a részállapot, például a gyakori értékek leíró karakterláncok is használható: OK (HTTP-állapotkód:: 200), létrehozott (HTTP-állapotkód:: 201-es), fogadja el (HTTP-állapotkód:: 202), nem tartalom (HTTP-állapotkód: 204), hibás kérés (HTTP-állapotkód:: 400), nem található (HTTP-állapotkód: 404-es), ütközés (HTTP-állapotkód: : 409), belső kiszolgálóhiba (HTTP-állapotkód: 500), a Service nem érhető el (HTTP-állapotkód: 503-as), átjáró időtúllépése (HTTP-állapotkód: 504). |
| eventTimestamp |Az esemény az esemény megfelelő a kérés feldolgozása az Azure-szolgáltatás által kiváltott idejét jelző időbélyegző. |
| submissionTimestamp |Az esemény váltak elérhetővé lekérdezése idejét jelző időbélyegző. |
| subscriptionId |Az Azure előfizetés-azonosító. |

## <a name="service-health"></a>Szolgáltatás állapota
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
  "id": "/subscriptions/mySubscriptionID/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
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
  "resourceId": "/subscriptions/mySubscriptionID",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "mySubscriptionID",
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

### <a name="property-descriptions"></a>Tulajdonság leírása
Elem neve | Leírás
-------- | -----------
csatornák | A következő értékek egyike: "Rendszergazda", "Művelet"
correlationId | Az általában egy GUID, amely a karakterlánc-formátum. Ugyanaz a uber művelet tartozik, amely az események az azonos correlationId általában megosztani.
leírás | Az esemény leírása.
eventDataId | Az esemény egyedi azonosítója.
EventName | Az esemény címe.
szint | Az esemény szintje. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés", "Tájékoztató" és "Részletes"
resourceProviderName | Az érintett erőforrás az erőforrás-szolgáltató neve. Ha nem ismeri, ez lehet null.
a resourceType| Az erőforrás az érintett erőforrás típusát. Ha nem ismeri, ez lehet null.
a részállapot | A szolgáltatás állapotával kapcsolatos események általában null értékű.
eventTimestamp | A eseményének generált és a küldve a műveletnapló idejét jelző időbélyegző.
submissionTimestamp |   Az esemény váltak elérhetővé a műveletnaplóban idejét jelző időbélyegző.
subscriptionId | Az Azure-előfizetés, amelyben ez az esemény naplózásának.
status | A művelet állapotát leíró karakterlánc. Néhány gyakori értékek a következők: aktív, a megoldott.
operationName | A művelet neve. Általában Microsoft.ServiceHealth/incident/action.
category | "ServiceHealth"
resourceId | Erőforrás-azonosító az érintett erőforrás, ha ismert. Ellenkező esetben van megadva előfizetés-azonosító.
Properties.Title | Ez a kommunikáció honosított címét. Angol az alapértelmezett nyelv.
Properties.Communication | A HTML-kódot szolgáltatással való kommunikációhoz honosított részleteit. Angol az alapértelmezett beállítás.
Properties.incidentType | A lehetséges értékek: AssistedRecovery, ActionRequired, információk, incidens, karbantartási, biztonsági
Properties.trackingId | Ez az esemény társítva van az incidens azonosítja. Ezzel az incidensekhez kapcsolódó eseményeket összefüggéseket.
Properties.impactedServices | Az escape-karakterrel megjelölt JSON-blob a szolgáltatások és az incidens által érintett területek leíró. Szolgáltatások listáját, amelyek mindegyike rendelkezik egy szolgáltatásnév és ImpactedRegions, amelyek mindegyikének a RegionName listáját.
Properties.defaultLanguageTitle | A kommunikáció, angol nyelven
Properties.defaultLanguageContent | A kommunikáció vagy a html-kódot, vagy az egyszerű szöveg angol nyelven
Properties.Stage | AssistedRecovery, a ActionRequired, a információkat, az incidensek, a biztonsági a lehetséges értékek: aktív, amelyek megoldva. A karbantartás vannak: aktív, tervezett, esetbejegyzések, visszavonva, Rescheduled, megoldva, kész
Properties.communicationId | A kommunikáció ezt az eseményt hozzá rendelve.

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
  "correlationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
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
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
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
  "subscriptionId": "mySubscriptionID"
}
```

### <a name="property-descriptions"></a>Tulajdonság leírása
| Elem neve | Leírás |
| --- | --- |
| hívó | Mindig Microsoft.Insights/alertRules |
| csatornák | Mindig "rendszergazda, a művelet" |
| Jogcímek | JSON-blob a SPN (egyszerű szolgáltatásnév), vagy az erőforrás típusát, a riasztás motor. |
| correlationId | A karakterlánc-formátum GUID azonosítója. |
| leírás |A figyelmeztetési esemény statikus szöveges leírása. |
| eventDataId |A riasztási eseménynek egyedi azonosítója. |
| szint |Az esemény szintje. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés", "Tájékoztató" és "Részletes" |
| erőforráscsoport-név |Ha egy metrika riasztás az érintett erőforrás az erőforráscsoport neve. Más riasztástípusok ettől esetén ez a riasztás saját magát tartalmazó erőforráscsoport nevét. |
| resourceProviderName |Ha egy metrika riasztás az érintett erőforrás az erőforrás-szolgáltató neve. A más típusú Ez az erőforrás-szolgáltató magát a riasztás nevét. |
| resourceId | Az erőforrás-azonosító a metrika riasztások esetén érintett erőforrás neve. A más típusú Ez az erőforrás-azonosítója, a riasztás erőforrás magát. |
| OperationID azonosítójú |Az eseményeket, amelyek megfelelnek egy művelettel között megosztott GUID. |
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
| Properties.status | A tevékenység napló riasztási szabály által aktiválható okozó tevékenység napló esemény állapotát.|

#### <a name="properties-for-metric-alerts"></a>A metrika riasztások tulajdonságai
| Elem neve | Leírás |
| --- | --- |
| tulajdonságok. RuleUri | Erőforrás-azonosító a metrika riasztási szabály saját magát. |
| tulajdonságok. Szabálynév | A metrika riasztási szabály neve. |
| tulajdonságok. RuleDescription | A metrika riasztási szabály (meghatározottak szerint a riasztási szabály) leírása. |
| tulajdonságok. Küszöbérték | A küszöbérték, a metrika riasztási szabály kiértékelésekor. |
| tulajdonságok. WindowSizeInMinutes | Az ablak mérete a metrika riasztási szabály kiértékelésekor. |
| tulajdonságok. Összesítés | Az összesítési típusát a metrika riasztási szabályban definiált. |
| tulajdonságok. Operátor | A metrika riasztási szabály értékeléséhez feltételes operátort. |
| tulajdonságok. MetricName | A metrika a metrika riasztási szabály értékeléséhez metrika neve. |
| tulajdonságok. MetricUnit | A metrika egység a metrika a metrika riasztási szabály kiértékelésekor. |

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
  "description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
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
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
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
  "subscriptionId": "mySubscriptionID"
}

```

### <a name="property-descriptions"></a>Tulajdonság leírása
| Elem neve | Leírás |
| --- | --- |
| hívó | Mindig Microsoft.Insights/autoscaleSettings |
| csatornák | Mindig "rendszergazda, a művelet" |
| Jogcímek | JSON-blob a SPN (egyszerű szolgáltatásnév), vagy az erőforrás típusát, az automatikus skálázás motor. |
| correlationId | A karakterlánc-formátum GUID azonosítója. |
| leírás |Az automatikus skálázás esemény statikus szöveges leírása. |
| eventDataId |Az automatikus skálázás esemény egyedi azonosítója. |
| szint |Az esemény szintje. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés", "Tájékoztató" és "Részletes" |
| erőforráscsoport-név |Az automatikus skálázási beállítás az erőforráscsoport neve. |
| resourceProviderName |Az automatikus skálázási beállítás az erőforrás-szolgáltató neve. |
| resourceId |Erőforrás-azonosító automatikus skálázási beállítás. |
| OperationID azonosítójú |Az eseményeket, amelyek megfelelnek egy művelettel között megosztott GUID. |
| operationName |A művelet neve. |
| properties |Állítsa be a `<Key, Value>` az esemény részleteit leíró párok (Ez azt jelenti, hogy szótárában). |
| tulajdonságok. Leírás | Az automatikus skálázás motor tevékenységeit részletes leírása. |
| tulajdonságok. ResourceName | Erőforrás-azonosító az érintett erőforrás (az erőforrás, amelyen a skálázási művelet végrehajtása) |
| tulajdonságok. OldInstancesCount | Az automatikus skálázási művelet előtt példányok alkalmaztak. |
| tulajdonságok. NewInstancesCount | Miután az automatikus skálázási művelet végrehajtásának hatása példányok száma. |
| tulajdonságok. LastScaleActionTime | Ha az automatikus skálázási művelet történt időbélyegzője. |
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
    "id": "/subscriptions/d4742bb8-c279-4903-9653-9858b17d0c2e/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
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
    "resourceId": "/subscriptions/d4742bb8-c279-4903-9653-9858b17d0c2e/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "d4742bb8-c279-4903-9653-9858b17d0c2e",
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
| erőforráscsoport-név |Az erőforrás az erőforráscsoport neve. |
| resourceProviderName |Az Azure Security Center az erőforrás-szolgáltató neve. Mindig "Microsoft.Security." |
| a resourceType |Az erőforrás által generált biztonsági esemény, például a "Microsoft.Security/locations/alerts" típusát |
| resourceId |A biztonsági figyelmeztetés erőforrás-azonosító. |
| OperationID azonosítójú |Az eseményeket, amelyek megfelelnek egy művelettel között megosztott GUID. |
| operationName |A művelet neve. |
| properties |Állítsa be a `<Key, Value>` az esemény részleteit leíró párok (Ez azt jelenti, hogy szótárában). Ezek a Tulajdonságok biztonsági riasztás típusától függően változhat. Lásd: [ezen a lapon](../security-center/security-center-alerts-type.md) a riasztástípusokat, amelyekről a Security Center származhat leírását. |
| tulajdonságok. Súlyosság: |A súlyossági szintet. A lehetséges értékek: "Felső", "Közepes" vagy "Alacsony". |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori értékek a következők: folyamatban van, sikeres, sikertelen, aktív, a lépések megoldva. |
| a részállapot | Biztonsági események rendszerint null értékű. |
| eventTimestamp |Az esemény az esemény megfelelő a kérés feldolgozása az Azure-szolgáltatás által kiváltott idejét jelző időbélyegző. |
| submissionTimestamp |Az esemény váltak elérhetővé lekérdezése idejét jelző időbélyegző. |
| subscriptionId |Az Azure előfizetés-azonosító. |

## <a name="next-steps"></a>Következő lépések
* [További információ a tevékenységnapló (korábbi nevén naplók)](monitoring-overview-activity-logs.md)
* [Az Event hubs Azure tevékenységnapló adatfolyam](monitoring-stream-activity-logs-event-hubs.md)
