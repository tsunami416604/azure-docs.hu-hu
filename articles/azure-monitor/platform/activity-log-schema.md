---
title: Azure Activity log esemény sémája
description: Az Azure-tevékenység naplójának egyes kategóriáira vonatkozó esemény sémáját ismerteti.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 1/16/2019
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: d196cf4024513d891182f3b916bd8412a2f81d14
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74305487"
---
# <a name="azure-activity-log-event-schema"></a>Azure Activity log esemény sémája
Az **Azure-tevékenység naplója** olyan napló, amely betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekre. Ez a cikk az egyes adatkategóriákon keresztül mutatja be az esemény sémáját. Az Adatséma különbözik attól függően, hogy a portálon, a PowerShellben, a CLI-ben vagy közvetlenül a REST APIon keresztül olvassa-e be az adatátvitelt, [és hogyan lehet a tárolóba vagy Event Hubs naplózni](activity-log-export.md). Az alábbi példák a portálon, a PowerShellen, a CLI-n és a REST APIon elérhetővé tett sémát mutatják be. Ezeknek a tulajdonságoknak az [Azure diagnosztikai naplók sémához](diagnostic-logs-schema.md) való leképezése a cikk végén található.

## <a name="administrative"></a>Adminisztratív
Ez a kategória a Resource Manageren keresztül végrehajtott összes létrehozási, frissítési, törlési és műveleti művelet rekordját tartalmazza. Az ebben a kategóriában látható események típusai közé tartozik például a "virtuális gép létrehozása" és a "hálózati biztonsági csoport törlése" művelet, amelyet egy felhasználó vagy alkalmazás a Resource Manager használatával végzett minden művelet egy adott erőforrástípus működésének megfelelően modellez. Ha a művelet típusa írás, törlés vagy művelet, akkor a művelet kezdési és sikerességi rekordjait is rögzíti a rendszer a felügyeleti kategóriában. A felügyeleti kategória az előfizetés szerepköralapú hozzáférés-vezérlésének változásait is tartalmazza.

### <a name="sample-event"></a>Minta esemény
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

### <a name="property-descriptions"></a>Tulajdonságok leírása
| Elem neve | Leírás |
| --- | --- |
| authorization |Az esemény RBAC-tulajdonságainak blobja. Általában a "művelet", a "szerepkör" és a "hatókör" tulajdonságokat tartalmazza. |
| hívó |Annak a felhasználónak az e-mail-címe, aki a művelet, UPN-jogcím vagy SPN jogcím alapján végrehajtotta a rendelkezésre állást. |
| csatornák |A következő értékek egyike: "admin", "művelet" |
| claims |Az Active Directory által használt JWT-jogkivonat hitelesíti a felhasználót vagy alkalmazást a művelet végrehajtásához a Resource Managerben. |
| correlationId |Általában egy GUID formátumú karakterlánc. A correlationId osztozó események ugyanahhoz az Über-művelethez tartoznak. |
| leírás |Egy esemény statikus szöveges leírása. |
| eventDataId |Egy esemény egyedi azonosítója. |
| eventName | A felügyeleti esemény rövid neve. |
| category | Mindig "adminisztráció" |
| httpRequest |A HTTP-kérést leíró blob. Általában a "ügyfélkérelem", a "clientIpAddress" és a "metódus" (HTTP-metódus) szerepel. Például: PUT). |
| szint |Az esemény szintje. A következő értékek egyike: "kritikus", "hiba", "figyelmeztetés" és "tájékoztató" |
| resourceGroupName |Az érintett erőforráshoz tartozó erőforráscsoport neve. |
| resourceProviderName |Az érintett erőforráshoz tartozó erőforrás-szolgáltató neve |
| resourceType | A felügyeleti esemény által érintett erőforrás típusa. |
| resourceId |Az érintett erőforrás erőforrás-azonosítója. |
| operationId |Az egyetlen műveletnek megfelelő események között megosztva lévő GUID. |
| operationName |A művelet neve. |
| properties |Az esemény részleteit leíró `<Key, Value>` pár (azaz egy szótár). |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori érték: elindítva, folyamatban, sikeres, sikertelen, aktív, megoldva. |
| Részállapot |Általában a megfelelő REST-hívás HTTP-állapotkód, de tartalmazhat más, alállapotot leíró karakterláncokat is, például az alábbi általános értékeket: OK (HTTP-állapotkód: 200), létrehozva (HTTP-állapotkód: 201), elfogadva (HTTP-állapotkód: 202), nincs tartalom (HTTP-állapot Kód: 204), hibás kérés (HTTP-állapotkód: 400), nem található (HTTP-állapotkód: 404), ütközés (HTTP-állapotkód: 409), belső kiszolgálóhiba (HTTP-állapotkód: 500), a szolgáltatás nem érhető el (HTTP-állapotkód: 503), átjáró időkorlátja (HTTP-állapotkód: 504). |
| EventTimestamp |Időbélyeg, ha az eseményt az Azure-szolgáltatás hozta létre, és az eseményt az eseménynek megfelelő kérelem dolgozza fel. |
| submissionTimestamp |Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez. |
| subscriptionId |Azure-előfizetés azonosítója. |

