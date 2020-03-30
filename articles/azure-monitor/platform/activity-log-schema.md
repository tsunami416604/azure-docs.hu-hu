---
title: Az Azure-tevékenységnapló eseménysémája
description: Az Azure-tevékenységnaplóban az egyes kategóriák eseménysémáját ismerteti.
author: bwren
services: azure-monitor
ms.topic: reference
ms.date: 12/04/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c2f171c79423e0cfe8b57c05b8248679f9ada9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472741"
---
# <a name="azure-activity-log-event-schema"></a>Az Azure-tevékenységnapló eseménysémája
Az [Azure-tevékenységnapló](platform-logs-overview.md) betekintést nyújt az Azure-ban előforduló előfizetési szintű eseményekbe. Ez a cikk az egyes kategóriák eseménysémáját ismerteti. 

Az alábbi példák a sémát mutatják be, amikor a portálról, a PowerShellből, a CLI-ből és a REST API-ból éri el a tevékenységnaplót. A séma eltérő, amikor [a tevékenységnaplót tárolóba vagy eseményközpontokba továbbítja.](resource-logs-stream-event-hubs.md) A tulajdonságok hozzárendelése az [erőforrásnaplók sémájához](diagnostic-logs-schema.md) a cikk végén található.

## <a name="administrative"></a>Adminisztratív
Ez a kategória az Erőforrás-kezelőn keresztül végrehajtott összes létrehozási, frissítési, törlési és műveletművelet rekordját tartalmazza. Az ebben a kategóriában látható események példái közé tartozik a "virtuális gép létrehozása" és a "hálózati biztonsági csoport törlése" Minden olyan művelet, amelyet egy felhasználó vagy alkalmazás az Erőforrás-kezelő használatával tett, egy adott erőforrástípus műveleteként van modellezve. Ha a művelet típusa Írás, Törlés vagy Művelet, a művelet kezdetének és sikeressének rekordjai a Felügyeleti kategóriában kerülnek rögzítésre. A Felügyeleti kategória az előfizetés szerepköralapú hozzáférés-vezérlésének módosításait is tartalmazza.

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
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.microsoft.com/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
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

### <a name="property-descriptions"></a>Tulajdonság leírások
| Elem neve | Leírás |
| --- | --- |
| engedélyezés |Az esemény RBAC-tulajdonságainak blobja. Általában tartalmazza a "művelet", "szerep" és a "hatókör" tulajdonságokat. |
| Hívó |A műveletet, az upn-jogcímeket vagy az SPN-jogcímet a rendelkezésre állást elvégző felhasználó e-mail-címe. |
| Csatornák |A következő értékek egyike: "Admin", "Operation" |
| Azt állítja |Az Active Directory által a felhasználó vagy alkalmazás által a művelet Resource Managerben történő végrehajtásához használt JWT-jogkivonat. |
| correlationId |Általában a GUID a karakterlánc formátumban. A korrelációsazonosítóval megegyező események ugyanahhoz az überművelethez tartoznak. |
| leírás |Egy esemény statikus szöveges leírása. |
| eventDataId |Egy esemény egyedi azonosítója |
| eventName | A felügyeleti esemény rövid neve. |
| category | Mindig "adminisztratív" |
| httpRequest (Kérés) |A Http-kérést leíró blob. Általában tartalmazza a "clientRequestId", "clientIpAddress" és "method" (HTTP metódus). Például PUT). |
| szint |Az esemény szintje. A következő értékek egyike: "Kritikus", "Hiba", "Figyelmeztetés" és "Tájékoztató" |
| resourceGroupName |Az érintett erőforrás erőforráscsoportjának neve. |
| resourceProviderName |Az érintett erőforrás erőforrás-szolgáltatójának neve |
| resourceType | A felügyeleti esemény által érintett erőforrás típusa. |
| resourceId |Az érintett erőforrás erőforrásazonosítója. |
| operationId |Egyetlen műveletnek megfelelő események között megosztott GUID azonosító. |
| operationName |A művelet neve. |
| properties |Párkészlet `<Key, Value>` (azaz szótár), amely leírja az esemény részleteit. |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori érték: Elindítva, Folyamatban, Sikeres, Sikertelen, Aktív, Megoldva. |
| alállapot |Általában a megfelelő REST-hívás HTTP-állapotkódja, de tartalmazhat más, alállapotot leíró karakterláncokat is, például a következő általános értékeket: OK (HTTP-állapotkód: 200), Létrehozva (HTTP-állapotkód: 201), Elfogadott (HTTP-állapotkód: 202), Nincs tartalom (HTTP-állapot Kód: 204), Hibás kérelem (HTTP-állapotkód: 400), Nem található (HTTP-állapotkód: 404), Ütközés (HTTP-állapotkód: 409), Belső kiszolgálóhiba (HTTP-állapotkód: 500), Szolgáltatás nem érhető el (HTTP állapotkód: 503), átjáró időtúllépések (HTTP-állapotkód: 504). |
| eventTimestamp |Időbélyeg, amikor az eseményt az Azure szolgáltatás hozta létre az eseménynek megfelelő kérés feldolgozása. |
| beküldésIdőbélyeg |Időbélyeg, amikor az esemény elérhetővé vált a lekérdezéshez. |
| subscriptionId |Azure-előfizetés-azonosító. |

