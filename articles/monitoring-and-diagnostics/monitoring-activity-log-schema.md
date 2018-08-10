---
title: Az Azure tevékenységnapló eseménysémája
description: Az esemény-sémát, a tevékenységnapló kibocsátott adatokról a ismertetése
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 4/12/2018
ms.author: dukek
ms.component: activitylog
ms.openlocfilehash: 9c1f4699f067ece3108813d28ff834c68f44316d
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003831"
---
# <a name="azure-activity-log-event-schema"></a>Az Azure tevékenységnapló eseménysémája
A **Azure-tevékenységnapló** , amely bármely Azure-ban bekövetkezett előfizetés-szintű eseményeit betekintést nyújt a bejelentkezés. Ez a cikk ismerteti a eseménysémája egy adatkategóriát. Az adatok sémája eltér attól függően, ha az adatok a portal, PowerShell, CLI-t, vagy közvetlenül a REST API és a segítségével olvas [streamelési adatok a storage vagy az Event Hubs használatával egy Naplóprofil](./monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). Az alábbi példák a séma szerint a portal, PowerShell, CLI és REST API-n keresztül elérhetővé tett. Ezen tulajdonságok leképezése a [Azure diagnosztikai naplók séma](./monitoring-diagnostic-logs-schema.md) van megadva a cikk végén található.

## <a name="administrative"></a>Adminisztratív
A kategória tartalmazza az összes rekordot létrehozni, frissítési, törlési és műveleti műveleteket hajtja végre a Resource Manageren keresztül. Milyen típusú itt jelennének meg ebbe a kategóriába tartozó eseményeket például a "virtuális gép létrehozása" és "hálózati biztonsági csoport törlése" minden felhasználó vagy alkalmazás használatával a Resource Manager által végrehajtott műveletek az egyes erőforrástípusok műveletként van modellezve. Ha a művelet típusa, Write, Delete vagy műveletet, a rekordokat a kezdő és a sikeres vagy sikertelen a művelet rögzítve lesznek a felügyeleti kategória. A felügyeleti kategória is módosítania kellene a szerepköralapú hozzáférés-vezérlés az előfizetéshez.

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

### <a name="property-descriptions"></a>A tulajdonság leírása
| Elem neve | Leírás |
| --- | --- |
| Engedélyezési |Az esemény tulajdonságainak RBAC-blobját. Általában tartalmazza az "action", "szerepkör" és "hatókör" tulajdonság. |
| hívó |A művelet, egyszerű Felhasználónévi jogcím vagy egyszerű szolgáltatásnév jogcím alapján a rendelkezésre állási végző felhasználó e-mail címe. |
| csatornák |A következő értékek egyikét: "Admin", "Művelet" |
| jogcímek |A JWT jogkivonat a felhasználó vagy az alkalmazásnak, hogy ezt a műveletet az erőforrás-kezelőben hitelesítéséhez az Active Directory által használandó. |
| correlationId |Általában egy GUID Azonosítót a karakterláncként. Ugyanaz a uber művelet eseményeket, amelyek megosztása a korrelációs azonosító tartozik. |
| leírás |Statikus szöveg egy esemény leírása. |
| eventDataId |Az esemény egyedi azonosítója. |
| Törzsparaméterei |A Http-kérelem leíró BLOB. Általában tartalmazza a "ügyfélkérelem", "clientIpAddress" és "method" (HTTP-metódust. For example, PUT). |
| szint |Az esemény szintjét. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés" és "Tájékoztatási szintű" |
| resourceGroupName |Az érintett erőforrás az erőforráscsoport neve. |
| erőforrás-szolgáltató neve |Az érintett erőforrás az erőforrás-szolgáltató neve |
| resourceId |Erőforrás-azonosító az érintett erőforrás. |
| operationId |Az események, amelyek megfelelnek egy műveletre alkalmaztam között megosztott GUID. |
| operationName |A művelet neve. |
| properties |Állítsa be a `<Key, Value>` párok (azaz egy szótárban), az esemény részleteit leíró. |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori értékek a következők: elindítva, folyamatban, sikeres, sikertelen, aktív, megoldva. |
| a részállapot |Általában a megfelelő REST HTTP-állapotkódot hívja, de más egy substatus, például az alábbi általános értékeket leíró karakterláncban is használható: OK (HTTP-állapotkód: 200) létrehozott (HTTP-állapotkód: 201-es) elfogadott, (HTTP-állapotkód: 202), nincs tartalom (HTTP-állapot Kód: 204), hibás kérelem (HTTP-állapotkód: 400), nem található (HTTP-állapotkód: 404-es), ütközés (HTTP-állapotkód: 409), belső kiszolgálóhiba (HTTP-állapotkód: 500), a Service nem érhető el (HTTP-állapotkód: 503-as), átjáró időtúllépése (HTTP-állapotkód: 504). |
| eventTimestamp |Időbélyeg, ha az esemény jött létre az Azure-szolgáltatás a megfelelő esemény kérelem feldolgozása. |
| submissionTimestamp |Időbélyeg, amikor az eseményt vált elérhetővé a lekérdezéséhez. |
| subscriptionId |Az Azure előfizetés-azonosítójára. |

