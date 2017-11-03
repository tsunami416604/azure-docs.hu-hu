---
title: "A webhook hívható meg Azure tevékenységnapló riasztások |} Microsoft Docs"
description: "Útvonal tevékenység alkalmazásnapló-események az egyéb szolgáltatásokkal egyéni műveletek. Például SMS küldése, jelentkezzen hibák, vagy egy csoport Csevegés/üzenetküldési szolgáltatáson keresztül értesíti."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 64d333d1-7f37-4a00-9d16-dda6e69a113b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: johnkem
ms.openlocfilehash: 341ab32ad0ec691285fbf1537ee298ab30156a5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="call-a-webhook-on-azure-activity-log-alerts"></a>A webhook hívható meg Azure tevékenységnapló riasztások
Webhook lehetővé teszik a más rendszerekkel utófeldolgozási vagy egyéni műveletek az Azure riasztási értesítések továbbításához. A riasztás olyan webhook segítségével szolgáltatásokról, amelyek SMS küldése, hibák naplózása, értesítés keresztül Csevegés/üzenetküldési szolgáltatásokban csoport vagy más műveletek többféle irányítja. A cikkből megtudhatja, hogyan kell beállítani a webhook kell meghívni, amikor egy Azure tevékenységnapló riasztási következik be. Ezenfelül itt látható a hasznos a történő olyan webhook HTTP-KÖZZÉTÉTELLEL néz. A telepítő és a séma Azure metrika riasztás [helyette látja ezt a lapot](insights-webhooks-alerts.md). Is állíthatja be tevékenységnapló riasztást aktiválásakor e-mail üzenetek küldéséhez.

> [!NOTE]
> Ez a funkció jelenleg előzetes verzióban érhetők, és a jövőben bármikor törlődik.
>
>

Egy tevékenység naplója riasztási használatával állíthat be a [Azure PowerShell-parancsmagok](insights-powershell-samples.md#create-metric-alerts), [platformfüggetlen parancssori felület](insights-cli-samples.md#work-with-alerts), vagy [Azure figyelő REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx). Jelenleg nem állítható be az Azure portál segítségével egy.

## <a name="authenticating-the-webhook"></a>A webhook hitelesítése
A webhook képes hitelesíteni az alábbi módszerek valamelyikével:

1. **Jogkivonat-alapú engedélyezési** -URI mentett jogkivonat-azonosítóval, például a webhook`https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2. **Alapszintű engedélyezési** -URI mentik felhasználónévvel és jelszóval, például a webhook`https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Hasznos séma
A POST műveletet tartalmaz, a következő JSON-adattartalmat és az összes tevékenységnapló-alapú értesítések séma. Ebben a sémában hasonlít a metrika-alapú értesítések használják.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

| Elem neve | Leírás |
| --- | --- |
| status |A metrika riasztások használt. Mindig megadni "aktív" tevékenységnapló riasztások esetén. |
| A környezetben |Az esemény környezetében. |
| resourceProviderName |Az erőforrás-szolgáltató az érintett erőforrás. |
| conditionType |Mindig "Event". |
| név |A riasztási szabály neve. |
| id |A riasztás erőforrás-azonosító. |
| leírás |A riasztás létrehozása során beállított riasztás leírása |
| subscriptionId |Az Azure előfizetés-azonosító. |
| időbélyeg |Az idő, amelyen az esemény váltotta az Azure-szolgáltatás, amely a kérelem feldolgozása. |
| resourceId |Erőforrás-azonosító az érintett erőforrás. |
| erőforráscsoport-név |Az érintett erőforrás az erőforráscsoport neve |
| properties |Állítsa be a `<Key, Value>` párok (azaz `Dictionary<String, String>`), amely tartalmazza az esemény részleteit. |
| Esemény |Az eseménnyel kapcsolatos metaadatokat tartalmazó elemnek. |
| Engedélyezési |Az esemény RBAC tulajdonságait. Általában ezek közé tartozik, az "action", "szerepkör" és a "hatókörben." |
| category |Az esemény kategóriáját. Támogatott értékek a következők: felügyeleti, a riasztásra, biztonság, ServiceHealth, javaslat. |
| hívó |A művelet, a jogcím vagy a rendelkezésre állás alapján SPN jogcím végző felhasználó e-mail címe. Bizonyos rendszerhívások null értékű lehet. |
| correlationId |Általában egy GUID karakterlánc-formátum. A correlationId események ugyanaz a nagyobb művelet tartozik, és általában megosztani az egy correlationId. |
| eventDescription |Az esemény leírása statikus szöveg. |
| eventDataId |Az esemény egyedi azonosítója. |
| Eseményforrás |Az Azure-szolgáltatás vagy az eseményt létrehozó infrastruktúra neve. |
| httpRequest |Általában a "clientRequestId", "clientIpAddress" és "method" tartalmazza (pl. PUT HTTP-metódus). |
| szint |A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés", "Tájékoztató" és "Részletes." |
| OperationID azonosítójú |Általában egy GUID azonosító az egyetlen műveletben megfelelő események között meg van osztva. |
| operationName |A művelet neve. |
| properties |Az esemény tulajdonságai. |
| status |Karakterlánc. A művelet állapotát. Gyakori értékek a következők: "Started", "Folyamatban", "Sikeres", "Sikertelen", "Active", "Megoldott". |
| a részállapot |Általában tartalmazza a megfelelő REST-hívást a HTTP-állapotkódot. A részállapot leíró más karakterláncok is tartalmazhat. Közös részállapot értékek a következők: OK (HTTP-állapotkód:: 200), létrehozott (HTTP-állapotkód:: 201-es), fogadja el (HTTP-állapotkód:: 202), nem a tartalom (HTTP-állapotkód: 204), hibás kérés (HTTP-állapotkód:: 400), nem található (HTTP-állapotkód:: 404-es), ütközés (HTTP-állapotkód:: 409), belső kiszolgálóhiba (HTTP-állapotkód:: 500), szolgáltatás nem érhető el (HTTP-állapotkód:: 503-as), átjáró időtúllépése (HTTP-állapotkód: 504) |

## <a name="next-steps"></a>Következő lépések
* [További információ a műveletnapló](monitoring-overview-activity-logs.md)
* [Azure Automation-parancsfájlok (Runbookok) végrehajtása Azure riasztások](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Logikai alkalmazás segítségével Twilio keresztül SMS küldése az Azure-riasztás alapján](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Ebben a példában a metrika riasztások, de használható tevékenységnapló riasztást sikerült módosítani.
* [Használja a logikai alkalmazás közzététele a Slack üzenet küldése az Azure-riasztás alapján](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Ebben a példában a metrika riasztások, de használható tevékenységnapló riasztást sikerült módosítani.
* [Üzenet küldése az Azure Queue az Azure-riasztás alapján logikai alkalmazás segítségével](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Ebben a példában a metrika riasztások, de használható tevékenységnapló riasztást sikerült módosítani.