## <a name="service-health"></a>Szolgáltatás állapota
Ez a kategória az Azure-ban előforduló szolgáltatásállapot-incidensek rekordját tartalmazza. Egy példa az ebben a kategóriában látható esemény típusára: "Az SQL Azure az USA keleti részén állásidőt tapasztal." A szolgáltatás állapoteseményei öt fajtából származnak: Művelet szükséges, Segített helyreállítás, Incidens, Karbantartás, Információ vagy Biztonság, és csak akkor jelennek meg, ha rendelkezik az erőforrással az előfizetésben, amelyet az esemény érintene.

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
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
A [szolgáltatásállapot-értesítések](./../../azure-monitor/platform/service-notifications.md) ről szóló cikkben a tulajdonságok értékeiről dokumentációt talál.

## <a name="resource-health"></a>Erőforrás állapota
Ez a kategória az Azure-erőforrásokban bekövetkezett erőforrás-állapotesemények rekordját tartalmazza. Az ebben a kategóriában látható esemény típusának egyik példája a "A virtuális gép állapota nem érhető el" állapot. Az erőforrás-állapotesemények a következő négy állapot egyikét képviselhetik: Elérhető, Nem érhető el, Leromlott és Ismeretlen. Emellett az erőforrás-állapotesemények platform által kezdeményezettként vagy felhasználó által kezdeményezettként kategorizálhatók.

### <a name="sample-event"></a>Mintaesemény

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Tulajdonság leírások
| Elem neve | Leírás |
| --- | --- |
| Csatornák | Mindig "Admin, Operation" |
| correlationId | GuiD a karakterlánc formátumban. |
| leírás |A riasztási esemény statikus szöveges leírása. |
| eventDataId |A riasztási esemény egyedi azonosítója |
| category | Mindig "ResourceHealth" |
| eventTimestamp |Időbélyeg, amikor az eseményt az Azure szolgáltatás hozta létre az eseménynek megfelelő kérés feldolgozása. |
| szint |Az esemény szintje. A következő értékek egyike: "Kritikus", "Hiba", "Figyelmeztetés", "Tájékoztató" és "Részletes" |
| operationId |Egyetlen műveletnek megfelelő események között megosztott GUID azonosító. |
| operationName |A művelet neve. |
| resourceGroupName |Az erőforrást tartalmazó erőforráscsoport neve. |
| resourceProviderName |Mindig "Microsoft.Resourcehealth/healthevent/action". |
| resourceType | Az erőforrás-állapot esemény által érintett erőforrás típusa. |
| resourceId | Az érintett erőforrás erőforrásazonosítójának neve. |
| status |Az állapotesemény állapotát leíró karakterlánc. Az értékek lehetnek: Aktív, Megoldva, InProgress, Frissítve. |
| alállapot | Általában null a riasztások. |
| beküldésIdőbélyeg |Időbélyeg, amikor az esemény elérhetővé vált a lekérdezéshez. |
| subscriptionId |Azure-előfizetés-azonosító. |
| properties |Párkészlet `<Key, Value>` (azaz szótár), amely leírja az esemény részleteit.|
| properties.title | Felhasználóbarát karakterlánc, amely az erőforrás állapotát írja le. |
| properties.details | Felhasználóbarát karakterlánc, amely az esemény további részleteit írja le. |
| properties.currentHealthStatus | Az erőforrás aktuális állapota. A következő értékek egyike: "Available", "Unavailable", "Degraded" és "Unknown". |
| properties.previousHealthStatus | Az erőforrás előző állapotállapota. A következő értékek egyike: "Available", "Unavailable", "Degraded" és "Unknown". |
| properties.type | Az erőforrás-állapotesemény típusának leírása. |
| properties.cause | Az erőforrás-állapotesemény okának leírása. Vagy "UserInitiated" és "PlatformInitiated". |