## <a name="service-health"></a>Szolgáltatások állapota
Ebben a kategóriában a service health az Azure-ban bekövetkezett események rekordot tartalmaz. Itt jelennének meg ebbe a kategóriába tartozó esemény típusa például a "SQL Azure, az USA keleti régiójában üzemen kívül van." A Szolgáltatásállapot-események az öt fajta származnak: szükséges művelet, eszközkapcsolat helyreállítási, incidens, karbantartási, adatokat vagy biztonsági, és csak jelenik meg, ha egy erőforráshoz lenne hatással lehet az esemény-előfizetés.

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
Tekintse meg a [szolgáltatás állapotára vonatkozó értesítések](./monitoring-service-notifications.md) dokumentációjában, a tulajdonságok értékeit ismertető cikket.

## <a name="alert"></a>Riasztás
Ez a kategória összes aktiválás az Azure-riasztások rekordot tartalmaz. Itt jelennének meg ebbe a kategóriába tartozó esemény típusa például a "százalékos processzorhasználatról a myVM már több mint 80-as az elmúlt 5 percben." Azure rendszerek különböző rendelkezik egy riasztási elv – valamilyen szabály meghatározása, és értesítést kaphat, ha a feltétel egyezik az szabályokat. Minden alkalommal, amikor egy támogatott Azure riasztástípus "aktiválja,", vagy értesítést hozhat létre, hogy a feltételek teljesülnek, egy rekord, az aktiválás szintén leküldéssel ebbe a kategóriába, a tevékenységnapló.

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

### <a name="property-descriptions"></a>A tulajdonság leírása
| Elem neve | Leírás |
| --- | --- |
| hívó | Always Microsoft.Insights/alertRules |
| csatornák | Mindig "rendszergazda, a művelet" |
| jogcímek | Az egyszerű szolgáltatásnév (szolgáltatásnév), vagy az erőforrás típusa, a riasztás motor JSON-blobját. |
| correlationId | GUID, amely a karakterlánc-formátum. |
| leírás |A figyelmeztetési esemény statikus szöveges leírása. |
| eventDataId |A figyelmeztetési esemény egyedi azonosítója. |
| szint |Az esemény szintjét. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés" és "Tájékoztatási szintű" |
| resourceGroupName |Az érintett erőforrás metrikariasztás esetén az erőforráscsoport neve. Más riasztástípusok esetén, amely tartalmazza a riasztás magát az erőforráscsoport nevét. |
| erőforrás-szolgáltató neve |Metrikariasztás esetén az érintett erőforrás az erőforrás-szolgáltató neve. Más riasztástípusok esetén ez a riasztás magát az erőforrás-szolgáltató nevét. |
| resourceId | Metrikariasztás esetén az érintett erőforrás erőforrás-azonosító neve. Más riasztástípusok esetén ez a riasztás erőforrásán erőforrás-Azonosítóját. |
| operationId |Az események, amelyek megfelelnek egy műveletre alkalmaztam között megosztott GUID. |
| operationName |A művelet neve. |
| properties |Állítsa be a `<Key, Value>` párok (azaz egy szótárban), az esemény részleteit leíró. |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori értékek a következők: elindítva, folyamatban, sikeres, sikertelen, aktív, megoldva. |
| a részállapot | Általában a null riasztások esetén. |
| eventTimestamp |Időbélyeg, ha az esemény jött létre az Azure-szolgáltatás a megfelelő esemény kérelem feldolgozása. |
| submissionTimestamp |Időbélyeg, amikor az eseményt vált elérhetővé a lekérdezéséhez. |
| subscriptionId |Az Azure előfizetés-azonosítójára. |

