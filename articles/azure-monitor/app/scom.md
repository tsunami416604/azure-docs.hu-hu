---
title: Az Application Insights az SCOM-integrációval |} A Microsoft Docs
description: Ha Ön egy SCOM-felhasználó, teljesítményének figyelése, és diagnosztizálhatja a problémákat az Application insights segítségével. Az irányítópultok átfogó, intelligens riasztások, hatékony diagnosztikai eszközöket és elemzési lekérdezések.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 606e9d03-c0e6-4a77-80e8-61b75efacde0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mbullwin
ms.openlocfilehash: a08a67a78362325e29b1002b44f390a94e78888a
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028638"
---
# <a name="application-performance-monitoring-using-application-insights-for-scom"></a>Alkalmazásteljesítmény-figyelés az Application Insights az SCOM-hoz használatával
Ha a System Center Operations Manager (SCOM) használatával felügyelheti kiszolgálóit, teljesítmény figyelheti és diagnosztizálhatja a teljesítménnyel kapcsolatos problémák segítségével. [Azure Application Insights](../../azure-monitor/app/asp-net.md). Az Application Insights figyeli a bejövő kéréseket a webes alkalmazás, REST és SQL-hívások, kivételek és nyomkövetési naplók kimenő. Ez biztosítja, irányítópultokat mérőszám-diagramok és intelligens riasztások, valamint hatékony diagnosztikai keresés és elemző lekérdezések képest ezt a telemetriát. 

Application insights általi figyelés az SCOM felügyeleti csomagok használatával válthat.

> [!IMPORTANT]
> A System Center Operations Manager felügyeleti csomag már **elavult**. Ez nem támogatja a legújabb Application Insights SDK-k és a továbbiakban nem javasolt.

## <a name="before-you-start"></a>Előkészületek
Feltételezzük, hogy:

* Ismerkedjen meg az SCOM és az SCOM 2012 R2 vagy 2016 használata kezelheti az IIS webkiszolgálóhoz.
* Már telepítette a kiszolgálók egy webalkalmazást, amely az Application Insights figyelni szeretné.
* Alkalmazás-keretrendszer verziója a .NET 4.5-ös vagy újabb verzió.
* Hozzáfér egy előfizetés [Microsoft Azure](https://azure.com) és bejelentkezhet a [az Azure portal](https://portal.azure.com). A szervezet egy előfizetéssel rendelkezhet, és adhatja hozzá a Microsoft-fiókjával.

(A fejlesztői csapat felépíthet a [Application Insights SDK](../../azure-monitor/app/asp-net.md) a webalkalmazásba. A build-idő kialakítása egyéni telemetriát írásban nagyobb rugalmasságot biztosít. Azonban nem számít: az itt ismertetett, akár anélkül az SDK-t a beépített lépéseket követnie.)

## <a name="one-time-install-application-insights-management-pack"></a>(Egy idő) Az Application Insights a felügyeleti csomag telepítése
A gépen, amelyen az Operations Manager futtatni:

1. Távolítsa el a felügyeleti csomag minden régebbi verzióját:
   1. Az Operations Manager rendszerben nyissa meg a felügyeleti csomagok a felügyelet. 
   2. Törölje a régi verzióját.
2. Töltse le és telepítse a felügyeleti csomag a katalógusból.
3. Indítsa újra az Operations Manager.

## <a name="create-a-management-pack"></a>Felügyeleti csomag létrehozása
1. Nyissa meg az Operations Manager programban **szerzői műveletek**, **... az Application Insights .NET**, **figyelés felvétele varázsló**, és újra válassza **.NET alkalmazással... Insights**.
   
    ![](./media/scom/020.png)
2. A konfiguráció után az alkalmazás neve. (Kell alakítsa ki egy alkalmazást egyszerre.)
   
    ![](./media/scom/030.png)
3. Ugyanazon az oldalon varázsló vagy hozzon létre egy új felügyeleti csomagot, vagy válasszon ki egy korábban létrehozott Application insights csomagot.
   
     (Az Application Insights [felügyeleti csomag](https://technet.microsoft.com/library/cc974491.aspx) egy sablon, amelyen-példány létrehozása. Újból felhasználhatja a példányt később.)

    ![Általános Tulajdonságok lapján írja be az alkalmazás nevét. Az új gombra, és írja be a felügyeleti csomag nevét. Kattintson az OK gombra, majd kattintson a Tovább gombra.](./media/scom/040.png)

1. Válasszon egy figyelni kívánt alkalmazást. A keresési funkció megkeresi a kiszolgálókon telepített alkalmazások között.
   
    ![Mit figyeljen lapon kattintson a Hozzáadás gombra, írja be az alkalmazás nevének egy részét, kattintson a Keresés elemre, válassza ki az alkalmazást, és Add, az OK gombra.](./media/scom/050.png)
   
    A figyelés hatóköre nem kötelező mező, adja meg a kiszolgálók egy része használható, ha nem szeretné az alkalmazást az összes kiszolgáló figyeléséhez.
2. A varázsló következő lapjára meg kell adnia hitelesítő adataival, és jelentkezzen be a Microsoft Azure-bA.
   
    Ezen az oldalon válassza ki az Application Insights-erőforrást, ahol azt szeretné, hogy a telemetriai adatokat elemzi és jelenik meg. 
   
   * Ha az alkalmazás fejlesztése során az Application Insights lett konfigurálva, válassza ki a meglévő erőforrást.
   * Máskülönben hozzon létre egy új erőforrást, az alkalmazás neve. Ha más alkalmazásokat, amelyek egy adott rendszer összetevői, helyezze el őket ugyanabban az erőforráscsoportban, könnyebben hozzáférés telemetriai adatokat kezelheti.
     
     Ezek a beállítások később módosíthatók.
     
     ![Az Application Insights beállítások lapon kattintson a "bejelentkezés" gombra, és adja meg a Microsoft-fiók hitelesítő adatait az Azure-hoz. Ezután válassza ki egy előfizetés, erőforráscsoport és erőforrások.](./media/scom/060.png)
3. A varázsló befejezése.
   
    ![Kattintson a Létrehozás gombra](./media/scom/070.png)

Ismételje meg ezt az eljárást minden egyes figyelni kívánt alkalmazást.

Ha beállításait később módosítani szeretné, nyissa meg újra a figyelő a szerzői műveletek ablakból tulajdonságait.

![A szerzői műveletek, válassza ki a .NET alkalmazásteljesítmény-figyelés az Application Insights, válassza ki a figyelőt és a Tulajdonságok parancsra.](./media/scom/080.png)

## <a name="verify-monitoring"></a>Ellenőrizze a figyelés
A figyelő, hogy minden kiszolgálón megkeresi az alkalmazás telepítve. Ha úgy találja, hogy az alkalmazás Application Insights Állapotfigyelőt az alkalmazás figyelésére konfigurálja. Ha szükséges, először telepíti az állapotfigyelő a kiszolgálón.

Talált az alkalmazás melyik példánya ellenőrizheti:

![A figyelés, nyissa meg az Application Insights](./media/scom/100.png)

## <a name="view-telemetry-in-application-insights"></a>Az Application Insights telemetria megtekintése
Az a [az Azure portal](https://portal.azure.com), keresse meg az erőforrást az alkalmazáshoz. Ön [telemetriai ábrázoló diagramok megjelenítéséhez](../../azure-monitor/app/app-insights-dashboards.md) az alkalmazásból. (Ha ez még nem látható a főoldalon még, kattintson az élő metrikák Stream.)

## <a name="next-steps"></a>További lépések
* [Állítsa be egy irányítópultot](../../azure-monitor/app/app-insights-dashboards.md) összegyűjthetők a legfontosabb diagramok ezzel és más alkalmazások figyelését.
* [Ismerkedés a metrikákkal](../../azure-monitor/app/metrics-explorer.md)
* [Riasztások beállítása](../../azure-monitor/app/alerts.md)
* [Teljesítményproblémák diagnosztizálása](../../azure-monitor/app/detect-triage-diagnose.md)
* [Nagy teljesítményű elemzési lekérdezések](../../azure-monitor/app/analytics.md)
* [Rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md)