## <a name="alert"></a>Riasztás
Ez a kategória tartalmazza az Azure-riasztások összes aktiválásának rekordját. Egy példa arra, hogy milyen típusú eseményt láthatebben a kategóriában: "A myVM CPU%-a az elmúlt 5 percben több mint 80 volt." Számos Azure-rendszer rendelkezik riasztási koncepcióval – meghatározhat valamilyen szabályt, és értesítést kaphat, ha a feltételek megfelelnek az adott szabálynak. Minden alkalommal, amikor egy támogatott Azure-riasztási típus "aktiválódik", vagy a feltételek teljesülnek egy értesítés létrehozásához, az aktiválási rekord is leküldi a tevékenységnapló ezen kategóriájába.

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

### <a name="property-descriptions"></a>Tulajdonság leírások
| Elem neve | Leírás |
| --- | --- |
| Hívó | Mindig Microsoft.Insights/alertRules |
| Csatornák | Mindig "Admin, Operation" |
| Azt állítja | JSON blob az egyszerű szolgáltatásnév (egyszerű szolgáltatás neve) vagy erőforrás-típusa, a riasztási motor. |
| correlationId | GuiD a karakterlánc formátumban. |
| leírás |A riasztási esemény statikus szöveges leírása. |
| eventDataId |A riasztási esemény egyedi azonosítója |
| category | Mindig "Alert" |
| szint |Az esemény szintje. A következő értékek egyike: "Kritikus", "Hiba", "Figyelmeztetés" és "Tájékoztató" |
| resourceGroupName |Az érintett erőforrás erőforráscsoportjának neve, ha metrikariasztás. Más riasztástípusok esetén ez annak az erőforráscsoportnak a neve, amely magát a riasztást tartalmazza. |
| resourceProviderName |Az érintett erőforrás erőforrás-szolgáltatójának neve, ha metrikariasztásról van szó. Más riasztástípusok esetében ez a riasztás erőforrás-szolgáltatójának neve. |
| resourceId | Az érintett erőforrás erőforrásazonosítójának neve, ha metrikariasztás. Más riasztási típusok esetében a riasztási erőforrás erőforrásazonosítója. |
| operationId |Egyetlen műveletnek megfelelő események között megosztott GUID azonosító. |
| operationName |A művelet neve. |
| properties |Párkészlet `<Key, Value>` (azaz szótár), amely leírja az esemény részleteit. |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori érték: Elindítva, Folyamatban, Sikeres, Sikertelen, Aktív, Megoldva. |
| alállapot | Általában null a riasztások. |
| eventTimestamp |Időbélyeg, amikor az eseményt az Azure szolgáltatás hozta létre az eseménynek megfelelő kérés feldolgozása. |
| beküldésIdőbélyeg |Időbélyeg, amikor az esemény elérhetővé vált a lekérdezéshez. |
| subscriptionId |Azure-előfizetés-azonosító. |

### <a name="properties-field-per-alert-type"></a>Tulajdonságok mező riasztástípusonként
A tulajdonságok mező a riasztási esemény forrásától függően különböző értékeket tartalmaz. Két gyakori riasztási esemény szolgáltató a tevékenységnapló-riasztások és a metrikariasztások.