### <a name="properties-field-per-alert-type"></a>Tulajdonságok mező riasztási típus szerint
A Tulajdonságok mező tartalmazni fog a figyelmeztetési esemény forrását függően különböző értékeket. Két gyakori riasztási eseménynek szolgáltatók a következők: tevékenységnapló-riasztások és metrikákhoz kapcsolódó riasztások.

#### <a name="properties-for-activity-log-alerts"></a>A tevékenységnapló-riasztások tulajdonságai
| Elem neve | Leírás |
| --- | --- |
| properties.subscriptionId | Az előfizetés-azonosító, a tevékenység riasztási szabály, amelyet aktiválnia kell az okozza, amely a tevékenységnaplóbeli esemény. |
| properties.eventDataId | Az esemény adatok Azonosítót a tevékenységnaplóbeli esemény a tevékenység riasztási szabály, amelyet aktiválnia kell az okozza, amely a. |
| properties.resourceGroup | Az erőforráscsoport, a tevékenység riasztási szabály, amelyet aktiválnia kell az okozza, amely a tevékenységnaplóbeli esemény. |
| properties.resourceId | Az erőforrás-azonosító, a tevékenység riasztási szabály, amelyet aktiválnia kell az okozza, amely a tevékenységnaplóbeli esemény. |
| properties.eventTimestamp | A tevékenység riasztási szabály, amelyet aktiválnia kell az okozza, amely a tevékenységnaplóbeli esemény esemény időbélyegzője. |
| properties.operationName | A tevékenységnapló eseményéhez a tevékenység riasztási szabály, amelyet aktiválnia kell az okozza, amely a művelet neve. |
| properties.status | Az a tevékenység riasztási szabály, amelyet aktiválnia kell az okozza, amely a tevékenységnaplóbeli esemény állapota.|

#### <a name="properties-for-metric-alerts"></a>A metrikai riasztások tulajdonságai
| Elem neve | Leírás |
| --- | --- |
| properties.RuleUri | Erőforrás-azonosító a metrikaalapú riasztási szabály magát. |
| properties.RuleName | A metrikaalapú riasztási szabály neve. |
| properties.RuleDescription | A metrikaalapú riasztási szabály (mint a riasztási szabályban meghatározott) leírása. |
| properties.Threshold | A küszöbérték, a metrikaalapú riasztási szabály kiértékelésekor. |
| properties.WindowSizeInMinutes | Az ablak mérete a metrikaalapú riasztási szabály kiértékelésekor. |
| properties.Aggregation | Az összesítés típusa a metrikaalapú riasztási szabály definiálva. |
| tulajdonságok. Operátor | A metrikaalapú riasztási szabály kiértékelésekor feltételes operátort. |
| properties.MetricName | A mérőszám a a metrikaalapú riasztási szabály értékelésére használt metrika neve. |
| properties.MetricUnit | A mérőszám a a metrikaalapú riasztási szabály értékelésére használt metrikus egység. |

## <a name="autoscale"></a>Automatikus méretezés
Ez a kategória tartalmazza a rekord a meghatározott az előfizetés automatikus skálázási beállítások alapján automatikus skálázási motor a művelethez kapcsolódó események. Itt jelennének meg ebbe a kategóriába tartozó esemény típusa például a "Automatikus vertikális felskálázási művelet sikertelen volt." Használja az automatikus méretezés, akkor lehet automatikusan horizontálisan felskálázhatja vagy leskálázhatja a támogatott erőforrástípus a példányok számának napi és/vagy terhelési () metrikaadatok egy automatikus skálázási beállítás használatával ideje alapján. Ha a feltételek teljesülnek a méretezési csoport felfelé vagy lefelé a kezdő és sikeres vagy sikertelen események rögzítése ebbe a kategóriába.

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