## <a name="service-health"></a>Szolgáltatás állapota
Ez a kategória tartalmazza az Azure-ban történt összes szolgáltatás-egészségügyi incidens rekordját. Ebben a kategóriában a "SQL Azure az USA keleti régiójában az állásidőt tapasztalja." A szolgáltatás állapotával kapcsolatos események öt különböző változatban állnak rendelkezésre: a szükséges beavatkozás, a helyreállítás, az incidens, a karbantartás, az információ vagy a biztonság, és csak akkor jelenik meg, ha az előfizetésben az esemény által érintett erőforrás van.

### <a name="sample-event"></a>Minta esemény
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
A tulajdonságok között található értékekkel kapcsolatos dokumentációért tekintse meg a [Service Health Notifications](./../../azure-monitor/platform/service-notifications.md) című cikket.

## <a name="resource-health"></a>Erőforrás állapota
Ez a kategória az Azure-erőforrásokra vonatkozó összes erőforrás-állapottal kapcsolatos esemény rekordját tartalmazza. Ebben a kategóriában a "virtuális gép állapota nem érhető el értékre módosult" típusú eseményre mutat példát. Az erőforrás-állapot eseményei a négy állapot egyikét jelezhetik: elérhető, nem elérhető, csökkentett teljesítményű és ismeretlen. Emellett az erőforrás-állapot eseményei a platform által kezdeményezett vagy a felhasználó által kezdeményezett módon kategorizálva is lehetnek.

### <a name="sample-event"></a>Minta esemény

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

### <a name="property-descriptions"></a>Tulajdonságok leírása
| Elem neve | Leírás |
| --- | --- |
| csatornák | Mindig a "rendszergazda, művelet" |
| correlationId | A karakterlánc formátumú GUID. |
| leírás |A riasztási esemény statikus szöveges leírása. |
| eventDataId |A riasztási esemény egyedi azonosítója. |
| category | Mindig "ResourceHealth" |
| EventTimestamp |Időbélyeg, ha az eseményt az Azure-szolgáltatás hozta létre, és az eseményt az eseménynek megfelelő kérelem dolgozza fel. |
| szint |Az esemény szintje. A következő értékek egyike: "kritikus", "hiba", "figyelmeztetés", "tájékoztató" és "részletes" |
| operationId |Az egyetlen műveletnek megfelelő események között megosztva lévő GUID. |
| operationName |A művelet neve. |
| resourceGroupName |Az erőforrást tartalmazó erőforráscsoport neve. |
| resourceProviderName |Mindig "Microsoft. Resourcehealth/healthevent/Action". |
| resourceType | Resource Health esemény által érintett erőforrás típusa. |
| resourceId | Az érintett erőforráshoz tartozó erőforrás-azonosító neve. |
| status |Az állapotadatok állapotát leíró karakterlánc. Az értékek a következőket okozhatják: aktív, megoldott, Inprogress, frissítve. |
| Részállapot | Általában null a riasztásokhoz. |
| submissionTimestamp |Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez. |
| subscriptionId |Azure-előfizetés azonosítója. |
| properties |Az esemény részleteit leíró `<Key, Value>` pár (azaz egy szótár).|
| tulajdonságok. cím | Egy felhasználóbarát karakterlánc, amely leírja az erőforrás állapotát. |
| Properties. details | Egy felhasználóbarát karakterlánc, amely az esemény további részleteit ismerteti. |
| properties.currentHealthStatus | Az erőforrás jelenlegi állapotának állapota. A következő értékek egyike: "elérhető", "nem elérhető", "csökkentett teljesítményű" és "ismeretlen". |
| properties.previousHealthStatus | Az erőforrás előző állapotának állapota. A következő értékek egyike: "elérhető", "nem elérhető", "csökkentett teljesítményű" és "ismeretlen". |
| properties.type | A Resource Health esemény típusának leírása. |
| tulajdonságok. ok | A Resource Health esemény okának leírása. Vagy "UserInitiated" és "PlatformInitiated". |


