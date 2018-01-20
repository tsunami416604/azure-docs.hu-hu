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
ms.openlocfilehash: 08467aed4e1601b32598fc42515d9c38b601a9d4
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
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

```json
{
    "WebhookName": "Alert1515526229589",
    "RequestBody": {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "authorization": {
                        "action": "Microsoft.Compute/virtualMachines/deallocate/action",
                        "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1"
                    },
                    "channels": "Operation",
                    "claims": {
                        "aud": "https://management.core.windows.net/",
                        "iss": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "iat": "1234567890",
                        "nbf": "1234567890",
                        "exp": "1234567890",
                        "aio": "Y2NgYBD8ZLlhu27JU6WZsXemMIvVAAA=",
                        "appid": "00000000-0000-0000-0000-000000000000",
                        "appidacr": "2",
                        "e_exp": "262800",
                        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "http://schemas.microsoft.com/identity/claims/objectidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                        "uti": "XnCk46TrDkOQXwo49Y8fAA",
                        "ver": "1.0"
                    },
                    "caller": "00000000-0000-0000-0000-000000000000",
                    "correlationId": "00000000-0000-0000-0000-000000000000",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-01-09T20:11:25.8410967+00:00",
                    "eventDataId": "00000000-0000-0000-0000-000000000000",
                    "level": "Informational",
                    "operationName": "Microsoft.Compute/virtualMachines/deallocate/action",
                    "operationId": "00000000-0000-0000-0000-000000000000",
                    "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
                    "resourceGroupName": "ContosoVM",
                    "resourceProviderName": "Microsoft.Compute",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "submissionTimestamp": "2018-01-09T20:11:40.2986126+00:00",
                    "resourceType": "Microsoft.Compute/virtualMachines"
                }
            },
            "properties": {}
        }
    },
    "RequestHeader": {
        "Expect": "100-continue",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "IcMBroadcaster/1.0",
        "X-CorrelationContext": "RkkKACgAAAACAAAAEADlBbM7x86VTrHdQ2JlmlxoAQAQALwazYvJ/INPskb8S5QzgDk=",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

| Elem neve | Leírás |
| --- | --- |
| status |A metrika riasztások használt. Mindig megadni "aktív" tevékenységnapló riasztások esetén. |
| A környezetben |Az esemény környezetében. |
| activityLog | A napló az esemény tulajdonságait.|
| Engedélyezési |Az esemény RBAC tulajdonságait. Általában ezek közé tartozik, az "action", "szerepkör" és a "hatókörben." |
| művelet | A művelet rögzíti a riasztást. |
| Hatókör | A riasztás (azaz hatókör az erőforrás).|
| csatornák | Művelet |
| Jogcímek | Információk gyűjteménye jelenleg kapcsolódik a jogcímeket. |
| hívó |GUID vagy a művelet, a jogcím vagy a rendelkezésre állás alapján SPN jogcím végző felhasználó felhasználóneve. Bizonyos rendszerhívások null értékű lehet. |
| correlationId |Általában egy GUID karakterlánc-formátum. A correlationId események ugyanaz a nagyobb művelet tartozik, és általában megosztani az egy correlationId. |
| leírás |A riasztás létrehozása során beállított riasztás leírása |
| eventSource |Az Azure-szolgáltatás vagy az eseményt létrehozó infrastruktúra neve. |
| eventTimestamp |Az esemény időpontja. |
| eventDataId |Az esemény egyedi azonosítója. |
| szint |A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés", "Tájékoztató" és "Részletes." |
| operationName |A művelet neve. |
| operationId |Általában egy GUID azonosító az egyetlen műveletben megfelelő események között meg van osztva. |
| resourceId |Erőforrás-azonosító az érintett erőforrás. |
| resourceGroupName |Az érintett erőforrás az erőforráscsoport neve |
| resourceProviderName |Az erőforrás-szolgáltató az érintett erőforrás. |
| status |Karakterlánc. A művelet állapotát. Common values include: "Started", "In Progress", "Succeeded", "Failed", "Active", "Resolved". |
| a részállapot |Általában tartalmazza a megfelelő REST-hívást a HTTP-állapotkódot. A részállapot leíró más karakterláncok is tartalmazhat. Közös részállapot értékek a következők: OK (HTTP-állapotkód:: 200), létrehozott (HTTP-állapotkód:: 201-es), fogadja el (HTTP-állapotkód:: 202), nem a tartalom (HTTP-állapotkód: 204), hibás kérés (HTTP-állapotkód:: 400), nem található (HTTP-állapotkód:: 404-es), ütközés (HTTP-állapotkód:: 409), belső kiszolgálóhiba (HTTP-állapotkód:: 500), szolgáltatás nem érhető el (HTTP-állapotkód:: 503-as), átjáró időtúllépése (HTTP-állapotkód: 504) |
| subscriptionId |Azure Subscription ID. |
| submissionTimestamp |Az idő, amelyen az esemény váltotta az Azure-szolgáltatás, amely a kérelem feldolgozása. |
| resourceType | Az eseményt létrehozó erőforrás típusa.|
| properties |Állítsa be a `<Key, Value>` párok (azaz `Dictionary<String, String>`), amely tartalmazza az esemény részleteit. |

## <a name="next-steps"></a>További lépések
* [További információ a műveletnapló](monitoring-overview-activity-logs.md)
* [Azure Automation-parancsfájlok (Runbookok) végrehajtása Azure riasztások](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Logikai alkalmazás segítségével Twilio keresztül SMS küldése az Azure-riasztás alapján](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Ebben a példában a metrika riasztások, de használható tevékenységnapló riasztást sikerült módosítani.
* [Használja a logikai alkalmazás közzététele a Slack üzenet küldése az Azure-riasztás alapján](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Ebben a példában a metrika riasztások, de használható tevékenységnapló riasztást sikerült módosítani.
* [Üzenet küldése az Azure Queue az Azure-riasztás alapján logikai alkalmazás segítségével](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Ebben a példában a metrika riasztások, de használható tevékenységnapló riasztást sikerült módosítani.