### <a name="property-descriptions"></a>A tulajdonság leírása
| Elem neve | Leírás |
| --- | --- |
| hívó | Always Microsoft.Insights/autoscaleSettings |
| csatornák | Mindig "rendszergazda, a művelet" |
| jogcímek | Az egyszerű szolgáltatásnév (szolgáltatásnév), vagy az erőforrás típusa, az automatikus skálázási motor JSON-blobját. |
| correlationId | GUID, amely a karakterlánc-formátum. |
| leírás |Az automatikus skálázási esemény statikus szöveges leírása. |
| eventDataId |Az automatikus skálázási esemény egyedi azonosítója. |
| szint |Az esemény szintjét. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés" és "Tájékoztatási szintű" |
| resourceGroupName |Az automatikus skálázási beállítás az erőforráscsoport neve. |
| erőforrás-szolgáltató neve |Az automatikus skálázási beállítás erőforrás-szolgáltató neve. |
| resourceId |Automatikus skálázási beállítás erőforrás-azonosító. |
| operationId |Az események, amelyek megfelelnek egy műveletre alkalmaztam között megosztott GUID. |
| operationName |A művelet neve. |
| properties |Állítsa be a `<Key, Value>` párok (azaz egy szótárban), az esemény részleteit leíró. |
| properties.Description | Az automatikus skálázási motor mire részletes leírása. |
| properties.ResourceName | Az érintett erőforrás erőforrás-azonosító (az erőforrás, amelyen a skálázási műveletek végrehajtása) |
| properties.OldInstancesCount | Az automatikus skálázási művelet előtt példányok érvénybe vett igénybe. |
| properties.NewInstancesCount | Az automatikus skálázási művelet után példányok érvénybe vett igénybe. |
| properties.LastScaleActionTime | Az időbélyeg, ha az automatikus skálázási művelet történt. |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori értékek a következők: elindítva, folyamatban, sikeres, sikertelen, aktív, megoldva. |
| a részállapot | Az automatikus skálázás általában null értékű. |
| eventTimestamp |Időbélyeg, ha az esemény jött létre az Azure-szolgáltatás a megfelelő esemény kérelem feldolgozása. |
| submissionTimestamp |Időbélyeg, amikor az eseményt vált elérhetővé a lekérdezéséhez. |
| subscriptionId |Az Azure előfizetés-azonosítójára. |

## <a name="security"></a>Biztonság
Ez a kategória tartalmazza azt a rekordot, bármely Azure Security Center által létrehozott riasztások. Itt jelennének meg ebbe a kategóriába tartozó esemény típusa például a "a program kettős kiterjesztésű gyanús fájlt futtatott."

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

### <a name="property-descriptions"></a>A tulajdonság leírása
| Elem neve | Leírás |
| --- | --- |
| csatornák | Mindig "művelet" |
| correlationId | GUID, amely a karakterlánc-formátum. |
| leírás |A biztonsági események statikus szöveges leírása. |
| eventDataId |A biztonsági esemény egyedi azonosítója. |
| EventName |A biztonsági események rövid neve. |
| id |A biztonsági események egyedi erőforrás-azonosítója. |
| szint |Az esemény szintjét. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés" vagy "Tájékoztatási szintű" |
| resourceGroupName |Az erőforrás az erőforráscsoport neve. |
| erőforrás-szolgáltató neve |Az Azure Security Center erőforrás-szolgáltató neve. Always "Microsoft.Security". |
| resourceType |A biztonsági esemény, például a "Microsoft.Security/locations/alerts" létrehozott erőforrás típusa |
| resourceId |A biztonsági riasztás erőforrás-azonosító. |
| operationId |Az események, amelyek megfelelnek egy műveletre alkalmaztam között megosztott GUID. |
| operationName |A művelet neve. |
| properties |Állítsa be a `<Key, Value>` párok (azaz egy szótárban), az esemény részleteit leíró. Ezek a Tulajdonságok biztonsági riasztás típusától függően változnak. Lásd: [ezt oldal](../security-center/security-center-alerts-type.md) a típusú riasztásokat, amelyeket a Security Center leírását. |
| properties.Severity |A súlyossági szintet. A lehetséges értékek: "Nagy", "Közepes" vagy "Alacsony". |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori értékek a következők: elindítva, folyamatban, sikeres, sikertelen, aktív, megoldva. |
| a részállapot | Biztonsági események rendszerint null értékű. |
| eventTimestamp |Időbélyeg, ha az esemény jött létre az Azure-szolgáltatás a megfelelő esemény kérelem feldolgozása. |
| submissionTimestamp |Időbélyeg, amikor az eseményt vált elérhetővé a lekérdezéséhez. |
| subscriptionId |Az Azure előfizetés-azonosítójára. |