## <a name="alert"></a>Riasztás
Ez a kategória az Azure-riasztások összes aktiválásának rekordját tartalmazza. Ebben a kategóriában a "CPU% on myVM az elmúlt 5 percben a 80-as számú esemény látható." Számos Azure-rendszer rendelkezik riasztási koncepcióval – meghatározhat valamilyen rendezési szabályt, és értesítést kaphat, ha a feltételek megfelelnek a szabálynak. Minden alkalommal, amikor egy támogatott Azure-riasztási típus aktiválódik, vagy ha teljesülnek a feltételek, a rendszer az aktiválási rekordot is leküldi a tevékenységi napló ezen kategóriájára.

### <a name="sample-event"></a>Minta esemény

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

### <a name="property-descriptions"></a>Tulajdonságok leírása
| Elem neve | Leírás |
| --- | --- |
| hívó | Always Microsoft.Insights/alertRules |
| csatornák | Mindig a "rendszergazda, művelet" |
| claims | JSON-blob a riasztási motor SPN-vel (egyszerű szolgáltatásnév) vagy erőforrás-típussal. |
| correlationId | A karakterlánc formátumú GUID. |
| leírás |A riasztási esemény statikus szöveges leírása. |
| eventDataId |A riasztási esemény egyedi azonosítója. |
| category | Mindig "riasztás" |
| szint |Az esemény szintje. A következő értékek egyike: "kritikus", "hiba", "figyelmeztetés" és "tájékoztató" |
| resourceGroupName |Az érintett erőforráshoz tartozó erőforráscsoport neve, ha metrikai riasztás. Más típusú riasztások esetén ez a riasztást tartalmazó erőforráscsoport neve. |
| resourceProviderName |Az érintett erőforráshoz tartozó erőforrás-szolgáltató neve, ha metrikai riasztás. Más típusú riasztások esetén a riasztáshoz tartozó erőforrás-szolgáltató neve. |
| resourceId | Az érintett erőforráshoz tartozó erőforrás-azonosító neve, ha metrikai riasztás. Más típusú riasztások esetén maga a riasztási erőforrás erőforrás-azonosítója. |
| operationId |Az egyetlen műveletnek megfelelő események között megosztva lévő GUID. |
| operationName |A művelet neve. |
| properties |Az esemény részleteit leíró `<Key, Value>` pár (azaz egy szótár). |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori érték: elindítva, folyamatban, sikeres, sikertelen, aktív, megoldva. |
| Részállapot | Általában null a riasztásokhoz. |
| EventTimestamp |Időbélyeg, ha az eseményt az Azure-szolgáltatás hozta létre, és az eseményt az eseménynek megfelelő kérelem dolgozza fel. |
| submissionTimestamp |Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez. |
| subscriptionId |Azure-előfizetés azonosítója. |

### <a name="properties-field-per-alert-type"></a>Tulajdonságok mező/riasztás típusa
A tulajdonságok mező a riasztási esemény forrásától függően eltérő értékeket fog tartalmazni. Két gyakori riasztási esemény szolgáltatója a tevékenységek naplójának riasztásai és a metrikák riasztásai.

