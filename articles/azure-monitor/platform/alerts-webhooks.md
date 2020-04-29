---
title: Webhook meghívása klasszikus metrikai riasztással Azure Monitor
description: Ismerje meg, hogyan irányíthatja át az Azure metrikus riasztásokat más, nem Azure rendszerekre.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: alerts
ms.openlocfilehash: 27510871f9a022cb27c6b03b812ce1d37b47312c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79248982"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Webhook meghívása klasszikus metrikai riasztással Azure Monitor

A webhookok használatával átirányíthat egy Azure-riasztási értesítést más rendszerekre a feldolgozás utáni vagy egyéni műveletekhez. A riasztások segítségével egy webhook használatával átirányíthatja azt a szolgáltatásba, amely SMS-üzeneteket küld, a hibák naplózása, a csapat csevegési vagy üzenetküldési szolgáltatásokon keresztüli értesítése, illetve egyéb műveletek esetén. 

Ez a cikk azt ismerteti, hogyan állítható be egy webhook egy Azure metrikai riasztásban. Emellett azt is bemutatja, hogy a HTTP-közzététel milyen hasznos adatokat keres egy webhookban. További információ az Azure-beli tevékenységekre vonatkozó riasztások beállításáról és sémáról (riasztás az eseményekről): [webhook meghívása Azure-beli tevékenység naplójának riasztására](alerts-log-webhook.md).

Az Azure-riasztások a HTTP POST paranccsal küldik el a riasztás tartalmát JSON formátumban a riasztás létrehozásakor megadott webhook URI-hoz. A séma a cikk későbbi részében van meghatározva. Az URI azonosítónak érvényes HTTP-vagy HTTPS-végpontnak kell lennie. Az Azure a riasztások aktiválása után egy bejegyzést kér be.

## <a name="configure-webhooks-via-the-azure-portal"></a>Webhookok konfigurálása a Azure Portal használatával
A webhook URI-azonosítójának hozzáadásához vagy frissítéséhez lépjen a [Azure Portal](https://portal.azure.com/)a **riasztások létrehozása/frissítése**elemre.

![Riasztási szabály hozzáadása panel](./media/alerts-webhooks/Alertwebhook.png)

Az [Azure PowerShell-parancsmagok](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts), [platformfüggetlen parancssori](../../azure-monitor/platform/cli-samples.md#work-with-alerts) [felület vagy Azure monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx)-k használatával riasztást is beállíthat a webhook URI-jának közzétételéhez.

## <a name="authenticate-the-webhook"></a>A webhook hitelesítése
A webhook a jogkivonat-alapú hitelesítés használatával tud hitelesítést végezni. A webhook URI-ja a jogkivonat-AZONOSÍTÓval lett mentve. Például:`https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Hasznos adatok sémája
A POST művelet a következő JSON-adattartalmat és sémát tartalmazza az összes metrika-alapú riasztáshoz:

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


| Mező | Kötelező | Rögzített értékek halmaza | Megjegyzések |
|:--- |:--- |:--- |:--- |
| status |I |Aktiválva, megoldva |A riasztás állapota a beállított feltételek alapján. |
| összefüggésben |I | |A riasztás kontextusa. |
| időbélyeg |I | |A riasztás aktiválásának időpontja. |
| id |I | |Minden riasztási szabály egyedi AZONOSÍTÓval rendelkezik. |
| név |I | |A riasztás neve. |
| leírás |I | |A riasztás leírása. |
| conditionType |I |Metrika, esemény |Két típusú riasztás támogatott: metrika és esemény. A metrikai riasztások metrikai feltételen alapulnak. Az események riasztásai a tevékenység naplójában lévő eseményen alapulnak. Ezzel az értékkel ellenőrizhető, hogy a riasztás metrikán vagy eseményen alapul-e. |
| feltétel |I | |A **conditionType** érték alapján ellenőrizhető konkrét mezők. |
| metricName |Metrikus riasztások esetén | |Annak a mérőszámnak a neve, amely meghatározza, hogy mi a szabály figyeli. |
| metricUnit |Metrikus riasztások esetén |Bájtok, BytesPerSecond, Darabszám, CountPerSecond, százalék, másodperc |A mérőszámban engedélyezett egység. Lásd az [engedélyezett értékeket](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |Metrikus riasztások esetén | |A riasztást kiváltó metrika tényleges értéke. |
| threshold |Metrikus riasztások esetén | |Az a küszöbérték, amelyen a riasztás aktiválva van. |
| windowSize |Metrikus riasztások esetén | |A riasztási tevékenység küszöbérték alapján történő figyeléséhez használt időtartam. Az értéknek 5 perc és 1 nap közé kell esnie. Az értéknek ISO 8601 időtartam formátumúnak kell lennie. |
| timeAggregation |Metrikus riasztások esetén |Átlag, utolsó, maximum, minimum, none, összesen |Az összegyűjtött adatok időbeli összevonása. Az alapértelmezett érték az átlag. Lásd az [engedélyezett értékeket](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operátor |Metrikus riasztások esetén | |Az aktuális metrikai adatok meghatározott küszöbértékhez való összehasonlításához használt operátor. |
| subscriptionId |I | |Az Azure-előfizetés azonosítója. |
| resourceGroupName |I | |Az érintett erőforráshoz tartozó erőforráscsoport neve. |
| resourceName |I | |Az érintett erőforrás neve. |
| resourceType |I | |Az érintett erőforrás erőforrástípus. |
| resourceId |I | |Az érintett erőforrás erőforrás-azonosítója. |
| resourceRegion |I | |Az érintett erőforrás régiója vagy helye. |
| portalLink |I | |Közvetlen hivatkozás a portál erőforrás-összefoglalás lapjára. |
| properties |N |Optional |Az esemény részleteit tartalmazó kulcs/érték párok halmaza. Például: `Dictionary<String, String>`. A Properties (Tulajdonságok) mező nem kötelező. Egyéni felhasználói felületen vagy logikai alkalmazáson alapuló munkafolyamatban a felhasználók megadhatják azokat a kulcs/érték párokat, amelyek átadhatók a hasznos adatokon keresztül. Az egyéni tulajdonságok a webhookba való visszaállításának másik módja a webhook URI-ja (lekérdezési paraméterek). |

> [!NOTE]
> A **Properties (Tulajdonságok** ) mezőt csak [Azure monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx)-k használatával állíthatja be.
>
>

## <a name="next-steps"></a>További lépések
* További információ az Azure-beli riasztásokról és webhookokról a videóban az [Azure-riasztások integrálása a PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080)-mel.
* Megtudhatja, hogyan [hajthat végre Azure Automation szkripteket (runbookok) az Azure-riasztásokon](https://go.microsoft.com/fwlink/?LinkId=627081).
* Ismerje meg, hogyan [KÜLDHET SMS-üzenetet a logikai alkalmazással egy Azure-riasztásból a Twilio-on keresztül](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Megtudhatja, hogyan lehet [logikai alkalmazás használatával Slack-üzenetet küldeni egy Azure-riasztásból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Megtudhatja, hogyan [küldhet egy Azure-riasztást egy logikai alkalmazással](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)egy Azure-üzenetsor számára.

