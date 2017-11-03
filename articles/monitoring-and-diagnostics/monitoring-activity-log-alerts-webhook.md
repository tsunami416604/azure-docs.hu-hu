---
title: "A webhook séma napló tevékenységriasztásokat használt megértése |} Microsoft Docs"
description: "További tudnivalók az fel az a webhook URL-CÍMÉT egy figyelmeztetés a napló aktiválja JSON-séma."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.openlocfilehash: 75c71bcd16573d4f4dd3377c623aa9b414aa3906
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Az Azure tevékenység napló riasztásokhoz Webhookok
Egy művelet csoport definíciójának részeként tevékenység napló riasztási értesítések fogadásának webhook végpontok is konfigurálhat. A webhook a más rendszerekkel utófeldolgozási vagy egyéni műveletek irányíthatja a ezek az értesítések. Ez a cikk bemutatja, mi a hasznos a HTTP POST egy webhook a következőhöz hasonló.

A napló tevékenységriasztásokat további információkért lásd: hogyan [Azure tevékenység létrehozása napló riasztások](monitoring-activity-log-alerts.md).

A művelet csoportok információkért lásd: hogyan [művelet csoportok létrehozása a](monitoring-action-groups.md).

## <a name="authenticate-the-webhook"></a>A webhook hitelesítéséhez
A webhook opcionálisan használhatja engedélyezési jogkivonat-alapú hitelesítésre. A webhook URI mentett jogkivonat-azonosítóval, például `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Hasznos séma
A JSON-adattartalmat a FELADÁS egy vagy több művelet található helytől a tartalom data.context.activityLog.eventSource mező.

###<a name="common"></a>Közös
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
###<a name="administrative"></a>Felügyeleti
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
###<a name="servicehealth"></a>ServiceHealth
```json
{
    "schemaId": "unknown",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "properties": {
                    "title": "...",
                    "service": "...",
                    "region": "...",
                    "communication": "...",
                    "incidentType": "Incident",
                    "trackingId": "...",
                    "groupId": "...",
                    "impactStartTime": "3/29/2017 3:43:21 PM",
                    "impactMitigationTime": "3/29/2017 3:43:21 PM",
                    "eventCreationTime": "3/29/2017 3:43:21 PM",
                    "impactedServices": "[{...}]",
                    "defaultLanguageTitle": "...",
                    "defaultLanguageContent": "...",
                    "stage": "Active",
                    "communicationId": "...",
                    "version": "0.1"
                }
            }
        },
        "properties": {}
    }
}
```

A szolgáltatás állapotának értesítési tevékenység napló riasztások adott séma részletekért lásd: [szolgáltatás állapotával kapcsolatos értesítésekre](monitoring-service-notifications.md).

Más tevékenység napló minden riasztásról adott séma részletekért lásd: [az Azure tevékenységnapló áttekintése](monitoring-overview-activity-logs.md).

| Elem neve | Leírás |
| --- | --- |
| status |A metrika riasztások használt. Beállítása mindig "aktivált" tevékenység napló riasztások esetén. |
| A környezetben |Az esemény környezetében. |
| resourceProviderName |Az erőforrás-szolgáltató az érintett erőforrás. |
| conditionType |Mindig "Event". |
| név |A riasztási szabály neve. |
| id |A riasztás erőforrás-azonosító. |
| leírás |Riasztás leírása állítható be, ha a riasztást hoz létre. |
| subscriptionId |Az Azure előfizetés-azonosító. |
| időbélyeg |Az idő, amelyen az esemény váltotta az Azure-szolgáltatás, amely a kérelem feldolgozása. |
| resourceId |Erőforrás-azonosító az érintett erőforrás. |
| erőforráscsoport-név |Az érintett erőforrás az erőforráscsoport neve. |
| properties |Állítsa be a `<Key, Value>` párok (Ez azt jelenti, hogy `Dictionary<String, String>`), amely tartalmazza az esemény részleteit. |
| Esemény |Az eseménnyel kapcsolatos metaadatokat tartalmazó elemet. |
| Engedélyezési |A szerepköralapú hozzáférés-vezérlés az esemény tulajdonságai. Ezek a Tulajdonságok általában például a művelet, a szerepkör és a hatókör. |
| category |Az esemény kategóriáját. Támogatott értékek a következők: felügyeleti, figyelmeztetés, biztonsági, ServiceHealth és javaslat. |
| hívó |A művelet, a jogcím vagy a rendelkezésre állás alapján SPN jogcím végző felhasználó e-mail címe. Bizonyos rendszerhívások null értékű lehet. |
| correlationId |Általában egy GUID karakterlánc-formátum. A correlationId események ugyanaz a nagyobb művelet tartozik, és általában megosztani az egy correlationId. |
| eventDescription |Az esemény leírása statikus szöveg. |
| eventDataId |Az esemény egyedi azonosítója. |
| Eseményforrás |Az Azure-szolgáltatás vagy az eseményt létrehozó infrastruktúra neve. |
| httpRequest |A kérelem általában tartalmazza a clientRequestId clientIpAddress és HTTP-metódust (például PUT). |
| szint |A következő értékek egyikét: kritikus, hiba, figyelmeztetés, tájékoztatás és részletes. |
| OperationID azonosítójú |Általában egy GUID azonosító az egyetlen műveletben megfelelő események között meg van osztva. |
| operationName |A művelet neve. |
| properties |Az esemény tulajdonságai. |
| status |Karakterlánc. A művelet állapotát. A gyakori értékek a következők: elindítva, folyamatban lévő, sikeres, sikertelen, aktív és megoldva. |
| a részállapot |Általában tartalmazza a megfelelő REST-hívást a HTTP-állapotkódot. A részállapot más karakterláncokat is tartalmazhat. Közös részállapot érték OK (HTTP-állapotkód:: 200), létrehozott (HTTP-állapotkód:: 201-es), fogadja el (HTTP-állapotkód:: 202), a tartalom nem (HTTP-állapotkód: 204), hibás kérés (HTTP-állapotkód:: 400), nem található (HTTP-állapotkód: 404-es), ütközés (HTTP-állapotkód:: 409 ), Belső kiszolgálóhiba (HTTP-állapotkód: 500), a Service nem érhető el (HTTP-állapotkód: 503-as), és az átjáró időtúllépése (HTTP-állapotkód: 504). |

## <a name="next-steps"></a>Következő lépések
* [További információ a tevékenységnapló](monitoring-overview-activity-logs.md).
* [Hajtsa végre az Azure automation-parancsfájlok (Runbookok) Azure riasztások](http://go.microsoft.com/fwlink/?LinkId=627081).
* [A logikai alkalmazás segítségével Twilio keresztül SMS küldése az Azure-riasztás alapján](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Ebben a példában a metrika riasztások, de módosítható egy figyelmeztetés a napló együttműködni.
* [Használja a logikai alkalmazás közzététele a Slack üzenet küldése az Azure-riasztás alapján](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Ebben a példában a metrika riasztások, de módosítható egy figyelmeztetés a napló együttműködni.
* [A logikai alkalmazás használja az Azure-riasztás alapján egy Azure-üzenetsorba való üzenetküldéshez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Ebben a példában a metrika riasztások, de módosítható egy figyelmeztetés a napló együttműködni.
