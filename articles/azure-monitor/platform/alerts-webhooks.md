---
title: Webhook hívása klasszikus metrikariasztással az Azure Monitorban
description: Ismerje meg, hogyan irányíthat át az Azure-metrikariasztásokat más, nem Azure-rendszerekbe.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: alerts
ms.openlocfilehash: 27510871f9a022cb27c6b03b812ce1d37b47312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248982"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Webhook hívása klasszikus metrikariasztással az Azure Monitorban

Webhookok segítségével továbbíthatja az Azure-riasztási értesítést más rendszerek utófeldolgozási vagy egyéni műveletek. A riasztáswebhook használatával átirányíthatja az SMS-üzeneteket küldő szolgáltatásokhoz, naplózhatja a hibákat, csevegésen vagy üzenetküldő szolgáltatásokon keresztül értesítheti a csapatot, vagy különböző egyéb műveleteket. 

Ez a cikk ismerteti, hogyan állíthat be egy webhook egy Azure-metrika riasztást. Azt is megmutatja, hogy a HTTP-POST hasznos teher egy webhook-hoz hogyan néz ki. Az Azure-tevékenységnapló-riasztás (eseményekre vonatkozó riasztás) beállításáról és sémájáról az [Azure-tevékenységnapló-riasztás webhook hívása című](alerts-log-webhook.md)témakörben talál további információt.

Az Azure-riasztások http-post használatával a riasztás tartalmát JSON formátumban egy webhook URI, amely a riasztás létrehozásakor megadott. A séma későbbi ebben a cikkben van definiálva. Az URI-nak érvényes HTTP- vagy HTTPS-végpontnak kell lennie. Az Azure kérésenként egy bejegyzést tesz közzé, ha egy riasztás aktiválva van.

## <a name="configure-webhooks-via-the-azure-portal"></a>Webhookok konfigurálása az Azure Portalon keresztül
A webhook URI hozzáadásához vagy frissítéséhez az [Azure Portalon](https://portal.azure.com/)nyissa meg a **Riasztások létrehozása/frissítése**című részt.

![Riasztási szabály ablaktábla hozzáadása](./media/alerts-webhooks/Alertwebhook.png)

A webhook URI-ba való közzétételre vonatkozó riasztást [azure PowerShell-parancsmagok](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts), [egy platformfüggetlen CLI](../../azure-monitor/platform/cli-samples.md#work-with-alerts)vagy az Azure Monitor REST API-k használatával is [beállíthatja.](https://msdn.microsoft.com/library/azure/dn933805.aspx)

## <a name="authenticate-the-webhook"></a>A webhook hitelesítése
A webhook jogkivonat-alapú engedélyezés használatával hitelesíthető. A webhook URI egy jogkivonat-azonosítóval menti. Például:`https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Hasznos adatséma
A POST művelet a következő JSON-tartalom- és sémát tartalmazza az összes metrikaalapú riasztáshoz:

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


| Mező | Kötelező | Rögzített értékhalmaz | Megjegyzések |
|:--- |:--- |:--- |:--- |
| status |I |Aktiválva, Megoldva |A riasztás állapota a beállított feltételek alapján. |
| Összefüggésben |I | |A riasztási környezet. |
| időbélyeg |I | |A riasztás indításának időpontja. |
| id |I | |Minden riasztási szabály egyedi azonosítóval rendelkezik. |
| név |I | |A riasztás neve. |
| leírás |I | |A riasztás leírása. |
| conditionType (feltételtípus) |I |Metrika, esemény |Kétféle riasztás támogatott: metrika és esemény. Metrika riasztások egy metrika feltétel alapján. Az eseményriasztások a tevékenységnaplóban lévő eseményen alapulnak. Ezzel az értékkel ellenőrizheti, hogy a riasztás egy metrika vagy egy esemény alapján. |
| Feltétel |I | |A **conditionType** érték alapján ellenőrizandó mezők. |
| metricName |Metrikariasztások esetén | |A metrika neve, amely meghatározza, hogy mit figyel a szabály. |
| metricUnit (metrikus egység) |Metrikariasztások esetén |Bájt,BytesPerSecond, Count, CountPerSecond, Percent, Másodperc |A metrikában engedélyezett egység. Lásd [az engedélyezett értékeket](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |Metrikariasztások esetén | |A riasztást okozó metrika tényleges értéke. |
| threshold |Metrikariasztások esetén | |Az a küszöbérték, amelynél a riasztás aktiválódik. |
| ablakméret |Metrikariasztások esetén | |A riasztási tevékenység figyelésére a küszöbérték alapján használt időtartam. Az értéknek 5 perc és 1 nap között kell lennie. Az értéknek ISO 8601 időtartam formátumban kell lennie. |
| timeAggregation |Metrikariasztások esetén |Átlag, Utolsó, Maximum, Minimum, Nincs, Összes |Az összegyűjtött adatok idővel való kombinálásának módja. Az alapértelmezett érték az Átlag. Lásd [az engedélyezett értékeket](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operátor |Metrikariasztások esetén | |Az operátor, amely az aktuális metrikaadatok összehasonlítására szolgál a beállított küszöbértékkel. |
| subscriptionId |I | |Az Azure-előfizetés azonosítója. |
| resourceGroupName |I | |Az érintett erőforrás erőforráscsoportjának neve. |
| resourceName |I | |Az érintett erőforrás erőforrásneve. |
| resourceType |I | |Az érintett erőforrás erőforrástípusa. |
| resourceId |I | |Az érintett erőforrás erőforrásazonosítója. |
| resourceRegion (erőforrásrégió) |I | |Az érintett erőforrás régiója vagy helye. |
| portalLink |I | |Közvetlen hivatkozás a portálerőforrás-összefoglaló lapra. |
| properties |N |Optional |Kulcs-/értékpárok készlete, amely az esemény részleteit tartalmazza. Például: `Dictionary<String, String>`. A tulajdonságok mező nem kötelező. Egy egyéni felhasználói felületen vagy logikai alkalmazás-alapú munkafolyamatban a felhasználók kulcs-/értékpárokat adhatnak meg, amelyek a hasznos adaton keresztül adhatók át. Egy másik módja az egyéni tulajdonságok visszaad a webhook keresztül webhook URI magát (lekérdezési paraméterek). |

> [!NOTE]
> A **tulajdonságok** mezőt csak az [Azure Monitor REST API-k](https://msdn.microsoft.com/library/azure/dn933805.aspx)használatával állíthatja be.
>
>

## <a name="next-steps"></a>További lépések
* Tudjon meg többet az Azure-riasztásokról és webhookokról a Videóban [Az Azure-riasztások integrálása a PagerDuty alkalmazással](https://go.microsoft.com/fwlink/?LinkId=627080)című videóban.
* Ismerje meg, hogyan hajthatja végre az [Azure Automation-parancsfájlokat (runbookokat) az Azure-riasztásokon.](https://go.microsoft.com/fwlink/?LinkId=627081)
* Ismerje meg, hogyan [használhat logikai alkalmazást SMS-üzenet küldésére Twilio-n keresztül egy Azure-riasztásból.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
* Megtudhatja, hogyan [küldhet slack-üzenetet egy Azure-riasztásból egy logikai alkalmazás használatával.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
* Megtudhatja, hogyan [küldhet üzenetet egy Azure-várólistába egy Azure-riasztásból egy logikai alkalmazás használatával.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