## <a name="recommendation"></a>Ajánlás
Ez a kategória tartalmazza a rekord az új javaslatok, amelyek akkor jönnek létre, a szolgáltatások. Egy javaslat lehet például "rendelkezésre állási csoportok használata a hibatűrő képesség javítása érdekében." Javaslattételi esemény létrehozható 4 típusa van: magas rendelkezésre állás, teljesítmény, biztonság és költségek optimalizálása. 

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
### <a name="property-descriptions"></a>A tulajdonság leírása
| Elem neve | Leírás |
| --- | --- |
| csatornák | Mindig "művelet" |
| correlationId | GUID, amely a karakterlánc-formátum. |
| leírás |Az ajánlás esemény statikus szöveges leírása |
| eventDataId | Az ajánlás esemény egyedi azonosítója. |
| category | Mindig "javaslat" |
| id |Az ajánlás esemény egyedi erőforrás-azonosítója. |
| szint |Az esemény szintjét. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés" vagy "Tájékoztatási szintű" |
| operationName |A művelet neve.  Mindig "Microsoft.Advisor/generateRecommendations/action"|
| resourceGroupName |Az erőforrás az erőforráscsoport neve. |
| erőforrás-szolgáltató neve |Az erőforrás-szolgáltató, amely a javaslat vonatkozik, például a "MICROSOFT.COMPUTE" erőforrás neve |
| resourceType |Az erőforrás, amely a javaslat vonatkozik, például a "MICROSOFT.COMPUTE/virtualmachines" az erőforrás típusának neve |
| resourceId |Az erőforrás, amelyekre vonatkozik a javaslat erőforrás-azonosító |
| status | Mindig "aktív" |
| submissionTimestamp |Időbélyeg, amikor az eseményt vált elérhetővé a lekérdezéséhez. |
| subscriptionId |Az Azure előfizetés-azonosítójára. |
| properties |Állítsa be a `<Key, Value>` párok (azaz egy szótárban), az ajánlás részleteit leíró.|
| properties.recommendationSchemaVersion| A tevékenységnapló-bejegyzés közzé a javaslat tulajdonságainak sémaverzió |
| properties.recommendationCategory | Az ajánlás kategóriáját. Lehetséges értékek: "Magas rendelkezésre állású", "Teljesítmény", "Security" és "Cost" |
| properties.recommendationImpact| Az ajánlás hatását. A lehetséges értékek: "Nagy", "Közepes", "Alacsony" |
| properties.recommendationRisk| Az ajánlás kockázatát. A lehetséges értékek: "Hiba", "Figyelmeztetés", "None" |

## <a name="mapping-to-diagnostic-logs-schema"></a>Diagnosztikai naplók séma leképezése

Az Azure-tevékenységnapló egy tárfiókhoz vagy az Event Hubs-névtér közvetítésekor követi-e az adatokat a [Azure diagnosztikai naplók séma](./monitoring-diagnostic-logs-schema.md). A következő tulajdonságokat a fenti séma a diagnosztikai naplók séma hozzárendelése:

| Diagnosztikai naplók sématulajdonság | Tevékenység Log REST API sématulajdonság | Megjegyzések |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | előfizetés-azonosító, a resourceType, resourceGroupName vannak az összes fióktól vette a beállításait az erőforrás-azonosító. |
| operationName | operationName.value |  |
| category | Művelet neve része | Kiscsoportos művelet típusa – "Írás" / "Törlés" / "Action" |
| resultType | Status.Value | |
| resultSignature | substatus.Value | |
| resultDescription | leírás |  |
| durationMs | – | Mindig 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | jogcímek és engedélyezési tulajdonságai |  |
| Szint | Szint |  |
| location | – | A hely, ahol az esemény feldolgozása. *Ez nem az erőforrás helyét, de ahelyett, az esemény feldolgozása. Ez a tulajdonság egy következő frissítés törlődni fog.* |
| Tulajdonságok | properties.eventProperties |  |
| properties.eventCategory | category | Ha nem található properties.eventCategory, kategória-e a "Felügyeleti" |
| properties.eventName | EventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |


## <a name="next-steps"></a>További lépések
* [További információ a tevékenységnapló (korábbi nevén Auditnaplók)](monitoring-overview-activity-logs.md)
* [Az Event hubs Azure tevékenységnapló Stream](monitoring-stream-activity-logs-event-hubs.md)
