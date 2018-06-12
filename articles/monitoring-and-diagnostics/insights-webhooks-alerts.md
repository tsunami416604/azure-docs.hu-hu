---
title: A klasszikus metrika riasztások értesítik a nem Azure rendszerbe olyan webhook rendelkezik
description: 'Útmutató: Azure metrika riasztásokat az egyéb, az-Azure rendszerek átirányítása.'
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 9cc017aad7fbdc740ab3fa3af5603223e5b844ce
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262351"
---
# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>A webhook Azure metrika riasztás konfigurálása
Webhook használhatja az Azure riasztási értesítések továbbításához a más rendszerekkel utófeldolgozási vagy egyéni műveletek. A riasztás olyan webhook segítségével szolgáltatások által küldött SMS-üzenetek, bejelentkezési hibák értesíteni a Csevegés vagy az üzenetkezelési szolgáltatások révén, vagy a különböző műveleteknél csoport irányítja. 

A cikkből megtudhatja, hogyan lehet beállítani a webhook a Azure metrika riasztást. Azt is bemutatja, mi a hasznos a HTTP POST egy webhook a következőhöz hasonló. A telepítő és a séma egy Azure tevékenység napló riasztás (riasztás események), lásd: [hívható meg olyan webhook Azure tevékenység napló riasztást](insights-auditlog-to-webhook-email.md).

Azure riasztásokat küldhet HTTP POST riasztási tartalma JSON formátumban a olyan webhook URI, amely a riasztás létrehozásakor adja meg. A sémát a cikk későbbi részében. Az URI érvényes HTTP vagy HTTPS-végpont kell lennie. Azure visszaküldés kérelmenként egy bejegyzést, egy riasztás aktiválásakor.

## <a name="configure-webhooks-via-the-azure-portal"></a>Konfigurálása webhookokkal az Azure-portálon
Hozzáadása vagy frissítése a webhook URI, az a [Azure-portálon](https://portal.azure.com/), és **Create/Update riasztások**.

![Egy riasztási szabály ablak hozzáadása](./media/insights-webhooks-alerts/Alertwebhook.png)

Beállíthatja úgy is riasztást küldje el a webhook URI használatával [Azure PowerShell-parancsmagok](insights-powershell-samples.md#create-metric-alerts), egy [platformfüggetlen parancssori felület](insights-cli-samples.md#work-with-alerts), vagy [Azure figyelő REST API-k](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>A webhook hitelesítéséhez
A webhook jogkivonat-alapú engedélyezési használatával képes hitelesíteni. A webhook URI mentik egy token. Például:`https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Hasznos séma
A POST műveletet tartalmaz, a következő JSON-adattartalmat és a séma összes metrika-alapú riasztásokhoz:

```JSON
{
    "WebhookName": "Alert1515515157799",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                "metricName": "Requests",
                "metricUnit": "Count",
                "metricValue": "10",
                "threshold": "10",
                "windowSize": "15",
                "timeAggregation": "Average",
                "operator": "GreaterThanOrEqual"
            },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
        },
        "properties": {
            "key1": "value1",
            "key2": "value2"
        }
    }
}
```


| Mező | Kötelező | Rögzített értékhalmazt | Megjegyzések |
|:--- |:--- |:--- |:--- |
| status |I |Aktivált, feloldva |A megadott feltételek alapján a riasztás állapota beállítva. |
| A környezetben |I | |A riasztás környezetét. |
| időbélyeg |I | |Az az idő, amelyen a riasztás működésbe lépett. |
| id |I | |Minden riasztási szabály egyedi rendelkezik. |
| név |I | |A riasztás nevét. |
| leírás |I | |A riasztás leírása. |
| conditionType |I |A metrika esemény |A riasztások két típusok támogatottak: metrika és az esemény. Metrika riasztások metrika feltétel alapulnak. Miatti riasztás a műveletnaplóban esemény alapulnak. Ezt az értéket használatával ellenőrizze, hogy a riasztás metrika vagy esemény alapján-e. |
| feltétel |I | |Ellenőrizze, hogy az adott mezők alapján a **conditionType** érték. |
| metricName |A metrika riasztások | |Meghatározza, hogy a szabály figyeli metrika neve. |
| metricUnit |A metrika riasztások |Bájt, BytesPerSecond, Count, CountPerSecond, százalék, másodpercben |Az egység a mérték megengedett. Lásd: [engedélyezett értékek](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |A metrika riasztások | |A metrika a riasztást kiváltó aktuális értékét. |
| Küszöbérték |A metrika riasztások | |A küszöbérték, amelyen a riasztás aktiválva van. |
| windowSize |A metrika riasztások | |Mennyi ideig használt riasztási figyelése alapján a küszöbértéket. Az érték 5 perc és 1 nap között kell lennie. Az érték az ISO 8601 időtartama formátumúnak kell lennie. |
| timeAggregation |A metrika riasztások |Átlagos, Last, maximális, minimális, None, teljes |A gyűjtött adatok hogyan időbeli használható együtt. Alapértelmezett érték átlaga. Lásd: [engedélyezett értékek](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| Operátor |A metrika riasztások | |Az operátort, amelynek segítségével összehasonlíthatja az aktuális metrika adatokat a beállított küszöbértéket. |
| subscriptionId |I | |Az Azure előfizetés-azonosító. |
| resourceGroupName |I | |Az érintett erőforrás az erőforráscsoport neve. |
| resourceName |I | |Az érintett erőforrás erőforrás neve. |
| resourceType |I | |Az érintett erőforrás erőforrás típusa. |
| resourceId |I | |Az erőforrás-azonosító az érintett erőforrás. |
| resourceRegion |I | |A régió vagy az érintett erőforrás helye. |
| portalLink |I | |A portál erőforrás összefoglaló lap egy közvetlen hivatkozást. |
| properties |N |Optional |Kulcs/érték párok csoportja, amely rendelkezik az esemény részleteit. Például: `Dictionary<String, String>`. A Tulajdonságok mező kitöltése nem kötelező. Egy egyéni felhasználói felületén vagy a logic app-alapú munkafolyamat felhasználók kulcs/érték párok, amelyek átadhatók a tartalom keresztül adhat meg. Egy másik átadandó egyéni tulajdonságok vissza a webhook módja a webhook URI-JÁT magát (lekérdezési paraméterek) keresztül. |

> [!NOTE]
> Beállíthatja a **tulajdonságok** mező csak használatával [Azure figyelő REST API-k](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>További lépések
* További tudnivalók Azure riasztások és a videó webhookok [PagerDuty integrálni Azure riasztások](http://go.microsoft.com/fwlink/?LinkId=627080).
* Megtudhatja, hogyan [hajtható végre az Azure Automation-parancsfájlok (runbookok) Azure riasztások](http://go.microsoft.com/fwlink/?LinkId=627081).
* Megtudhatja, hogyan [logikai alkalmazás segítségével Twilio keresztül egy SMS-üzenet küldése az Azure-riasztás alapján](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Megtudhatja, hogyan [használja a logikai alkalmazás közzététele a Slack üzenet küldése az Azure-riasztás alapján](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Megtudhatja, hogyan [logikai alkalmazás használható az Azure-riasztás alapján egy Azure-üzenetsorba való üzenetküldéshez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).