#### <a name="properties-for-activity-log-alerts"></a>A műveletnapló-riasztások tulajdonságai
| Elem neve | Leírás |
| --- | --- |
| properties.subscriptionId | A tevékenység naplójának eseményéhez tartozó előfizetés-azonosító, amely miatt a tevékenység naplójának riasztási szabálya aktiválva lett. |
| properties.eventDataId | A tevékenység naplójának eseményéhez tartozó esemény-azonosító, amely miatt a tevékenység naplójának riasztási szabálya aktiválva lett. |
| properties.resourceGroup | Az a műveletnapló eseményből származó erőforráscsoport, amely miatt a tevékenység naplójának riasztási szabálya aktiválva lett. |
| properties.resourceId | A tevékenység naplójának eseményéhez tartozó erőforrás-azonosító, amely miatt a tevékenység naplójának riasztási szabálya aktiválva lett. |
| properties.eventTimestamp | A tevékenység naplózási eseményének az esemény időbélyegzője, amely miatt a tevékenység naplójának riasztási szabálya aktiválva lett. |
| properties.operationName | A művelet neve a tevékenység naplójának eseményében, amely miatt a tevékenység naplójának riasztási szabálya aktiválva lett. |
| properties.status | A tevékenység naplózási eseményének állapota, amely miatt a rendszer aktiválja a tevékenység naplójának riasztási szabályát.|

#### <a name="properties-for-metric-alerts"></a>Metrikus riasztások tulajdonságai
| Elem neve | Leírás |
| --- | --- |
| properties.RuleUri | A metrika riasztási szabályának erőforrás-azonosítója. |
| properties.RuleName | A metrika riasztási szabályának neve. |
| properties.RuleDescription | A metrika riasztási szabályának leírása (a riasztási szabályban meghatározottak szerint). |
| properties.Threshold | A metrika riasztási szabályának kiértékeléséhez használt küszöbérték. |
| properties.WindowSizeInMinutes | A metrika riasztási szabályának kiértékeléséhez használt ablakméret |
| properties.Aggregation | A metrika riasztási szabályában definiált összesítési típus. |
| Tulajdonságok. Üzemeltető | A metrika riasztási szabályának kiértékelésében használt feltételes operátor. |
| properties.MetricName | A metrika riasztási szabályának kiértékeléséhez használt metrika metrikai neve. |
| properties.MetricUnit | A metrika riasztási szabályának kiértékeléséhez használt metrika mérőszáma. |

## <a name="autoscale"></a>Automatikus méretezés
Ez a kategória tartalmazza az adott előfizetésben definiált, az előfizetéshez megadott összes autoskálázási beállítás alapján az autoskálázási motor működésével kapcsolatos események rekordját. Egy példa arra, hogy milyen típusú eseményre lenne szüksége ebben a kategóriában: "a vertikális Felskálázási művelet nem sikerült." Az automatikus méretezés használatával automatikusan kibővítheti vagy méretezheti a támogatott erőforrástípus példányainak számát a nap-és/vagy betöltési (metrikus) adatokon alapuló automatikus méretezési beállítással. Ha a feltételek teljesülnek a vertikális fel-vagy leskálázáshoz, a rendszer a kezdő és a sikeres vagy sikertelen eseményeket rögzíti ebben a kategóriában.

### <a name="sample-event"></a>Minta esemény
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

### <a name="property-descriptions"></a>Tulajdonságok leírása
| Elem neve | Leírás |
| --- | --- |
| hívó | Always Microsoft.Insights/autoscaleSettings |
| csatornák | Mindig a "rendszergazda, művelet" |
| claims | JSON-blob az autoskálázási motor SPN-vel (egyszerű szolgáltatásnév) vagy erőforrás-típussal. |
| correlationId | A karakterlánc formátumú GUID. |
| leírás |Az autoskálázási esemény statikus szöveges leírása. |
| eventDataId |Az autoskálázási esemény egyedi azonosítója. |
| szint |Az esemény szintje. A következő értékek egyike: "kritikus", "hiba", "figyelmeztetés" és "tájékoztató" |
| resourceGroupName |Az autoskálázási beállításhoz tartozó erőforráscsoport neve. |
| resourceProviderName |Az erőforrás-szolgáltató neve az autoskálázási beállításhoz. |
| resourceId |Az autoskálázási beállítás erőforrás-azonosítója. |
| operationId |Az egyetlen műveletnek megfelelő események között megosztva lévő GUID. |
| operationName |A művelet neve. |
| properties |Az esemény részleteit leíró `<Key, Value>` pár (azaz egy szótár). |
| properties.Description | Annak részletes leírása, hogy mit csinált az autoskálázási motor. |
| properties.ResourceName | Az érintett erőforrás erőforrás-azonosítója (az erőforrás, amelyen a skálázási műveletet végrehajtották) |
| properties.OldInstancesCount | A példányok száma, mielőtt az autoskálázási művelet érvénybe lépett. |
| properties.NewInstancesCount | A példányok száma, miután az autoskálázás művelet érvénybe lépett. |
| properties.LastScaleActionTime | Az az időbélyeg, amikor az autoskálázás művelet bekövetkezett. |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori érték: elindítva, folyamatban, sikeres, sikertelen, aktív, megoldva. |
| Részállapot | Általában null az autoskálázáshoz. |
| EventTimestamp |Időbélyeg, ha az eseményt az Azure-szolgáltatás hozta létre, és az eseményt az eseménynek megfelelő kérelem dolgozza fel. |
| submissionTimestamp |Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez. |
| subscriptionId |Azure-előfizetés azonosítója. |

