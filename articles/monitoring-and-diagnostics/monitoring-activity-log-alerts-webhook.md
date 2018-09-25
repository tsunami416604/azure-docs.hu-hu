---
title: A webhook sémáról a tevékenységnapló-riasztások használt ismertetése
description: Ismerje meg a séma a JSON-t egy webhook URL-CÍMÉT, amikor egy tevékenységnapló-riasztás aktiválja tesznek közzé.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: e989406c852b7c87123681dd875f9cd8229524c1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971926"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhookok az Azure tevékenységnapló-riasztások
Műveletcsoport definíciójának részeként a webhook végpontok tevékenység log riasztási értesítések fogadása is beállíthatja. A webhookok ezek az értesítések is átirányítása utófeldolgozási vagy egyéni műveleteket más rendszerekre. Ez a cikk bemutatja, hogy az a HTTP POST a webhook hasznos adatai néz ki.

A tevékenységnapló-riasztások további információkért lásd: hogyan [Azure tevékenységnapló-riasztások létrehozása](monitoring-activity-log-alerts.md).

Műveletcsoportok kapcsolatos tudnivalókat lásd: hogyan [Műveletcsoportok létrehozása a](monitoring-action-groups.md).

## <a name="authenticate-the-webhook"></a>A webhook hitelesítése
A webhook engedélyezési jogkivonat-alapú hitelesítéshez használhatnak. A webhook URI-t is mentve lesz egy jogkivonat-azonosító, például `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Adattartalom-séma
A POST művelet található JSON-adattartalom eltér a hasznos data.context.activityLog.eventSource mezőre.

### <a name="common"></a>Közös
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```
### <a name="administrative"></a>Adminisztratív
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}

