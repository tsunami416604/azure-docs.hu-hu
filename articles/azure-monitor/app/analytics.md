---
title: Analytics - a hatékony keresési és a lekérdezési eszköz, az Azure Application Insights |} A Microsoft Docs
description: 'A hatékony diagnosztikai keresés eszköz az Application Insights Analytics áttekintése. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: c41444f94e4685d246de225500c8a5beefc74944
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065613"
---
# <a name="analytics-in-application-insights"></a>Az Application Insights Analytics
Analytics olyan hatékony keresést és lekérdezéseket eszközben [Application Insights](../../application-insights/app-insights-overview.md). Analytics egy olyan webes eszköz, ezért semmilyen beállítást nem szükséges. Ha már konfigurálta az Application Insights egy, az alkalmazások, akkor az alkalmazás adatait elemezheti az alkalmazásából származó Analytics megnyitásával [áttekintő paneljén](../../azure-monitor/app/app-insights-dashboards.md).

![Portal.Azure.com címen nyissa meg, nyissa meg az Application Insights-erőforrást, és kattintson az Analytics.](./media/analytics/001.png)

Is használhatja a [Analytics playground](https://go.microsoft.com/fwlink/?linkid=859557) amely mintaadatokat rengeteg ingyenes bemutató környezetben.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="query-data-in-analytics"></a>Elemzési adatok lekérdezése
Egy tipikus lekérdezést egy táblázat neve, és a egy sorozatát kezdődik *operátorok* elválasztva `|`.
Például keressük meg, hogy hány kérésnek a különböző országokban kapott az elmúlt 3 órában alkalmazást:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

A tábla nevével kezdődik *kérelmek* , és szükség szerint adja hozzá a védőeszközön elemeket.  Először meghatározzuk, tekintse át az elmúlt 3 órában rekordjai csak egy Időszűrő.
Azt, majd száma országonként rekordok száma (, hogy az oszlopban található adat *client_CountryOrRegion*). Végül azt jelennek meg az eredményeket a kördiagram.
<br>

![Lekérdezés eredményei](./media/analytics/030.png)

A nyelv számos vonzó funkciókkal rendelkezik:

* [Szűrő](/azure/kusto/query/whereoperator) a nyers alkalmazás telemetriai adatainak mezőket, beleértve az egyéni tulajdonságok és metrikák alapján.
* [Csatlakozás](/azure/kusto/query/joinoperator) több táblák - correlate kéréseket a lapmegtekintéseket, a függőségi hívások, a kivételek és a nyomkövetési naplók.
* Hatékony statisztikai [összesítések](/azure/kusto/query/summarizeoperator).
* Azonnali és sokoldalú vizualizációkat.
* [REST API-val](https://dev.applicationinsights.io/) használható lekérdezések futtatása programozott módon, például a Powershellből.

A [nyelvi referencia teljes](https://go.microsoft.com/fwlink/?linkid=856079) minden parancs támogatott részleteit, és rendszeresen frissíti.

## <a name="next-steps"></a>További lépések
* Ismerkedés a [Analytics-portál](https://go.microsoft.com/fwlink/?linkid=856587)
* Első lépések [lekérdezések írása](https://go.microsoft.com/fwlink/?linkid=856078)
* Tekintse át a [SQL-felhasználók – adatlap](https://aka.ms/sql-analytics) a leggyakrabban használt nyelv szintaxisát a fordítások.
* Próbálja ki a Analytics a a [playground](https://analytics.applicationinsights.io/demo) az alkalmazás nem adatot küldenek, az Application Insightsba még.
* Tekintse meg a [bevezető videót](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).