## <a name="security"></a>Biztonság
Ez a kategória tartalmazza a Azure Security Center által generált riasztások rekordját. Ebben a kategóriában egy példa arra, hogy milyen típusú eseményt láthat a "gyanús dupla kiterjesztésű fájl végrehajtása".

### <a name="sample-event"></a>Minta esemény
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

### <a name="property-descriptions"></a>Tulajdonságok leírása
| Elem neve | Leírás |
| --- | --- |
| csatornák | Mindig "művelet" |
| correlationId | A karakterlánc formátumú GUID. |
| leírás |A biztonsági esemény statikus szöveges leírása. |
| eventDataId |A biztonsági esemény egyedi azonosítója. |
| eventName |A biztonsági esemény rövid neve. |
| category | Mindig "biztonság" |
| id |A biztonsági esemény egyedi erőforrás-azonosítója. |
| szint |Az esemény szintje. A következő értékek egyike: "kritikus", "hiba", "figyelmeztetés" vagy "tájékoztató" |
| resourceGroupName |Az erőforrás erőforráscsoport neve. |
| resourceProviderName |A Azure Security Center erőforrás-szolgáltatójának neve. Always "Microsoft.Security". |
| resourceType |A biztonsági eseményt létrehozó erőforrás típusa, például "Microsoft. Security/Locations/riasztások" |
| resourceId |A biztonsági riasztás erőforrás-azonosítója. |
| operationId |Az egyetlen műveletnek megfelelő események között megosztva lévő GUID. |
| operationName |A művelet neve. |
| properties |Az esemény részleteit leíró `<Key, Value>` pár (azaz egy szótár). Ezek a tulajdonságok a biztonsági riasztás típusától függően változnak. [Ezen az oldalon](../../security-center/security-center-alerts-overview.md) megtekintheti az Security Centerból származó riasztási típusok leírását. |
| properties.Severity |A súlyossági szint. A lehetséges értékek: "magas", "közepes" vagy "alacsony". |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori érték: elindítva, folyamatban, sikeres, sikertelen, aktív, megoldva. |
| Részállapot | Általában null a biztonsági eseményekhez. |
| EventTimestamp |Időbélyeg, ha az eseményt az Azure-szolgáltatás hozta létre, és az eseményt az eseménynek megfelelő kérelem dolgozza fel. |
| submissionTimestamp |Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez. |
| subscriptionId |Azure-előfizetés azonosítója. |

## <a name="recommendation"></a>Ajánlás
Ez a kategória a szolgáltatásokhoz létrehozott új javaslatok rekordját tartalmazza. Egy javaslat példaként a "rendelkezésre állási csoportok használata a jobb hibatűrés érdekében" értékre mutat. A rendszer négyféle ajánlási eseményt hozhat létre: magas rendelkezésre állást, teljesítményt, biztonságot és költségmegtakarítást. 