#### <a name="properties-for-activity-log-alerts"></a>A tevékenységnapló-riasztások tulajdonságai
| Elem neve | Leírás |
| --- | --- |
| properties.subscriptionId | Az előfizetés azonosítója a tevékenységnapló-eseményből, amely a tevékenységnapló-riasztási szabály aktiválását okozta. |
| properties.eventDataId | A tevékenységnapló-riasztási szabály aktiválását okozó tevékenységnapló-riasztási szabály eseményadat-azonosítója. |
| properties.resourceGroup | A tevékenységnapló-riasztási szabály aktiválását okozó tevékenységnapló-riasztási szabály erőforráscsoportja. |
| properties.resourceId | Az erőforrás-azonosító a tevékenységnapló-eseményből, amely a tevékenységnapló riasztási szabály aktiválását okozta. |
| properties.eventTimestamp | A tevékenységnapló-esemény eseményidőbélyege, amely a tevékenységnapló-riasztási szabály aktiválását okozta. |
| properties.operationName | A tevékenységnapló-riasztási szabály aktiválását okozó tevékenységnapló-esemény műveletneve. |
| properties.status | A tevékenységnapló-riasztási szabály aktiválását okozó tevékenységnapló-esemény állapota.|

#### <a name="properties-for-metric-alerts"></a>Metrikariasztások tulajdonságai
| Elem neve | Leírás |
| --- | --- |
| Tulajdonságok. Szabályuri | A metrikariasztási szabály erőforrásazonosítója. |
| Tulajdonságok. Szabályneve | A metrikariasztási szabály neve. |
| Tulajdonságok. Szabályleírása | A metrikariasztási szabály leírása (a riasztási szabályban meghatározottak szerint). |
| Tulajdonságok. Küszöb | A metrikariasztási szabály kiértékelésében használt küszöbérték. |
| Tulajdonságok. WindowSizeInMinutes | A metrikariasztási szabály kiértékelésében használt ablakméret. |
| Tulajdonságok. Összesítés | A metrikariasztási szabályban meghatározott összesítési típus. |
| Tulajdonságok. Üzemeltető | A metrikariasztási szabály kiértékelésében használt feltételes operátor. |
| Tulajdonságok. Metrikusnév | A metrikariasztási szabály kiértékelésében használt metrika neve. |
| Tulajdonságok. MetricUnit (Metrikus egység) | A metrikariasztási szabály kiértékelésében használt metrika mértékegysége. |

## <a name="autoscale"></a>Automatikus méretezés
Ez a kategória az automatikus skálázási motor működéséhez kapcsolódó események rekordját tartalmazza az előfizetésben megadott automatikus skálázási beállítások alapján. Az ebben a kategóriában látható esemény típusára példa az "Automatikus skálázási felskálázási művelet sikertelen". Az automatikus skálázás használatával automatikusan skálázhatja vagy skálázhatja a támogatott erőforrás-típusok példányainak számát a napszak és/vagy a betöltési (metrika) adatok automatikus skálázási beállítás használatával. Ha a feltételek teljesülnek a fel- vagy leskálázáshoz, a kezdő és a sikeres vagy sikertelen események ebben a kategóriában kerülnek rögzítésre.

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

### <a name="property-descriptions"></a>Tulajdonság leírások
| Elem neve | Leírás |
| --- | --- |
| Hívó | Mindig Microsoft.Insights/autoscaleBeállítások |
| Csatornák | Mindig "Admin, Operation" |
| Azt állítja | JSON blob az egyszerű szolgáltatásnév (egyszerű szolgáltatás neve), vagy erőforrás-típus, az automatikus skálázási motor. |
| correlationId | GuiD a karakterlánc formátumban. |
| leírás |Az automatikus skálázási esemény statikus szöveges leírása. |
| eventDataId |Az automatikus skálázási esemény egyedi azonosítója |
| szint |Az esemény szintje. A következő értékek egyike: "Kritikus", "Hiba", "Figyelmeztetés" és "Tájékoztató" |
| resourceGroupName |Az automatikus skálázási beállítás erőforráscsoportjának neve. |
| resourceProviderName |Az automatikus skálázási beállítás erőforrás-szolgáltatójának neve. |
| resourceId |Az automatikus skálázási beállítás erőforrásazonosítója. |
| operationId |Egyetlen műveletnek megfelelő események között megosztott GUID azonosító. |
| operationName |A művelet neve. |
| properties |Párkészlet `<Key, Value>` (azaz szótár), amely leírja az esemény részleteit. |
| Tulajdonságok. Leírás | Részletes leírása, amit az autoscale motor csinál. |
| Tulajdonságok. Erőforrásneve | Az érintett erőforrás erőforrásazonosítója (az az erőforrás, amelyen a méretezési műveletet hajtották végre) |
| Tulajdonságok. OldInstancesCount (Régi instancescount) | Az automatikus skálázási művelet érvénybe lépése előtti példányok száma. |
| Tulajdonságok. NewInstancesCount (Új instancesCount) | Az automatikus skálázási művelet érvénybe lépése utáni példányok száma. |
| Tulajdonságok. LastScaleActionTime | Az automatikus skálázási művelet bekövetkezésének időbélyege. |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori érték: Elindítva, Folyamatban, Sikeres, Sikertelen, Aktív, Megoldva. |
| alállapot | Általában null az automatikus skálázáshoz. |
| eventTimestamp |Időbélyeg, amikor az eseményt az Azure szolgáltatás hozta létre az eseménynek megfelelő kérés feldolgozása. |
| beküldésIdőbélyeg |Időbélyeg, amikor az esemény elérhetővé vált a lekérdezéshez. |
| subscriptionId |Azure-előfizetés-azonosító. |

