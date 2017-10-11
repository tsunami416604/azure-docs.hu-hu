---
title: Az Azure Application Insights for ASP.NET Core |} Microsoft Docs
description: "Webalkalmazások rendelkezésre állását, teljesítményét és használatának a figyelheti."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: d86495eea467977f6c079de72e2b49a2a1da2b60
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="application-insights-for-aspnet-core"></a>ASP.Net Core-hoz készült Application Insights
[Az Application Insights](app-insights-overview.md) lehetővé teszi, hogy a webalkalmazás rendelkezésre állását, teljesítményét és használatának figyelése. A széles körben elérhető módon működő alkalmazások teljesítményével és hatékonyságával kapcsolatos visszajelzések birtokában tájékozott döntéseket hozhat a fejlesztés irányát illetően az egyes fejlesztési fázisokban.

![Példa](./media/app-insights-asp-net-core/sample.png)

Szüksége lesz egy előfizetés [Microsoft Azure](http://azure.com). Jelentkezzen be egy Microsoft-fiókkal – ez tartozhat a Windowshoz, az XBox Live-hoz vagy egyéb Microsoft felhőszolgáltatásokhoz. A csoport lehet egy szervezeti Azure-előfizetéssel: a nyilvános venni azt a Microsoft-fiókjával.

## <a name="getting-started"></a>Bevezetés

* A Visual Studio Solution Explorerben kattintson jobb gombbal a projektre, és válassza ki **konfigurálja az Application Insights**, vagy **Hozzáadás > Application Insights**. [További információk](app-insights-asp-net.md).
* Ha nem látja ezeket menüparancsai, kövesse a [manuális első lépések útmutató](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started). Erre a projekt egy Visual Studio verziója előtt 2017 hozták létre, ha szeretne.

## <a name="using-application-insights"></a>Az Application Insights használata
Jelentkezzen be a [Microsoft Azure-portálon](https://portal.azure.com), jelölje be **összes erőforrás** vagy **Application Insights**, majd válassza ki a figyelheti az alkalmazást a létrehozott erőforrás.

Egy külön böngészőablakban használja az alkalmazást egy ideig. Láthatja, hogy az Application Insights diagramok szereplő adatok. (Lehetséges, hogy a frissítés gombra.) Nem lesznek csak kisebb mennyiségű adatot közben kidolgozása, de a diagramokat valóban származnak életben tegye közzé az alkalmazást, és a sok felhasználóval rendelkezik. 

A – Áttekintés lapon látható legfontosabb teljesítményi diagramok: kiszolgáló válaszideje, lapbetöltési idő és a sikertelen kérelmek száma. Kattintson bármelyik olyan diagram további diagramok és adatok megjelenítéséhez.

A portál nézetek három fő kategóriába sorolhatók:

* [Metrikaböngésző](app-insights-metrics-explorer.md) diagramjait és a metrikák és számát, például válaszidejét, hiba díjszabás vagy a saját metrikákat jeleníti meg a [API](app-insights-api-custom-events-metrics.md). Szűrés, és szegmentálja a data tulajdonság értékével jobban megismerheti a felhasználók és az alkalmazás.
* [Keresés Explorer](app-insights-diagnostic-search.md) események, például bizonyos kérésekre, kivételek, naplókivonatokat vagy magának a létrehozott események felsorolja a [API](app-insights-api-custom-events-metrics.md). Szűrje és az események kereséséhez, és keresse meg kell vizsgálni a problémákat a kapcsolódó események között.
* [Elemzés](app-insights-analytics.md) lehetővé teszi, hogy az SQL-szerű lekérdezéseket futtatnia a telemetriai adatokat, és a hatékony analitikai és diagnosztikai eszköz.

## <a name="alerts"></a>Riasztások
* Automatikusan elérhetővé [proaktív diagnosztikai riasztások](app-insights-proactive-diagnostics.md) , amely közli, hiba sebességét és más metrikákkal rendellenes változásaira vonatkozó.
* Állítson be [rendelkezésreállás figyelésére szolgáló tesztek](app-insights-monitor-web-app-availability.md) tesztelje a webhelyet, folyamatosan helyekről világszerte, és e-mailek, ahogy bármely tesztelése sikertelen.
* Állítson be [metrika riasztások](app-insights-monitor-web-app-availability.md) tudni, hogy ha például a válaszidők vagy kivétel díjszabás metrikák nyissa meg külső elfogadható határértékeket.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="open-source"></a>Nyílt forráskód
[Olvassa el, és hozzájárulnak a kódot](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)


## <a name="next-steps"></a>Következő lépések
* [Telemetriai adatok felvétele a weblapok](app-insights-javascript.md) a figyelő lap használati és teljesítményadatokat.
* [Függőségek figyelése](app-insights-asp-net-dependencies.md) megjelenítéséhez, ha a többi, SQL vagy más külső erőforrások lassítja meg.
* [Az API-val](app-insights-api-custom-events-metrics.md) a saját eseményeket és metrikákat az alkalmazás teljesítményét és használatának részletes nézet küldését.
* [Rendelkezésreállás figyelésére szolgáló tesztek](app-insights-monitor-web-app-availability.md) ellenőrizze a világszerte folyamatosan az alkalmazást. 