```
### <a name="servicehealth"></a>ServiceHealth
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
    "status": "Activated",
    "context": {
        "activityLog": {
        "channels": "Admin",
        "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
        "description": "Active: Virtual Machines - Australia East",
        "eventSource": "ServiceHealth",
        "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
        "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
        "level": "Informational",
        "operationName": "Microsoft.ServiceHealth/incident/action",
        "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
        "properties": {
            "title": "Virtual Machines - Australia East",
            "service": "Virtual Machines",
            "region": "Australia East",
            "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
            "incidentType": "Incident",
            "trackingId": "0NIH-U2O",
            "impactStartTime": "2017-10-18T02:48:00.0000000Z",
            "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
            "defaultLanguageTitle": "Virtual Machines - Australia East",
            "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
            "stage": "Active",
            "communicationId": "636439673646212912",
            "version": "0.1.1"
        },
        "status": "Active",
        "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
        "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

### <a name="resourcehealth"></a>Resource Health
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated",
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

A service health értesítési tevékenységnapló-riasztások adott séma részletekért lásd: [szolgáltatás állapotára vonatkozó értesítések](monitoring-service-notifications.md). Ezen kívül megtudhatja, hogyan [konfigurálása a service health webhook-értesítésekkel a probléma meglévő kezelési megoldásaival való](../service-health/service-health-alert-webhook-guide.md).

Az összes többi tevékenységnapló-riasztások adott séma részletekért lásd: [áttekintése az Azure-tevékenységnapló](monitoring-overview-activity-logs.md).

| Elem neve | Leírás |
| --- | --- |
| status |Metrikákhoz kapcsolódó riasztások használja. Minden esetben állítsa be "aktiválva", a tevékenységnapló-riasztások. |
| Környezet |Az esemény környezetében. |
| erőforrás-szolgáltató neve |Az erőforrás-szolgáltató az érintett erőforrás. |
| conditionType |Mindig "esemény". |
| név |A riasztási szabály neve. |
| id |A riasztás erőforrás-azonosító. |
| leírás |A riasztás létrehozásakor állítsa be a riasztás leírásában. |
| subscriptionId |Az Azure előfizetés-azonosítójára. |
| időbélyeg |Idő, amikor az eseményt az Azure-szolgáltatás, amely a kérelmet feldolgozó jött létre. |
| resourceId |Erőforrás-azonosító az érintett erőforrás. |
| resourceGroupName |Az érintett erőforrás az erőforráscsoport neve. |
| properties |Készlete `<Key, Value>` párok (azaz `Dictionary<String, String>`), amely tartalmazza az esemény részleteit. |
| esemény |Az esemény kapcsolatos metaadatokat tartalmazó elemet. |
| Engedélyezési |A szerepköralapú hozzáférés-vezérlés az esemény tulajdonságai. Ezek a Tulajdonságok általában tartalmazzák a műveletet, a szerepkör és a hatókör. |
| category |Az esemény kategóriája. Támogatott értékek például a felügyeleti, a riasztás, biztonság, ServiceHealth és javaslat. |
| hívó |A művelet, egyszerű Felhasználónévi jogcím vagy egyszerű szolgáltatásnév jogcímet rendelkezésre állása alapján végrehajtó felhasználó e-mail címe. Bizonyos rendszerhívások null értékű lehet. |
| correlationId |Általában egy GUID Azonosítót a karakterlánc-formátum. Korrelációs azonosító események általában megosztani egy korrelációs azonosító, és ugyanaz a nagyobb művelet tartozik. |
| eventDescription |Az esemény statikus szöveges leírása. |
| eventDataId |Az esemény egyedi azonosítója. |
| eventSource |Neve az Azure-szolgáltatások és infrastruktúra, amely az esemény jön létre. |
| Törzsparaméterei |A kérés általában tartalmazza az ügyfélkérelem clientIpAddress és HTTP-metódus (például helyezzen el). |
| szint |A következő értékek egyikét: kritikus fontosságú, hiba, figyelmeztetésként vagy tájékoztató. |
| operationId |Általában egy GUID Azonosítót az egyetlen műveletben megfelelő események között. |
| operationName |A művelet neve. |
| properties |Az esemény tulajdonságai. |
| status |karakterlánc. A művelet állapotát. A gyakori értékek a következők: elindítva, folyamatban lévő, sikeres, sikertelen, aktív és megoldott. |
| a részállapot |Általában tartalmazza a megfelelő REST-hívást HTTP-állapotkódot. A részállapot egyéb karakterláncokat is tartalmazhat. Közös substatus értékek közé tartozik az OK (HTTP-állapotkód:: 200-as) létrehozott (HTTP-állapotkód:: 201-es) elfogadott, (HTTP-állapotkód:: 202), nincs tartalom (HTTP-állapotkód:: 204), hibás kérés (HTTP-állapotkód:: 400), nem található (HTTP-állapotkód:: 404-es), ütközés (HTTP-állapotkód: 409 ), Belső kiszolgálóhiba (HTTP-állapotkód: 500), a Service nem érhető el (HTTP-állapotkód: 503), és az átjáró időtúllépése (HTTP-állapotkód: 504). |

## <a name="next-steps"></a>További lépések
* [További információ a tevékenységnapló](monitoring-overview-activity-logs.md).
* [Az Azure automation-szkriptek (Runbookok) végrehajtása az Azure-riasztások](http://go.microsoft.com/fwlink/?LinkId=627081).
* [Logikai alkalmazás használatával a Twilio segítségével SMS küldése Azure riasztásból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Ebben a példában a metrikákhoz kapcsolódó riasztások, de módosítható egy tevékenységnapló-riasztás dolgozhat.
* [Logikai alkalmazás használatával egy Slack-üzenet küldése egy Azure-riasztásokból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Ebben a példában a metrikákhoz kapcsolódó riasztások, de módosítható egy tevékenységnapló-riasztás dolgozhat.
* [Üzenet küldése az Azure-üzenetsort származó Azure Logic Apps-alkalmazás használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Ebben a példában a metrikákhoz kapcsolódó riasztások, de módosítható egy tevékenységnapló-riasztás dolgozhat.