## <a name="security"></a>Biztonság
Ez a kategória tartalmazza az Azure Security Center által létrehozott riasztások rekord. Az ebben a kategóriában látható esemény típusára példa a "Gyanús kettős kiterjesztésű fájl végrehajtása".

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

### <a name="property-descriptions"></a>Tulajdonság leírások
| Elem neve | Leírás |
| --- | --- |
| Csatornák | Mindig "Művelet" |
| correlationId | GuiD a karakterlánc formátumban. |
| leírás |A biztonsági esemény statikus szöveges leírása. |
| eventDataId |A biztonsági esemény egyedi azonosítója |
| eventName |A biztonsági esemény rövid neve. |
| category | Mindig "Biztonság" |
| ID (Azonosító) |A biztonsági esemény egyedi erőforrás-azonosítója |
| szint |Az esemény szintje. A következő értékek egyike: "Kritikus", "Hiba", "Figyelmeztetés", vagy "Tájékoztató" |
| resourceGroupName |Az erőforrás erőforráscsoportjának neve. |
| resourceProviderName |Az Azure Security Center erőforrás-szolgáltatójának neve. Mindig "Microsoft.Security". |
| resourceType |A biztonsági eseményt létrehozó erőforrás típusa, például "Microsoft.Security/locations/alerts" |
| resourceId |A biztonsági riasztás erőforrásazonosítója. |
| operationId |Egyetlen műveletnek megfelelő események között megosztott GUID azonosító. |
| operationName |A művelet neve. |
| properties |Párkészlet `<Key, Value>` (azaz szótár), amely leírja az esemény részleteit. Ezek a tulajdonságok a biztonsági riasztás típusától függően változnak. [Ezen a lapon](../../security-center/security-center-alerts-overview.md) a Security Centerből érkező riasztások típusainak leírását találja. |
| Tulajdonságok. Súlyossága |A súlyossági szint. A lehetséges értékek a "Magas", "Közepes" vagy "Alacsony". |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori érték: Elindítva, Folyamatban, Sikeres, Sikertelen, Aktív, Megoldva. |
| alállapot | Biztonsági események esetén általában null. |
| eventTimestamp |Időbélyeg, amikor az eseményt az Azure szolgáltatás hozta létre az eseménynek megfelelő kérés feldolgozása. |
| beküldésIdőbélyeg |Időbélyeg, amikor az esemény elérhetővé vált a lekérdezéshez. |
| subscriptionId |Azure-előfizetés-azonosító. |

