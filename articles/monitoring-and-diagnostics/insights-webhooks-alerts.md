---
title: "Azure metrika riasztások konfigurálása webhookokkal |} Microsoft Docs"
description: "A más-Azure rendszerekkel Azure riasztások átirányítása."
author: johnkemnetz
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 8b3ae540-1d19-4f3d-a635-376042f8a5bb
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: johnkem
ms.openlocfilehash: 1a885166e5c71f13da222bfc22b0fc579096c52f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>A webhook Azure metrika riasztás konfigurálása
Webhook lehetővé teszik a más rendszerekkel utófeldolgozási vagy egyéni műveletek az Azure riasztási értesítések továbbításához. A riasztás olyan webhook segítségével szolgáltatásokról, amelyek SMS küldése, hibák naplózása, értesítés keresztül Csevegés/üzenetküldési szolgáltatásokban csoport vagy más műveletek többféle irányítja. Ez a cikk ismerteti, hogyan lehet beállítani a webhook a Azure metrika riasztást, és a tartalom a HTTP POST az olyan webhook néz. A telepítő és a séma (riasztás események), Azure tevékenységnapló riasztás [helyette látja ezt a lapot](insights-auditlog-to-webhook-email.md).

Azure riasztások HTTP POST riasztási tartalmát JSON formátumot, egy webhook URI, amely a riasztás létrehozásakor adja meg az alábbi meghatározott séma. Ezt az URI érvényes HTTP vagy HTTPS-végpont kell lennie. Azure visszaküldés kérelmenként egy bejegyzést, egy riasztás aktiválásakor.

## <a name="configuring-webhooks-via-the-portal"></a>Webhookok beállításáról a portálon
Adja hozzá, vagy frissítse a webhook URI a Create/Update riasztások képernyőjén a a [portal](https://portal.azure.com/).

![Riasztási szabály felvétele](./media/insights-webhooks-alerts/Alertwebhook.png)

Riasztást küldje el a webhook URI használatával is konfigurálhatja a [Azure PowerShell-parancsmagok](insights-powershell-samples.md#create-metric-alerts), [platformfüggetlen parancssori felület](insights-cli-samples.md#work-with-alerts), vagy [Azure figyelő REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>A webhook hitelesítése
A webhook jogkivonat-alapú engedélyezési használatával képes hitelesíteni. A webhook URI-t menti jogkivonat-azonosítóval, pl. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Hasznos séma
A POST műveletet tartalmaz, a következő JSON-adattartalmat és az összes metrika-alapú értesítések séma.

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


| Mező | Kötelező | Rögzített értékhalmazt | Megjegyzések |
|:--- |:--- |:--- |:--- |
| status |I |"Aktív", "Megoldott" |A állította ki a feltételek alapján riasztás állapota. |
| A környezetben |I | |A riasztás környezetét. |
| időbélyeg |I | |Az az idő, amelyen a riasztás működésbe lépett. |
| id |I | |Minden riasztási szabály van egyedi azonosítója. |
| név |I | |A riasztás nevét. |
| leírás |I | |A riasztás leírása. |
| conditionType |I |"Metrika", az "Event" |A riasztások két típusok támogatottak. Egy mérték feltétel alapján, a másik a műveletnaplóban esemény alapján. Ez az érték segítségével ellenőrizheti, ha a riasztás alapján esemény vagy mértéket. |
| Az állapot |I | |Az adott mezők kereséséhez a conditionType alapján. |
| metricName |a metrika riasztások | |Meghatározza, hogy a szabály figyeli metrika neve. |
| metricUnit |a metrika riasztások |"Memória", "BytesPerSecond", "Count", "CountPerSecond", "Százaléka", "Seconds" |Az egység a mérték megengedett. [Engedélyezett értékek az itt felsorolt](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |a metrika riasztások | |A metrika a riasztást kiváltó aktuális értékét. |
| Küszöbérték |a metrika riasztások | |A küszöbérték, amelyen a riasztás aktiválva van. |
| Ablakméret |a metrika riasztások | |Az időtartam, amely alapján a küszöbérték értesítési tevékenység figyelésére használható. 5 perc és 1 nap között kell lennie. ISO 8601 időtartama formátumban. |
| timeAggregation |a metrika riasztások |"Átlagos", "Legutóbbi", "Maximális", "Minimális", "None", "teljes" |A gyűjtött adatok hogyan időbeli használható együtt. Alapértelmezett érték átlaga. [Engedélyezett értékek az itt felsorolt](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| Operátor |a metrika riasztások | |Az operátor összehasonlíthatja az aktuális metrika adatokat a beállított küszöbértéket. |
| subscriptionId |I | |Az Azure előfizetés-azonosító. |
| erőforráscsoport-név |I | |Az érintett erőforrás az erőforráscsoport neve. |
| resourceName |I | |Az érintett erőforrás erőforrás nevét. |
| a resourceType |I | |Az érintett erőforrás erőforrás típusát. |
| resourceId |I | |Erőforrás-azonosító az érintett erőforrás. |
| resourceRegion |I | |A régióban vagy az érintett erőforrás helye. |
| portalLink |I | |A portál erőforrás összefoglalás lapon mutató közvetlen hivatkozást. |
| properties |N |Optional |Állítsa be a `<Key, Value>` párok (azaz `Dictionary<String, String>`), amely tartalmazza az esemény részleteit. A Tulajdonságok mező kitöltése nem kötelező. Egy munkafolyamatban egyéni felhasználói felületén vagy a Logic app-alapú felhasználók kulcs és-értékek, amelyek átadhatók a tartalom keresztül adhat meg. Az alternatív átadandó egyéni tulajdonságok vissza a webhook módja a webhook URI-ját magát (lekérdezési paraméterek) keresztül |

> [!NOTE]
> A Tulajdonságok mező csak akkor állítható használatával a [Azure figyelő REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Következő lépések
* További tudnivalók Azure riasztások és a videó webhookok [PagerDuty integrálni Azure riasztások](http://go.microsoft.com/fwlink/?LinkId=627080)
* [Azure Automation-parancsfájlok (Runbookok) végrehajtása Azure riasztások](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Logikai alkalmazás segítségével Twilio keresztül SMS küldése az Azure-riasztás alapján](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
* [Használja a logikai alkalmazás közzététele a Slack üzenet küldése az Azure-riasztás alapján](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
* [Üzenet küldése az Azure Queue az Azure-riasztás alapján a Logic App használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)
