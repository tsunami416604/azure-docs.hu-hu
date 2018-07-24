---
title: Mérőszámok az Azure Monitor áttekintése
description: Ismerje meg, hogyan szabhatja testre a figyelési diagramjait az Azure-ban.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/06/2017
ms.author: robb
ms.component: metrics
ms.openlocfilehash: 44daf6461a062e75435ec6f70fbc3cf10327e799
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213044"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>A Microsoft Azure-ban mérőszámok áttekintése
Azure-szolgáltatásokhoz nyomon követheti a fontos metrika, amely segítségével nyomon követheti a állapotáról, teljesítményéről, rendelkezésre állás és a szolgáltatások használatának engedélyezése. Az Azure Portalon is megtekintheti ezeket a metrikákat, és használhatja a [REST API-val](https://msdn.microsoft.com/library/azure/dn931930.aspx) vagy [.NET SDK-val](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) programozott módon a teljes körű metrikák eléréséhez.

Egyes szolgáltatások esetén szükség lehet ahhoz, hogy minden olyan metrikák megtekintéséhez engedélyezze a diagnosztikát. Mások, például a virtuális gépek kap egy alapvető házirendcsoport metrikák, de van szükség ahhoz, hogy a teljes beállítása nagy gyakoriságú metrikákat. Lásd: [engedélyezze a megfigyelést és a diagnosztika](insights-how-to-use-diagnostics.md) további.

## <a name="using-monitoring-charts"></a>Figyelési diagramok használata
Akkor is diagram bármely metrikák azokat bármilyen választott időszakra.

1. Az a [az Azure Portal](https://portal.azure.com/), kattintson a **Tallózás**, majd erőforrás érdekli figyelése és.
2. A **figyelés** szakasz tartalmazza a legfontosabb metrikákat, minden egyes Azure-beli erőforráshoz. Például, egy webalkalmazás, **kérelmek és hibák**, ahol, egy virtuális gépet kellene **processzorhasználat** és **lemez olvasási és írási**: ![figyelés fókusz](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. Bármely diagramra kattintva megjelenik a **metrika** panelen. A panelen, a diagram mellett van egy táblát, amely azt mutatja be, összesítést a mérőszámok (például az átlagos, minimális és maximális, a kiválasztott időtartomány keresztül). Amelyek az alábbiakban a riasztási szabályok az erőforráshoz.
    ![Metrika panel](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. Testre szabhatja a sorok jelennek meg, kattintson a **szerkesztése** gomb a diagramra vagy, a **diagram szerkesztése** parancs a metrika panel.
5. A lekérdezés szerkesztése panelen három műveleteket végezheti el:
   
   * Állítsa az időtartományt
   * A Megjelenés közötti váltás az eszköztáron és a vonal
   * Válassza ki a különböző metics ![lekérdezés szerkesztése](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. Az időtartomány módosítása olyan egyszerű, mint kiválasztania egy másik tartományt (például **utóbbi egy órában**), és kattintson a **mentése** a panel alján. Azt is beállíthatja **egyéni**, amely lehetővé teszi, hogy válassza ki minden időszak, amíg az elmúlt 2 hét alatt megy végbe. Például megtekintheti a teljes két hét, vagy, mappaverziót csak 1 óra. Írja be a szövegmezőben adjon meg egy másik óránként.
    ![Egyéni időtartomány](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. Alább az időtartományt, üzletlánc válassza ki a diagramon megjelenítendő mérőszámokat tetszőleges számú.
8. Amikor, kattintson a Mentés gombra a módosításokat menti a rendszer az adott erőforráshoz. Például ha két virtuális gépet, és módosíthatja a diagram egy, az nem befolyásolja a másik.

## <a name="creating-side-by-side-charts"></a>Egymás melletti diagramok létrehozása
A hatékony testreszabása a portálon is hozzáadhat a kívánt számú diagramok.

1. Az a **...**  kattintson a panel tetején lévő menüben **csempék hozzáadása**:  
    ![Hozzáadásra szolgáló menü](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Ezután, kattintson a diagram a **katalógus** , a képernyő jobb oldalán: ![gyűjteménye](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Ha nem látja a metrika azt szeretné, mindig felvehet egy előre megadott metrikák, és **szerkesztése** a diagramot úgy, hogy nincs szüksége a metrika megjelenítése.

## <a name="monitoring-usage-quotas"></a>Használati kvóták figyelése
Legtöbb metrika trendeket jelenít meg, idővel, de egyes adatok, például a használati kvótákat, egy küszöbértékkel időponthoz információkat.

Használati kvóták a kvóták rendelkező erőforrások panelen is látható:

![Használat](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Például metrikák, a segítségével a [REST API](https://msdn.microsoft.com/library/azure/dn931963.aspx) vagy [.NET SDK-val](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) programozott módon a teljes körű használati kvóták eléréséhez.

## <a name="next-steps"></a>További lépések
* [Riasztási értesítések fogadása](insights-receive-alert-notifications.md) , ha egy metrika átlépi a küszöbértéket.
* [Engedélyezze a megfigyelést és a diagnosztika](insights-how-to-use-diagnostics.md) kíván gyűjteni részletes nagy gyakoriságú a szolgáltatást.
* [Automatikusan skálázhatja a példányszámot](insights-how-to-scale.md) , győződjön meg arról, hogy szolgáltatása elérhető és válaszkész.
* [Alkalmazások teljesítményének figyelése](../application-insights/app-insights-azure-web-apps.md) Ha szeretné megtudni, hogy pontosan milyen kódját működik-e a felhőben.
* Használat [Application Insights JavaScript-alkalmazásokhoz és weblapokhoz](../application-insights/app-insights-web-track-usage.md) ügyfél analytics tudnivalók a böngészők, amelyek ellátogatnak egy weblapra beolvasásához.
* [Rendelkezésre állásának és válaszkészségének megfigyelése bármilyen weblapon](../application-insights/app-insights-monitor-web-app-availability.md) az Application insights szolgáltatással, így megtudhatja, ha az oldal nem működik.

