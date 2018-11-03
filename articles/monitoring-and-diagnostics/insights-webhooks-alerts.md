---
title: Klasszikus metrikariasztás értesítése egy webhook használatával nem Azure-beli system rendelkezik
description: Ismerje meg, hogyan irányítsa át az Azure metrikákhoz kapcsolódó riasztások más, nem Azure-beli rendszerekhez.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 0f08e63d6cbf5be8667b1fd61556e3f33e31d06f
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957658"
---
# <a name="have-a-classic-metric-alert-notify-a-non-azure-system-using-a-webhook"></a>Klasszikus metrikariasztás értesítése egy webhook használatával nem Azure-beli system rendelkezik
Webhookok használatával irányíthatja a riasztási értesítés az Azure utólagos feldolgozási vagy egyéni műveleteket más rendszerekre. A riasztás egy webhook használatával átirányítása a szolgáltatások által küldött SMS-ezni, hibák, csevegési vagy az üzenetkezelési szolgáltatások révén, vagy a különböző műveleteknél csapat értesítése bejelentkezni. 

Ez a cikk ismerteti, hogyan lehet beállítani a webhook a egy Azure metrikariasztás. Azt is bemutatja, hogy az a HTTP POST a webhook hasznos adatai néz ki. További információ a telepítő és a egy Azure-tevékenységi sémája (riasztás események), riasztás: [webhook meghívására az Azure tevékenységnapló-riasztás](monitor-alerts-unified-log-webhook.md).

Azure-riasztások HTTP POST JSON formátumban küldendő riasztási tartalma egy webhook URI-t, adja meg a riasztás létrehozásakor használja. A sémát a cikk későbbi részében. Az URI érvényes HTTP vagy HTTPS-végpontot kell lennie. Azure kérelmenként egy bejegyzést tesz közzé, ha egy riasztás aktiválva van.

## <a name="configure-webhooks-via-the-azure-portal"></a>Webhookok az Azure Portalon keresztül konfigurálása
Hozzáadandó vagy frissítendő a webhook URI-t, az a [az Azure portal](https://portal.azure.com/), lépjen a **Create/Update riasztások**.

![Adjon hozzá egy riasztási szabály panelen](./media/insights-webhooks-alerts/Alertwebhook.png)

Beállíthatja egy riasztás küldése a webhook URI használatával [Azure PowerShell-parancsmagok](insights-powershell-samples.md#create-metric-alerts), amely egy [platformfüggetlen CLI](insights-cli-samples.md#work-with-alerts), vagy [Azure Monitor REST API-k](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>A webhook hitelesítése
A webhook jogkivonat-alapú hitelesítést végezhet hitelesítést. A webhook URI-t is mentve lesz egy token. Például:`https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Adattartalom-séma
A POST művelet a következő JSON-adattartalom és az összes mérőszám-alapú értesítések séma tartalmazza:

```JSON
{
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
```


| Mező | Kötelező | Rögzített számú értékek | Megjegyzések |
|:--- |:--- |:--- |:--- |
| status |I |Aktivált, feloldva |A feltételek alapján a riasztás állapotának beállítása. |
| Környezet |I | |A riasztás környezetét. |
| időbélyeg |I | |Az idő, amelyen a riasztás lett elindítva. |
| id |I | |Minden riasztási szabály tartozik egy egyedi azonosítót. |
| név |I | |A riasztás neve. |
| leírás |I | |A riasztás leírása. |
| conditionType |I |A metrika esemény |A riasztások két típusok támogatottak: metrika- és esemény. Metrikákhoz kapcsolódó riasztások egy metrikai feltétel teljesülésekor alapulnak. Egy esemény a tevékenységnaplóban miatti riasztás alapul. Ez az érték használatával ellenőrizze-e a riasztás egy metrikát, illetve egy esemény alapul-e. |
| feltétel |I | |Ellenőrizze, hogy az adott mezők alapján a **conditionType** értéket. |
| MetricName |A metrikákhoz kapcsolódó riasztások | |A mérőszám, amely meghatározza, mit figyeli a szabály neve. |
| metricUnit |A metrikákhoz kapcsolódó riasztások |Bájt, BytesPerSecond, Count, CountPerSecond, %, másodperc |Az egység a metrika az engedélyezett. Lásd: [megengedett értékek](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |A metrikákhoz kapcsolódó riasztások | |A metrika a riasztást kiváltó tényleges értéke. |
| Küszöbérték |A metrikákhoz kapcsolódó riasztások | |A küszöbérték, a riasztás aktiválódik. |
| windowSize |A metrikákhoz kapcsolódó riasztások | |Az az időtartam, használt figyelése a riasztás a küszöbérték alapján. Az érték 5 perc és 1 nap között kell lennie. Az értéknek időtartama ISO 8601 formátumban kell lennie. |
| timeAggregation |A metrikákhoz kapcsolódó riasztások |Átlagos, Last, maximális, minimális, None, összesen |A gyűjtött adatokat hogyan idővel nelze kombinovat. Az alapértelmezett érték: átlaga. Lásd: [megengedett értékek](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| Operátor |A metrikákhoz kapcsolódó riasztások | |Az operátort, amelynek a szolgál hasonlítani a jelenlegi metrikaadatok a beállított küszöbértéket. |
| subscriptionId |I | |Az Azure-előfizetés azonosítóját. |
| resourceGroupName |I | |Az erőforráscsoport az érintett erőforrás neve. |
| Erőforrásnév |I | |Az érintett erőforrás erőforrás neve. |
| resourceType |I | |Az érintett erőforrás erőforrás típusa. |
| resourceId |I | |Az érintett erőforrás erőforrás-Azonosítóját. |
| resourceRegion |I | |A régió vagy az érintett erőforrás helyét. |
| portalLink |I | |Portál erőforrás összegzés lapján közvetlen hivatkozás. |
| properties |N |Optional |Kulcs/érték párok készletét, amely rendelkezik az esemény részleteit. Például: `Dictionary<String, String>`. A Tulajdonságok mező kitöltése nem kötelező. Egy egyéni felhasználói felületen vagy a logic app-alapú munkafolyamat a felhasználók kulcs/érték párok, amelyek használatával a hasznos átadhatók adhat meg. Egyéni tulajdonságok haladnak vissza a webhook a másik módszere van, a webhook (a lekérdezési paraméterek) maga URI-n keresztül. |

> [!NOTE]
> Beállíthatja a **tulajdonságok** mezőt csak használatával [Azure Monitor REST API-k](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>További lépések
* További információ az Azure-riasztások és a webhookok a videóban [a pagerduty segítségével integrálhatja Azure alerts](http://go.microsoft.com/fwlink/?LinkId=627080).
* Ismerje meg, hogyan [Azure Automation-szkriptek (runbookok) végrehajtása az Azure-riasztások](http://go.microsoft.com/fwlink/?LinkId=627081).
* Ismerje meg, hogyan [Logic Apps-alkalmazás használatával küldjön SMS-t, Twilio-n keresztül az Azure riasztásból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Ismerje meg, hogyan [Logic Apps-alkalmazás használatával egy Slack-üzenet küldése egy Azure-riasztásokból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Ismerje meg, hogyan [üzenet küldése az Azure-üzenetsort származó Azure Logic Apps-alkalmazás használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).
