---
title: Az Azure a figyelő a metrikák áttekintése
description: Ismerje meg, hogyan szabhatja testre a figyelési diagramokat az Azure-ban.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/06/2017
ms.author: robb
ms.component: metrics
ms.openlocfilehash: 878ba004e7572ad78f574c15fd76c8868b281117
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262256"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>A Microsoft Azure-ban mérőszámok áttekintése
Az összes Azure-szolgáltatások nyomon követéséhez alapvető metrikákat, amelyek lehetővé teszik a állapotának, teljesítmény, rendelkezésre állási és a szolgáltatások használatának figyelése. Megtekintheti a következő metrikák tekintetében az Azure portálon, és használhatja a [REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx) vagy [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) metrikák teljes készletét programozott módon eléréséhez.

Bizonyos szolgáltatások esetén szükség lehet ahhoz, hogy bármely metrikák lásd: a diagnosztika bekapcsolásához. Mások, például a virtuális gépek elérhetővé válik egy alapvető házirendcsoport metrikákat, de kell ahhoz, hogy a teljes beállítása nagyon gyakori metrikákat. Lásd: [figyelés engedélyezésekor és diagnosztikai](insights-how-to-use-diagnostics.md) további.

## <a name="using-monitoring-charts"></a>Figyelési diagramok használatával
A metrikák bármelyikét meg is diagram bármely idő alatt, válassza ki őket.

1. Az a [Azure Portal](https://portal.azure.com/), kattintson a **Tallózás**, és majd erőforrás érdekli a figyelés.
2. A **figyelés** a szakasz a legfontosabb metrikákat, az egyes Azure-erőforrás. Például a webes alkalmazás rendelkezik **kérések és hibák követése**, ahol a virtuális gépek érvényesek **processzor** és **lemez olvasási és írási**: ![figyelés fókuszban](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. Kattintson a bármelyik olyan diagram bemutatja, a **metrika** panelen. A panelen, a graph mellett egy táblázat bemutatja, hogy a metrikák (például átlag, minimális és maximális, a kiválasztott időtartomány keresztül) összesítéseinek van. Amely az alábbiakban az erőforrás riasztási szabályok.
    ![Metrika panel](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. A sorok megjelenő testre szabásához kattintson a **szerkesztése** gombra a diagram vagy, a **diagram szerkesztése** parancsot a metrika panelen.
5. A lekérdezés szerkesztése panelen három műveleteket végezheti el:
   
   * Az időtartomány módosítása
   * A Megjelenés közötti váltás sáv és vonal
   * Válasszon másik metics ![lekérdezés szerkesztése](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. Egy másik tartomány jelölnie úgy a időtartományát (például **elmúlt egy órában**) elemre kattintva **mentése** a panel alján. Másik lehetőségként **egyéni**, amely lehetővé teszi, hogy válassza ki a minden idõszak, amíg az elmúlt 2 hétben. Például láthatja a teljes két hét, vagy, tegnapi csak 1 óra. Adja meg a másik óránként megadására szolgáló szövegmező.
    ![Egyéni időtartomány](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. Alább az időtartományt, munkakör válassza a diagramon megjelenítendő metrikák tetszőleges számú.
8. Kattintson a Mentés gombra a módosítások fogja menteni az adott erőforráshoz. Például ha két virtuális gép, és egy diagram módosításához, nincs hatással a többi.

## <a name="creating-side-by-side-charts"></a>Egymás melletti diagramok létrehozása
A hatékony testreszabási a portálon is hozzáadhat annyi diagramokat, ahányat csak szeretne.

1. Az a **...**  menü felső részén kattintson a panel **hozzáadhatja a csempéket**:  
    ![Hozzáadásra szolgáló menü](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Majd, akkor választhatja válassza a diagram a **gyűjtemény** a képernyő jobb oldalán: ![gyűjteménye](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Ha nem látja a metrika azt szeretné, bármikor hozzáadhat az egyik előre definiált metrikákat, és **szerkesztése** a diagram megjelenítése a metrika kell.

## <a name="monitoring-usage-quotas"></a>Figyelési használati kvóták
A legtöbb metrikák is láthat trendek adott idő alatt, de egyes adatok, például használati kvótákat, az időpontban információk a küszöbértéket.

A kvóták erőforrások panelen használati kvóták is látható:

![Használat](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Például a metrikákat, használhatja a [REST API](https://msdn.microsoft.com/library/azure/dn931963.aspx) vagy [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) használati kvóták teljes készletét programozott módon eléréséhez.

## <a name="next-steps"></a>További lépések
* [Riasztási értesítéseket](insights-receive-alert-notifications.md) amikor metrika keverve használ a küszöbértéket.
* [Figyelés engedélyezésekor és diagnosztikai](insights-how-to-use-diagnostics.md) nagyon gyakori gyűjtéséhez részletes a a szolgáltatásban.
* [Automatikus méretezése a példányok száma](insights-how-to-scale.md) ellenőrizze, hogy a szolgáltatás elérhető, és a gyors.
* [Az alkalmazások teljesítményének figyelésére](../application-insights/app-insights-azure-web-apps.md) Ha azt szeretné tudni, pontosan hogyan a kód működik-e a felhőben.
* Használjon [Application Insights JavaScript alkalmazásokkal és weblapok](../application-insights/app-insights-web-track-usage.md) ügyfél analytics egy webhelyet a böngészők kapcsolatos eléréséhez.
* [Figyelése rendelkezésre állásának és bármilyen weblap válaszképességének](../application-insights/app-insights-monitor-web-app-availability.md) az Application insights szolgáltatással, így azt is megtudhatja, ha az oldala nem működik.

