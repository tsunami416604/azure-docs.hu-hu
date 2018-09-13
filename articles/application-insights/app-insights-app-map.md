---
title: Az Azure Application Insights Alkalmazástérkép |} A Microsoft Docs
description: Az alkalmazástérkép topológiák összetett alkalmazások figyelése
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 32d735b24eef90f75e3ab4b9c1af58c4b6b02b15
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644680"
---
# <a name="application-map-triage-distributed-applications"></a>Alkalmazás-hozzárendelés: Osztályozhatja az elosztott alkalmazások

Alkalmazás-hozzárendelés segít a helyszíni teljesítmény szűk vagy hiba elérési pontokhoz történő termékcsalád összes tagjára vonatkozó az elosztott alkalmazás. Alkalmazás-összetevő vagy annak függőségeit; felel meg a térképen minden csomópont KPI állapot rendelkezik, és figyelmezteti állapota. Végigkattinthat valamelyik összetevő a részletesebb diagnosztikát, például az Application Insights-eseményeket. Ha az alkalmazás Azure-szolgáltatásokat használ, is kattinthat keresztül az Azure diagnostics, például az SQL Database Advisor javaslatait.

## <a name="what-is-a-component"></a>Mi az összetevő?

Összetevők egymástól függetlenül üzembe helyezhető az elosztott és mikroszolgáltatás-alkalmazások tartoznak. A fejlesztők és műveleti csapatok kód szintű láthatóság vagy a ezeket alkalmazás-összetevők által létrehozott telemetriát hozzáféréssel rendelkezik. 

* Összetevők eltérnek "a megfigyelt" külső függőségei, például az SQL-EventHub stb., amelyek nem lehet a csapat vagy szervezet (kód vagy telemetria) eléréséhez.
* Összetevők futtassa bármely server-szerepkör-tárolót példányok száma.
* Összetevők külön Application Insights-kialakítási kulcs (akkor is, ha az előfizetések különböző) vagy egy egyetlen az Application Insights-kialakítási kulcsot a jelentéskészítés különböző szerepköröket is. Előnézeti térkép tapasztalatok mutatja az összetevők függetlenül, hogyan állíthatók be.

## <a name="composite-application-map"></a>Összetett alkalmazástérkép

A teljes Alkalmazástopológia kapcsolódó alkalmazás-összetevők szinteken átívelő megjelenik. Összetevők különböző Application Insights-erőforrások, és a egy adott erőforrásban a különböző szerepkörrel lehet. Az alkalmazástérkép összetevők a következő függőségi végzett HTTP-hívások az Application Insights SDK telepített kiszolgálók között talál. 

Ez a tapasztalat összetevők fokozatos felderítés kezdődik. Ha először tölt be az alkalmazás-hozzárendelés, a lekérdezések ehhez az összetevőhöz kapcsolódó összetevők felderítése aktiválódnak. A bal felső sarkában található gomb frissíteni fogja az alkalmazását az összetevők számát tudásukat azok felderítése. 

A "Frissítés térképösszetevők" gombra kattintva a térkép a felderített mutasson, amíg az összes összetevő frissülnek.

Ha az összetevők összes szerepkörét egy Application Insights-erőforrást, majd a felderítési lépése, nem szükséges. Az alkalmazáshoz a kezdeti betöltés összes összetevőjének fog rendelkezni.

![Képernyőkép az alkalmazásról térkép](media/app-insights-app-map/001.png)

Több száz szilárd összetevők komplex topológiáit jeleníthetik az egyik fő célja a felhasználói élményét.

Kattintson a kapcsolódó elemzések és a teljesítmény és hibák osztályozási élmény az adott összetevő nyissa meg valamelyik összetevő.

![Úszó menü](media/app-insights-app-map/application-map-001.png)

### <a name="investigate-failures"></a>Hibák vizsgálata

Válassza ki **hibavizsgálat** elindíthatja a hibák panelen.

![Képernyőkép a hibák gomb vizsgálata](media/app-insights-app-map/investigate-failures.png)

![Képernyőkép a hibák élmény](media/app-insights-app-map/failures.png)

### <a name="investigate-performance"></a>Teljesítmény vizsgálata

Teljesítmény-problémákat válassza hibaelhárítása **teljesítményének vizsgálata**

![Képernyőkép a teljesítmény gomb vizsgálata](media/app-insights-app-map/investigate-performance.png)

![Képernyőkép a teljesítmény biztosítása érdekében](media/app-insights-app-map/performance.png)

### <a name="go-to-details"></a>Részletek megnyitása

Válassza ki **lépjen a részletek** a végpontok közötti tranzakció élményt kínálnak a hívási verem szintre kész nézeteket, amelyek megismeréséhez.

![Képernyőkép a go-Részletek gombra](media/app-insights-app-map/go-to-details.png)

![Képernyőkép a végpontok közötti tranzakció részletei](media/app-insights-app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Megtekintés az Analyticsben

Lekérdezése, és vizsgálja meg az alkalmazások adatainak további kattintson **megtekintés az analyticsben**.

![Elemzés gomb nézet képernyőképe](media/app-insights-app-map/view-in-analytics.png)

![Képernyőkép a analytics élmény](media/app-insights-app-map/analytics.png)

### <a name="alerts"></a>Riasztások

Az aktív riasztások és az alapul szolgáló szabályok tiggered kell a riasztásokat kiváltó megtekintéséhez jelölje ki **riasztások**.

![Riasztások gomb képernyőképe](media/app-insights-app-map/alerts.png)

![Képernyőkép a analytics élmény](media/app-insights-app-map/alerts-view.png)

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Visszajelzés
Adja meg a portál visszajelzési lehetőség keretében visszajelzést.

![MapLink 1. kép](./media/app-insights-app-map/13.png)

## <a name="next-steps"></a>További lépések

* [Azure Portal](https://portal.azure.com)
