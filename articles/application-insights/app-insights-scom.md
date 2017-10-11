---
title: "SCOM-integráció az Application insights szolgáltatással |} Microsoft Docs"
description: "Ha az SCOM-felhasználó, teljesítmény figyeléséhez és eseményadatokat az Application insights szolgáltatással. Átfogó irányítópultok, intelligens riasztások, hatékony diagnosztikai eszközöket és elemzési lekérdezések."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 606e9d03-c0e6-4a77-80e8-61b75efacde0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/12/2016
ms.author: bwren
ms.openlocfilehash: 9c205465981fabdbb696cdc44f765532bbb992b5
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="application-performance-monitoring-using-application-insights-for-scom"></a>Alkalmazásteljesítmény-figyelés az Application Insights az SCOM-hoz használatával
Ha a System Center Operations Manager (SCOM) használatával kezeli a kiszolgálók, teljesítmény figyeléséhez, és segítségével. a teljesítménnyel kapcsolatos problémák diagnosztizálásához [Azure Application Insights](app-insights-asp-net.md). Az Application Insights figyeli a webes alkalmazás bejövő kérelmet, REST és SQL-hívások, kivételek és naplókivonatokat kimenő. Nyújtja a irányítópultok metrika diagramok és intelligens riasztások, valamint hatékony diagnosztikai keresési és elemzési lekérdezéseket a telemetriai keresztül. 

Az Application Insights általi figyelés az SCOM felügyeleti csomagok használatával válthat.

## <a name="before-you-start"></a>Előkészületek
Feltételezzük, hogy:

