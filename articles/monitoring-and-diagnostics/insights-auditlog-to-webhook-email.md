---
title: A webhook hívható meg egy Azure napló figyelmeztetés (klasszikus)
description: Útmutató a tevékenység naplóeseményeket irányításához az egyéni műveletek más szolgáltatásokkal. Például SMS üzeneteket küldeni, jelentkezzen hibák, vagy egy csoport csevegés vagy üzenetküldési szolgáltatás keresztül értesíti.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: e825d0f2487c20c8c7f3d210d7180b07742d7173
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262460"
---
# <a name="call-a-webhook-on-an-azure-activity-log-alert"></a>A webhook hívható meg egy Azure napló figyelmeztetés
Webhook használhatja az Azure riasztási értesítések továbbításához a más rendszerekkel utófeldolgozás vagy egyéni műveletek. A riasztás olyan webhook segítségével szolgáltatások által küldött SMS-üzenetek, bejelentkezési hibák értesíteni a Csevegés vagy az üzenetkezelési szolgáltatások révén, vagy a különböző műveleteknél csoport irányítja. Is állíthatja be egy figyelmeztetés a napló az e-mailek küldése, ha egy riasztás aktiválva van.

A cikkből megtudhatja, hogyan kell beállítani a webhook kell meghívni, amikor egy Azure tevékenység napló riasztási következik be. Ezenfelül itt látható a hasznos a történő olyan webhook HTTP-KÖZZÉTÉTELLEL néz. A telepítő és a séma Azure metrika riasztási kapcsolatos információkért lásd: [olyan webhook konfigurálása Azure metrika riasztást](insights-webhooks-alerts.md). 

> [!NOTE]
> A szolgáltatás, amely támogatja a webhook hívása a következő Azure tevékenység napló riasztást jelenleg előzetes verzióban érhetők.
>
>

A tevékenység napló riasztást állíthat be [Azure PowerShell-parancsmagok](insights-powershell-samples.md#create-metric-alerts), egy [platformfüggetlen parancssori felület](insights-cli-samples.md#work-with-alerts), vagy [Azure figyelő REST API-k](https://msdn.microsoft.com/library/azure/dn933805.aspx). Az Azure-portál jelenleg nem használható tevékenység napló riasztás beállítása.

## <a name="authenticate-the-webhook"></a>A webhook hitelesítéséhez
A webhook hitelesíthetők ezek a módszerek egyikével:

* **Jogkivonat-alapú engedélyezési**. A webhook URI mentik egy token. Például:`https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
* **Alapszintű engedélyezési**. A webhook URI menti egy felhasználónevet és jelszót. Példa: `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Hasznos séma
A POST műveletet tartalmaz, a következő JSON-adattartalmat és az összes tevékenység napló-alapú értesítések séma. Ebben a sémában hasonlít a metrika-alapú értesítések használatos.

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
| status |A metrika riasztások használt. A napló tevékenységriasztásokat mindig be aktív.|
| A környezetben |Az esemény környezetében. |
| activityLog | A napló az esemény tulajdonságait.|
| Engedélyezési |Az esemény szerepköralapú hozzáférés-vezérlés (RBAC) tulajdonságait. Ezek a Tulajdonságok általában például **művelet**, **szerepkör**, és **hatókör**. |
| művelet | A művelet rögzíti a riasztást. |
| scope | A riasztás (Ez azt jelenti, hogy az erőforrás) hatókörében.|
| csatornák | A műveletet. |
| Jogcímek | Egy gyűjtemény, mert az információk a jogcímek vonatkozik. |
| hívó |A GUID Azonosítóját vagy a művelet, a jogcím vagy a rendelkezésre állás alapján SPN jogcím végző felhasználó felhasználóneve. Bizonyos rendszerhívások null értékű lehet. |
| correlationId |Általában egy GUID karakterlánc-formátum. Az események **correlationId** ugyanaz a nagyobb művelet tartozik. Általában rendelkeznek azonos **correlationId** érték. |
| leírás |A riasztás leírásában a riasztás létrehozásakor megadott. |
| eventSource |Az Azure-szolgáltatásra vagy az eseményt létrehozó infrastruktúra neve. |
| eventTimestamp |Az esemény időpontja. |
| eventDataId |Az esemény egyedi azonosítója. |
| szint |A következő értékek egyikét: kritikus, hiba, figyelmeztetés, tájékoztatás vagy részletes. |
| operationName |A művelet neve. |
| operationId |Általában egy GUID azonosító, amelyet az eseményeket használ. A GUID általában megfelel egy művelettel. |
| resourceId |Az erőforrás-azonosító az érintett erőforrás. |
| resourceGroupName |Az érintett erőforrás az erőforráscsoport neve. |
| resourceProviderName |Az erőforrás-szolgáltató az érintett erőforrás. |
| status |A művelet állapotát jelző karakterlánc-érték. A gyakori értékek a következők: elindítva, folyamatban lévő, sikeres, sikertelen, aktív és megoldva. |
| a részállapot |Általában tartalmazza a megfelelő REST-hívást a HTTP-állapotkódot. A részállapot más karakterláncokat is tartalmazhat. Közös részállapot érték OK (HTTP-állapotkód:: 200), létrehozott (HTTP-állapotkód:: 201-es), fogadja el (HTTP-állapotkód:: 202), a tartalom nem (HTTP-állapotkód: 204), hibás kérés (HTTP-állapotkód:: 400), nem található (HTTP-állapotkód: 404-es), ütközés (HTTP-állapotkód:: 409 ), Belső kiszolgálóhiba (HTTP-állapotkód: 500), a Service nem érhető el (HTTP-állapotkód: 503-as), és az átjáró időtúllépése (HTTP-állapotkód: 504). |
| subscriptionId |Az Azure előfizetés-azonosító. |
| submissionTimestamp |Az az idő, amelyen az esemény váltotta az Azure-szolgáltatás, amely a kérelem feldolgozása. |
| resourceType | Az eseményt létrehozó erőforrás típusa.|
| properties |Kulcs/érték párok csoportja, amely rendelkezik az esemény részleteit. Például: `Dictionary<String, String>`. |

## <a name="next-steps"></a>További lépések
* További információ a [tevékenységnapló](monitoring-overview-activity-logs.md).
* Megtudhatja, hogyan [hajtható végre az Azure Automation-parancsfájlok (runbookok) Azure riasztások](http://go.microsoft.com/fwlink/?LinkId=627081).
* Megtudhatja, hogyan [logikai alkalmazás segítségével Twilio keresztül egy SMS-üzenet küldése az Azure-riasztás alapján](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Ebben a példában a metrika riasztások, de ezt módosíthatja, egy figyelmeztetés a napló együttműködni.
* Megtudhatja, hogyan [használja a logikai alkalmazás közzététele a Slack üzenet küldése az Azure-riasztás alapján](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Ebben a példában a metrika riasztások, de ezt módosíthatja, egy figyelmeztetés a napló együttműködni.
* Megtudhatja, hogyan [logikai alkalmazás használható az Azure-riasztás alapján egy Azure-üzenetsorba való üzenetküldéshez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Ebben a példában a metrika riasztások, de ezt módosíthatja, egy figyelmeztetés a napló együttműködni.