### <a name="sample-event"></a>Minta esemény
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
### <a name="property-descriptions"></a>Tulajdonságok leírása
| Elem neve | Leírás |
| --- | --- |
| csatornák | Mindig "művelet" |
| correlationId | A karakterlánc formátumú GUID. |
| leírás |A javaslati esemény statikus szöveges leírása |
| eventDataId | A javaslati esemény egyedi azonosítója. |
| category | Mindig "javaslat" |
| id |A javaslati esemény egyedi erőforrás-azonosítója. |
| szint |Az esemény szintje. A következő értékek egyike: "kritikus", "hiba", "figyelmeztetés" vagy "tájékoztató" |
| operationName |A művelet neve.  Mindig "Microsoft. Advisor/generateRecommendations/Action"|
| resourceGroupName |Az erőforrás erőforráscsoport neve. |
| resourceProviderName |Azon erőforrás-szolgáltató neve, amelyre ez a javaslat vonatkozik, például "MICROSOFT. számítás" |
| resourceType |Annak az erőforrásnak a neve, amelyre ez az ajánlás vonatkozik, például "MICROSOFT. számítás/virtualmachines" |
| resourceId |Azon erőforrás erőforrás-azonosítója, amelyre a javaslat vonatkozik |
| status | Mindig "aktív" |
| submissionTimestamp |Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez. |
| subscriptionId |Azure-előfizetés azonosítója. |
| properties |`<Key, Value>` pár (azaz egy szótár) készlete, amely leírja a javaslat részleteit.|
| Properties. recommendationSchemaVersion| A tevékenység naplójának bejegyzésében közzétett ajánlási tulajdonságok sémájának verziója |
| Properties. recommendationCategory | A javaslat kategóriája. A lehetséges értékek a "magas rendelkezésre állás", a "teljesítmény", a "biztonság" és a "Cost" |
| Properties. recommendationImpact| A javaslat hatása. A lehetséges értékek a következők: "magas", "közepes", "alacsony" |
| Properties. recommendationRisk| A javaslat kockázata. A lehetséges értékek a következők: "Error", "Warning", "None" |

## <a name="policy"></a>Szabályzat

Ez a kategória a [Azure Policy](../../governance/policy/overview.md)által végrehajtott összes hatás művelet műveleteit tartalmazza. Az ebben a kategóriában látható események típusai közé tartoznak például a _naplózás_ és a _Megtagadás_. A házirend által végrehajtott összes művelet az erőforráson végzett műveletként van modellezve.

### <a name="sample-policy-event"></a>Példa házirend-eseményre

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

### <a name="policy-event-property-descriptions"></a>Házirend-esemény tulajdonságainak leírása