## <a name="recommendation"></a>Ajánlás
Ez a kategória a szolgáltatásokhoz létrehozott új javaslatok rekordját tartalmazza. Egy javaslat egy példa lenne a "Rendelkezésre állási csoportok használata a jobb hibatűrés." Négy féle ajánlási események, amelyek létrehozhatók: magas rendelkezésre állás, teljesítmény, biztonság és költségoptimalizálás. 

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
### <a name="property-descriptions"></a>Tulajdonság leírások
| Elem neve | Leírás |
| --- | --- |
| Csatornák | Mindig "Művelet" |
| correlationId | GuiD a karakterlánc formátumban. |
| leírás |Az ajánlási esemény statikus szöveges leírása |
| eventDataId | A javaslati esemény egyedi azonosítója |
| category | Mindig "Ajánlás" |
| ID (Azonosító) |A javaslati esemény egyedi erőforrás-azonosítója |
| szint |Az esemény szintje. A következő értékek egyike: "Kritikus", "Hiba", "Figyelmeztetés", vagy "Tájékoztató" |
| operationName |A művelet neve.  Mindig "Microsoft.Advisor/generateRecommendations/action"|
| resourceGroupName |Az erőforrás erőforráscsoportjának neve. |
| resourceProviderName |A javaslat hatálya alatt álló erőforrás erőforrás-szolgáltatójának neve, például "MICROSOFT.COMPUTE" |
| resourceType |A javaslat hatálya alatt álló erőforrás erőforrástípusának neve, például "MICROSOFT.COMPUTE/virtualmachines" |
| resourceId |Annak az erőforrásnak az erőforrásazonosítója, amelyre a javaslat vonatkozik |
| status | Mindig "Aktív" |
| beküldésIdőbélyeg |Időbélyeg, amikor az esemény elérhetővé vált a lekérdezéshez. |
| subscriptionId |Azure-előfizetés-azonosító. |
| properties |`<Key, Value>` Párkészlet (azaz szótár), amely leírja az ajánlás részleteit.|
| properties.recommendationSchemaVersion| A tevékenységnapló bejegyzésben közzétett javaslati tulajdonságok sémaverziója |
| properties.recommendationKategória | Az ajánlás kategóriája. A lehetséges értékek a következők: "Magas rendelkezésre állás", "Teljesítmény", "Biztonság" és "Költség" |
| properties.recommendationImpact| Az ajánlás hatása. A lehetséges értékek a következők: "Magas", "Közepes", "Alacsony" |
| properties.recommendationRisk| Az ajánlás kockázata. A lehetséges értékek a következők: "Hiba", "Figyelmeztetés", "Nincs" |

## <a name="policy"></a>Szabályzat

Ez a kategória az [Azure Policy](../../governance/policy/overview.md)által végrehajtott összes hatásművelet rekordjait tartalmazza. Az ebben a kategóriában megjelenő eseménytípusok közé tartozik _például a Naplózás_ és _a Megtagadás_. A szabályzat által végrehajtott minden művelet egy erőforrás-műveletként van modellezve.

### <a name="sample-policy-event"></a>Mintaházirend-esemény

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Házirend-eseménytulajdonság-leírások