* Megismerheti az SCOM, és hogy használatával SCOM 2012 R2 vagy 2016 kezeli az IIS webkiszolgálón.
* Már telepítette a kiszolgálókon, amelyek az Application insights szolgáltatással figyelni kívánt webalkalmazás.
* Az alkalmazás-keretrendszer verziója a .NET 4.5-ös vagy újabb.
* Rendelkezik hozzáféréssel az előfizetés [Microsoft Azure](https://azure.com) és való bejelentkezés a [Azure-portálon](https://portal.azure.com). A szervezet előfordulhat, hogy rendelkezik előfizetéssel, és adhat hozzá a Microsoft-fiókjához azt.

(A fejlesztői csapat létrehozhat a [Application Insights SDK](app-insights-asp-net.md) a webes alkalmazásban. A felépítés során instrumentation egyéni telemetria írásban nagyobb rugalmasságot biztosít. Azonban nem számít: az SDK-t a beépített nélkül akár az itt leírt lépéseket követheti.)

## <a name="one-time-install-application-insights-management-pack"></a>(Egy alkalommal) Az Application Insights felügyeleti csomag telepítéséhez
A számítógépen, amelyen az Operations Manager futtatja:

1. Távolítsa el a régi verziót a felügyeleti csomag:
   1. Az Operations Manager programban nyissa meg a felügyeleti, a felügyeleti csomagokat. 
   2. Törölje a régi verzióját.
2. Töltse le és telepítse a felügyeleti csomag a katalógusban.
3. Indítsa újra az Operations Manager.

## <a name="create-a-management-pack"></a>Felügyeleti csomag létrehozása
1. Az Operations Manager programban nyissa meg a **szerzői műveletek**, **.NET... az Application insights szolgáltatással**, **figyelés felvétele varázsló**, és újra válasszon **... az Application insights szolgáltatással .NET**.
   
    ![](./media/app-insights-scom/020.png)
2. A konfiguráció után az alkalmazás nevét. (Hogy egyetlen alkalmazás állíthatnak be egyszerre.)
   
    ![](./media/app-insights-scom/030.png)
3. Ugyanazon az oldalon varázsló vagy hozzon létre egy új felügyeleti csomagot, vagy válassza ki az Application Insights korábban létrehozott csomagot.
   
     (Az Application Insights [felügyeleti csomag](https://technet.microsoft.com/library/cc974491.aspx) egy sablon, amelyből példányt létrehozni. Többször is felhasználhatja ugyanazon később.)

    ![Az általános tulajdonságok lapján írja be az alkalmazás nevét. Az új gombra, és írja be a felügyeleti csomag nevét. Kattintson az OK gombra, majd kattintson a Tovább gombra.](./media/app-insights-scom/040.png)

1. Válasszon egy alkalmazást, amely segítségével nyomon követni kívánt. A keresési funkciót keres a kiszolgálókon telepített alkalmazások között.
   
    ![Mit figyeljen lapon kattintson a Hozzáadás gombra, írja be az alkalmazás nevére részét, kattintson a Keresés gombra, válassza ki az alkalmazást, és hozzáadása, az OK gombra.](./media/app-insights-scom/050.png)
   
    A figyelés hatóköre nem kötelező mező segítségével egy részét a kiszolgálóit, ha nem kívánja az összes kiszolgálót az alkalmazás figyelése.
2. A varázsló következő lapján meg kell adnia a Microsoft Azure bejelentkezési adatait.
   
    Ezen a lapon válassza ki az Application Insights-erőforrást, ha azt szeretné, hogy a telemetriai adatok elemzése és jelenik meg. 
   
   * Ha az alkalmazás fejlesztése során az Application Insights lett konfigurálva, válassza ki a meglévő erőforrást.
   * Máskülönben hozzon létre egy új erőforrást az alkalmazáshoz. Ha más alkalmazásokat, amelyek az adott rendszer összetevők, helyezze el őket ugyanabban az erőforráscsoportban, könnyebben hozzáférés a telemetriai adatok kezeléséhez.
     
     Ezeket a beállításokat később bármikor módosíthatja.
     
     ![Az Application Insights-beállítások lapján kattintson a "bejelentkezés", és adja meg a Microsoft-fiók hitelesítő adatait az Azure-bA. Ezután válasszon ki egy előfizetést, az erőforráscsoport és az erőforrás.](./media/app-insights-scom/060.png)
3. A varázsló befejezéséhez.
   
    ![Kattintson a Létrehozás gombra](./media/app-insights-scom/070.png)

Ismételje meg az eljárást minden, amely segítségével nyomon követni kívánt alkalmazáshoz.

Ha beállításait később módosítani szeretné, nyissa meg ismét a tulajdonságok a figyelő a szerzői műveletek ablakból.

![A szerzői műveletek, válassza ki a .NET alkalmazásteljesítmény-figyelés az Application insights szolgáltatással, válassza ki a figyelő, és kattintson a Tulajdonságok elemre.](./media/app-insights-scom/080.png)

## <a name="verify-monitoring"></a>Ellenőrizze a figyelés
A figyelő, hogy telepítette minden kiszolgálón megkeresi az alkalmazást. Ha úgy találja, hogy az alkalmazást, és azt konfigurálja az Application Insights Állapotmonitort a figyelheti az alkalmazást. Szükség esetén azt először telepíti Állapotmonitort a kiszolgálón.

Talált alkalmazáspéldányok, mely ellenőrizheti:

![A figyelés, nyissa meg az Application insights szolgáltatással](./media/app-insights-scom/100.png)

## <a name="view-telemetry-in-application-insights"></a>Az Application Insightsban nézet telemetria
Az a [Azure-portálon](https://portal.azure.com), keresse meg az alkalmazás erőforrás. Ön [telemetriai ábrázoló diagramok megjelenítéséhez](app-insights-dashboards.md) az alkalmazásból. (Ha ez nem látható fő lapján még, kattintson a metrikák élő adatfolyam.)

## <a name="next-steps"></a>Következő lépések
* [Állítson be egy irányítópultot](app-insights-dashboards.md) kell összefogni a legfontosabb diagramok ezzel és más alkalmazások figyelését.
* [További tudnivalók a metrikák](app-insights-metrics-explorer.md)
* [Riasztások beállítása](app-insights-alerts.md)
* [Teljesítménnyel kapcsolatos problémák diagnosztizálása](app-insights-detect-triage-diagnose.md)
* [Hatékony elemzési lekérdezések](app-insights-analytics.md)
* [A webteszt rendelkezésre állása](app-insights-monitor-web-app-availability.md)

