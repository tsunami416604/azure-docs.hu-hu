---
title: Hibakeresés a Visual Studióban az Azure Application Insights
description: Webalkalmazások teljesítményelemzése és diagnosztikája hibakeresés közben és éles környezetben.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/07/2017
ms.reviewer: mbullwin
ms.custom: vs-azure
ms.openlocfilehash: 8905222214d58eeba24ecf50da768ffa1d65c39d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670882"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Alkalmazások hibakeresése az Azure Application Insights a Visual Studióban
A Visual Studio 2015-ös és újabb verzióiban elemezheti az ASP.NET-webalkalmazások teljesítményét és diagnosztizálhatja a problémákat hibakeresés közben és éles környezetben is az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) telemetriájával.

Ha az ASP.NET-webalkalmazást a Visual Studio 2017-es vagy újabb verziójával hozta létre, már a részét képezi az Application Insights SDK. Korábbi verziók esetén, ha még nem tette meg, [adja hozzá az alkalmazáshoz az Application Insights-t](../../azure-monitor/app/asp-net.md).

Az alkalmazás éles környezetben végzett megfigyeléséhez általában az [Azure Portalon](https://portal.azure.com) tekintheti meg az Application Insights Telemetriát, ahol riasztásokat állíthat be és hatékony megfigyelő eszközöket alkalmazhat. Hibakereséshez azonban a Visual Studióban is megkeresheti és elemezheti a telemetriát. A Visual Studióval elemezheti a telemetria mind az üzemi helyről, mind a fejlesztői gépen futó hibakeresési folyamatokból. Az utóbbi esetben akkor is elemezhet hibakeresési futtatásokat, ha még nem konfigurálta az SDK-t, hogy a telemetriát az Azure Portalra továbbítsa. 

## <a name="debug-your-project"></a><a name="run"></a> Hibakeresés a projektben
Futtassa a webalkalmazást helyi hibakeresési módban az F5 billentyűvel. Nyisson meg több lapot, hogy létrejöjjön valamennyi telemetria.

A Visual Studióban láthatja a projekt Application Insights modulja által naplózott események számát.

![A Visual Studióban megjelenik az Application Insights gomb a hibakeresés alatt.](./media/visual-studio/appinsights-09eventcount.png)

Erre a gombra kattintva kereshet a telemetriában. 

## <a name="application-insights-search"></a>Application Insights-keresés
Az Application Insights Keresés ablaka megjeleníti a naplózott eseményeket. (Ha Application Insights beállításakor bejelentkezett az Azure-ba, akkor a Azure Portal is kereshet ugyanezen események között.)

![Kattintson a jobb gombbal a projektre, és válassza az Application Insights, Keresés lehetőséget.](./media/visual-studio/34.png)

> [!NOTE] 
> A szűrők bejelölése vagy kijelölésük törlése után kattintson a szöveges keresőmező végénél található Keresés gombra.
>

A szabad szöveges keresés az események minden mezőjén használható. Kereshet például egy oldal URL-jének egy részére, egy tulajdonság egy értékére (pl. az ügyfél városa) vagy egy nyomkövetési napló adott szavaira.

A részletes tulajdonságok megtekintéséhez kattintson egy eseményre.

A webalkalmazáshoz tartozó kérelmekhez végigkattinthat a kódon.

![A Request Details (Adatok kérése) alatt kattintson végig a kódon](./media/visual-studio/31.png)

A kapcsolódó elemeket is megnyithatja a sikertelen kérések vagy a kivételek diagnosztizálásához.

![A Request Details (Adatok kérése) alatt görgessen le a vonatkozó elemekhez](./media/visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>Kivételek és sikertelen kérelmek megtekintése
A keresési ablakban megjelennek a kivételekről szóló jelentések. (Az ASP.NET alkalmazás néhány régebbi típusában [be kell állítania a kivételfigyelést](../../azure-monitor/app/asp-net-exceptions.md) a keretrendszer által kezelt kivételek megtekintéséhez.)

Híváslánc lekéréséhez kattintson egy kivételre. Ha az alkalmazás kódja meg van nyitva a Visual Studióban, a hívásláncból végigkattinthat a kód megfelelő soráig.

![Kivétel híváslánca](./media/visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>A kérelem és a kivételek összegzésének megtekintése a kódban
Az egyes kezelői módszerek fölötti kód Lencsés sorban megtekintheti az elmúlt 24 óra Application Insights által naplózott kérések és kivételek számát.

![Kivétel híváslánca](./media/visual-studio/21.png)

> [!NOTE] 
> A Code Lens csak akkor jeleníti meg az Application Insights adatait, ha [úgy konfigurálta az alkalmazást, hogy telemetriát küldjön az Application Insights portálra](../../azure-monitor/app/asp-net.md).
>

[További információk az Application Insights-telemetriáról a Code Lensben](../../azure-monitor/app/visual-studio-codelens.md)

## <a name="trends"></a>Trendek
A Trendek használatával megjelenítheti az alkalmazás időbeni működésének a módját. 

Válassza az **Explore Telemetry Trends** (Telemetriatrendek megtekintése) elemet az Application Insights eszköztárgombjáról vagy az Application Insights Keresés ablakában. A kezdéshez válasszon egyet az öt gyakori lekérdezés közül. A különböző adatkészleteket telemetriatípusok, időintervallumok és egyéb tulajdonságok szerint elemezheti. 

Az adatokban előforduló rendellenességek felderítéséhez válassza valamelyik rendellenességi lehetőséget a „View Type” (Nézettípus) legördülő menüben. Az ablak alján található szűrőbeállítások megkönnyítik a telemetria bizonyos részhalmazainak alaposabb vizsgálatát.

![Trendek](./media/visual-studio/51.png)

[További információ a Trendekről](../../azure-monitor/app/visual-studio-trends.md).

## <a name="local-monitoring"></a>Helyi figyelés
(A Visual Studio 2015 Update 2) Ha nem konfigurálta az SDK-t, hogy telemetria küldjön a Application Insights portálra (így nincs rendszerállapot-kulcs a ApplicationInsights. config fájlban), akkor a diagnosztika ablak a legutóbbi hibakeresési munkamenetből származó telemetria jeleníti meg. 

Ez akkor javasolt, ha már közzétette az alkalmazás korábbi verzióit. Az nem lenne szerencsés, ha a hibakeresési munkamenetek telemetriája összekeveredjen a közzétett alkalmazás Application Insights portálján lévő telemetriával.

Az is hasznos, ha van [egyéni telemetriája](../../azure-monitor/app/api-custom-events-metrics.md), amelyen hibakeresést végez, mielőtt elküldené a telemetriát a portálra.

* *Először teljesen konfiguráltam Application Insights, hogy telemetria küldjenek a portálra. Most azonban azt szeretném, hogy a telemetria csak a Visual Studióban legyen látható.*
  
  * A Keresés ablak Beállításai között lehetősége van a helyi diagnosztika keresésére még akkor is, ha az alkalmazás elküldi a telemetriát a portálra.
  * A telemetria a portálra való elküldésének leállításához tegye `<instrumentationkey>...` megjegyzésbe az ApplicationInsights. config fájlból származó sort. Ha készen áll arra, hogy ismét elküldhesse a telemetria a portálra, tegye meg a megjegyzéseit.


## <a name="next-steps"></a>További lépések
|  |  |
| --- | --- |
| **[További adatok hozzáadása](../../azure-monitor/app/asp-net-more.md)**<br/>Figyelheti a használatot, az elérhetőséget, a függőségeket és a kivételeket. Integrálhatja a nyomkövetéseket naplózási keretrendszerekből. Egyéni telemetriát írhat. |![Visual Studio](./media/visual-studio/64.png) |
| **[Az Application Insights portál használata](../../azure-monitor/app/overview-dashboard.md)**<br/>Megtekintheti az irányítópultokat, a hatékony diagnosztikai és elemzési eszközöket, a riasztásokat, az alkalmazás élő függőségi térképét és az exportált telemetria-adatait. |![Visual Studio](./media/visual-studio/62.png) |