| Elem neve | Leírás |
| --- | --- |
| engedélyezés | Az esemény RBAC-tulajdonságainak tömbje. Új erőforrások esetén ez a művelet és a kérelem hatóköre, amely kiváltotta a kiértékelést. A meglévő erőforrások esetében a művelet a "Microsoft.Resources/checkPolicyCompliance/read". |
| Hívó | Új erőforrások esetén a központi telepítést kezdeményező identitás. A meglévő erőforrások esetében a Microsoft Azure Policy Insights RP GUID azonosítója. |
| Csatornák | A házirend-események csak az "Operation" csatornát használják. |
| Azt állítja | Az Active Directory által a felhasználó vagy alkalmazás által a művelet Resource Managerben történő végrehajtásához használt JWT-jogkivonat. |
| correlationId | Általában a GUID a karakterlánc formátumban. A korrelációsazonosítóval megegyező események ugyanahhoz az überművelethez tartoznak. |
| leírás | Ez a mező üres a Házirend-események esetén. |
| eventDataId | Egy esemény egyedi azonosítója |
| eventName | "BeginRequest" vagy "EndRequest" vagy "EndRequest". A "BeginRequest" a késleltetett auditIfNotExists és deployIfNotExists kiértékelésekhez használatos, és amikor egy deployIfNotExists hatás elindít egy sablon központi telepítését. Minden más művelet visszaadja az "EndRequest" műveletet. |
| category | A tevékenységnapló-eseményt a "Szabályzat" -hoz tartozóként deklarálja. |
| eventTimestamp | Időbélyeg, amikor az eseményt az Azure szolgáltatás hozta létre az eseménynek megfelelő kérés feldolgozása. |
| ID (Azonosító) | Az adott erőforrás eseményének egyedi azonosítója |
| szint | Az esemény szintje. A naplózás a "Figyelmeztetés" szót, a Megtagadás pedig a "Hiba" szót használja. Egy auditIfNotExists vagy deployIfNotExists hiba a súlyosságtól függően "Figyelmeztetés" vagy "Hiba" értéket generálhat. Minden más házirend-esemény az "Információs" szót használja. |
| operationId | Egyetlen műveletnek megfelelő események között megosztott GUID azonosító. |
| operationName | A művelet neve, és közvetlenül korrelál a házirend-effektushoz. |
| resourceGroupName | A kiértékelt erőforrás erőforráscsoportjának neve. |
| resourceProviderName | A kiértékelt erőforrás erőforrás-szolgáltatójának neve. |
| resourceType | Az új erőforrások esetében ez a kiértékelendő típus. Meglévő erőforrások esetén a "Microsoft.Resources/checkPolicyCompliance" értéket adja vissza. |
| resourceId | A kiértékelt erőforrás erőforrásazonosítója. |
| status | A házirend-értékelési eredmény állapotát leíró karakterlánc. A legtöbb házirend-kiértékelés "Sikeres" értéket ad vissza, de a Deny hatás a "Sikertelen" értéket adja vissza. Az auditIfNotExists vagy deployIfNotExists hibák szintén "Sikertelen" adják vissza. |
| alállapot | A mező üres a Házirend-eseményeknél. |
| beküldésIdőbélyeg | Időbélyeg, amikor az esemény elérhetővé vált a lekérdezéshez. |
| subscriptionId | Azure-előfizetés-azonosító. |
| properties.isComplianceCheck | "Hamis" értéket ad vissza, ha egy új erőforrás telepítve van, vagy egy meglévő erőforrás Erőforrás-kezelő tulajdonságai frissülnek. Minden más [kiértékelési eseményindító "True"](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) értéket eredményez. |
| properties.resourceLocation | A kiértékelt erőforrás Azure-régiója. |
| properties.ancestors | A közvetlen szülőtől a legtávolabbi nagyszülőig rendelt szülőkezelési csoportok vesszővel tagolt listája. |
| properties.házirendek | A házirend-definíció,-hozzárendelés, -hatás és -paraméterek részleteit tartalmazza, amelyek nek ez a házirend-kiértékelés eredménye. |
| relatedEvents (kapcsolódó események) | Ez a mező üres a Házirend-események esetén. |


## <a name="schema-from-storage-account-and-event-hubs"></a>Séma a tárfiókból és az eseményközpontokból
Az Azure-tevékenységnapló tárfiókba vagy eseményközpontba való streamelésekor az adatok az [erőforrásnapló-sémát](diagnostic-logs-schema.md)követik. Az alábbi táblázat a fenti séma és az erőforrásnaplók sémájához való hozzárendelést tartalmazza.

> [!IMPORTANT]
> 2018. november 1-jén jsonsorokra változott a tárfiókba írt tevékenységnapló-adatok formátuma. [A formátumváltozás előkészítése az Azure Monitor egyik fiókba archivált erőforrásnaplókról](diagnostic-logs-append-blobs.md) a formátumváltozás részleteiért olvassa el.


| Erőforrásnaplók sématulajdonsága | Tevékenységnapló REST API-sématulajdonsága | Megjegyzések |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | a subscriptionId, resourceType, resourceGroupName mind a resourceId azonosítóból származik. |
| operationName | operationName.érték |  |
| category | A művelet egy része | A művelettípus kitörése - "Write"/"Delete"/"Action" |
| resultType | status.érték | |
| resultSignature | alstatus.érték | |
| resultDescription | leírás |  |
| durationMs | N/A | Mindig 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | jogcímek és engedélyezési tulajdonságok |  |
| Szint | Szint |  |
| location | N/A | Az esemény feldolgozásának helye. *Ez nem az erőforrás helye, hanem az esemény feldolgozási helye. Ez a tulajdonság egy későbbi frissítésben törlődik.* |
| Tulajdonságok | properties.eventProperties |  |
| properties.eventKategória | category | Ha a properties.eventCategory nincs jelen, a kategória "Adminisztratív" |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |

Az alábbiakban egy példa látható egy, a sémát használó eseményre..

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
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
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```





## <a name="next-steps"></a>További lépések
* [További információ a tevékenységnaplóról](platform-logs-overview.md)
* [Diagnosztikai beállítás létrehozása a Tevékenységnapló log analytics-munkaterületre, Azure-tárhelyre vagy eseményközpontokba való elküldéséhez](diagnostic-settings.md)

