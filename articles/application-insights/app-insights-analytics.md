---
title: "Elemzés - a hatékony keresési és a lekérdezés eszközt az Azure Application Insights |} Microsoft Docs"
description: "Elemzés, a hatékony diagnosztikai eszköz az Application Insights áttekintése. "
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: mbullwin
ms.openlocfilehash: 80a9e248ca50c11ef61a5c50c4986c4f8f4ead9d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="analytics-in-application-insights"></a>Az Application Insightsban elemzés
Elemzés a hatékony keresési és lekérdezés eszköz [Application Insights](app-insights-overview.md). Analytics egy webes eszköz, így nem szükséges. Ha már konfigurálta az Application Insights egy, az alkalmazások, akkor az alkalmazás adatainak elemezheti a alkalmazás Analytics megnyitásával [áttekintése panel](app-insights-dashboards.md).

![Nyissa meg portal.azure.com, nyissa meg az Application Insights-erőforrást, majd kattintson a elemzés.](./media/app-insights-analytics/001.png)

Használhatja a [Analytics playground](https://go.microsoft.com/fwlink/?linkid=859557) Ez nagy mennyiségű adatot tartalmazó egy szabad bemutató környezetben.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="query-data-in-analytics"></a>Az Analytics lekérdezési adatok
Egy tipikus lekérdezés kezdődik-e egy tábla nevét, majd több *operátorok* elválasztott `|`.
Például keressük meg hogy hány kérésnek a különböző országokból az utolsó 3 óra során kapott alkalmazást:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

A táblanév először *kérelmek* , és adja hozzá a védőeszközön elemek szükség szerint.  Először igazolnia idő kapcsolódó szűrő megadásához. csak az utolsó 3 órát rekordok áttekintéséhez.
Azt, majd száma az egyes országok rekordok száma (, hogy az oszlopban található adat *client_CountryOrRegion*). Végül azt jeleníti meg a tortadiagram.
<br>

![Lekérdezés eredményei](./media/app-insights-analytics/030.png)

A nyelv számos vonzó lehetőséggel rendelkezik:

* [Szűrő](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html) által a mezőket, beleértve az egyéni tulajdonságok és a metrikák a nyers app telemetriai adatokat.
* [Csatlakozás](https://docs.loganalytics.io/queryLanguage/query_language_joinoperator.html) több táblázatot – korrelálja Lapmegtekintések, függőségi hívások esetében, kivételeket és naplókivonatokat kéri.
* Hatékony statisztikai [összesítések](https://docs.loganalytics.io/learn/tutorials/aggregations.html).
* Közvetlen és erőteljes képi megjelenítések.
* [REST API](https://dev.applicationinsights.io/) használható lekérdezések futtatása programozott módon, például a Powershellből.

A [teljes nyelvi referencia](https://go.microsoft.com/fwlink/?linkid=856079) minden támogatott parancs adatokat, és rendszeresen frissíti.

## <a name="next-steps"></a>Következő lépések
* Az első lépései a [Analytics portál](https://go.microsoft.com/fwlink/?linkid=856587)
* Első lépések [lekérdezések írásáról](https://go.microsoft.com/fwlink/?linkid=856078)
* Tekintse át a [SQL-felhasználók lap cheat](https://aka.ms/sql-analytics) a leggyakrabban használt idioms kifejezés fordítását.
* Kipróbálása Analytics a a [playground](https://analytics.applicationinsights.io/demo) Ha az alkalmazás nem adatok küldése az Application Insights még.
* Tekintse meg a [bevezető videó](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).