| Elem neve | Leírás |
| --- | --- |
| authorization | Az esemény RBAC-tulajdonságainak tömbje. Az új erőforrások esetében ez a művelet és a kiértékelést kiváltó kérelem hatóköre. A meglévő erőforrások esetében a művelet a következő: "Microsoft. Resources/checkPolicyCompliance/Read". |
| hívó | Új erőforrások esetén a központi telepítést kezdeményező identitás. Meglévő erőforrások esetében a Microsoft Azure Policy bepillantást az RP GUID azonosító. |
| csatornák | A házirend-események csak a "művelet" csatornát használják. |
| claims | Az Active Directory által használt JWT-jogkivonat hitelesíti a felhasználót vagy alkalmazást a művelet végrehajtásához a Resource Managerben. |
| correlationId | Általában egy GUID formátumú karakterlánc. A correlationId osztozó események ugyanahhoz az Über-művelethez tartoznak. |
| leírás | Ez a mező a házirend eseményeinél üres. |
| eventDataId | Egy esemény egyedi azonosítója. |
| eventName | Vagy "BeginRequest" vagy "EndRequest". A "BeginRequest" a késleltetett auditIfNotExists és deployIfNotExists-értékelések, valamint a deployIfNotExists-effektusok elindítására szolgál. Minden más művelet "EndRequest" értéket ad vissza. |
| category | Deklarálja a tevékenység naplójának eseményét a "szabályzat" kifejezésnek megfelelően. |
| EventTimestamp | Időbélyeg, ha az eseményt az Azure-szolgáltatás hozta létre, és az eseményt az eseménynek megfelelő kérelem dolgozza fel. |
| id | Az esemény egyedi azonosítója az adott erőforráson. |
| szint | Az esemény szintje. A naplózás a "figyelmeztetés" kifejezést használja, és a megtagadás a "hiba" kifejezést használja. Egy auditIfNotExists vagy deployIfNotExists hiba a súlyosságtól függően "figyelmeztetés" vagy "hiba" hozható elő. Minden más házirend-esemény a "tájékoztató" kifejezést használja. |
| operationId | Az egyetlen műveletnek megfelelő események között megosztva lévő GUID. |
| operationName | A művelet neve, és közvetlenül összefügg a házirend hatásával. |
| resourceGroupName | A kiértékelt erőforráshoz tartozó erőforráscsoport neve. |
| resourceProviderName | A kiértékelt erőforráshoz tartozó erőforrás-szolgáltató neve. |
| resourceType | Az új erőforrások esetében ez a típus kiértékelése. A meglévő erőforrások esetében a "Microsoft. Resources/checkPolicyCompliance" értéket adja vissza. |
| resourceId | A kiértékelt erőforrás erőforrás-azonosítója. |
| status | A szabályzat kiértékelési eredményének állapotát leíró karakterlánc. A legtöbb házirend-Értékelés "sikeres" értéket ad vissza, de a Megtagadás effektus a "sikertelen" értéket adja vissza. A auditIfNotExists vagy a deployIfNotExists hibája szintén "sikertelen" értéket ad vissza. |
| Részállapot | A mező üres a házirend eseményeinél. |
| submissionTimestamp | Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez. |
| subscriptionId | Azure-előfizetés azonosítója. |
| properties.isComplianceCheck | A "false" értéket adja vissza új erőforrás telepítésekor vagy egy meglévő erőforrás Resource Manager-tulajdonságainak frissítésekor. Minden más [kiértékelési eseményindító](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) "igaz" értéket eredményez. |
| properties.resourceLocation | A kiértékelt erőforrás Azure-régiója. |
| tulajdonságok. ősök | A fölérendelt felügyeleti csoportok vesszővel elválasztott listája, amely a közvetlen szülőről a legtávolabbi nagyszülőre van rendezve. |
| tulajdonságok. házirendek | A szabályzat-definícióval, a hozzárendeléssel, a hatással és a paraméterekkel kapcsolatos, a házirend kiértékelését eredményező adatokat tartalmazza. |
| relatedEvents | Ez a mező a házirend eseményeinél üres. |

## <a name="mapping-to-diagnostic-logs-schema"></a>Hozzárendelés diagnosztikai naplók sémához

Az Azure-tevékenység naplójának Storage-fiókba vagy Event Hubs névtérbe való továbbításakor az [Azure diagnosztikai naplók sémája](./diagnostic-logs-schema.md)követi az adatátvitelt. Itt látható a fenti séma tulajdonságainak leképezése a diagnosztikai naplók sémájára:

| Diagnosztikai naplók séma tulajdonsága | Műveletnapló REST API Schema tulajdonság | Megjegyzések |
| --- | --- | --- |
| time | EventTimestamp |  |
| resourceId | resourceId | a subscriptionId, a resourceType és a resourceGroupName a resourceId. |
| operationName | operationName. Value |  |
| category | Művelet nevének része | A művelet típusának feltörése – "írás"/"Törlés"/"művelet" |
| resultType | status. Value | |
| resultSignature | alállapot. érték | |
| resultDescription | leírás |  |
| durationMs | N/A | Mindig 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | jogcímek és engedélyezési tulajdonságok |  |
| Szint | Szint |  |
| location | N/A | Az esemény feldolgozásának helye. *Ez nem az erőforrás helye, hanem az eseményt feldolgozták. A rendszer eltávolítja ezt a tulajdonságot egy jövőbeli frissítésben.* |
| Tulajdonságok | properties.eventProperties |  |
| Properties. eventCategory | category | Ha a Properties. eventCategory nincs jelen, a kategória a "rendszergazda" |
| properties.eventName | eventName |  |
| Properties. operationId | operationId |  |
| properties.eventProperties | properties |  |


## <a name="next-steps"></a>Következő lépések
* [További információ a tevékenység naplóról](activity-logs-overview.md)
* [A tevékenység naplójának exportálása az Azure Storage-ba vagy a Event Hubs](activity-log-export.md)

