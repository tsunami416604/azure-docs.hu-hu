---
title: Az Azure Application Insights a Visual Studióban alkalmazások hibakeresése |} A Microsoft Docs
description: Webalkalmazások teljesítményelemzése és diagnosztikája hibakeresés közben és éles környezetben.
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: carmonm
ms.assetid: 2059802b-1131-477e-a7b4-5f70fb53f974
ms.service: application-insights
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 07/07/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 27fa1edd055d8d4f009db325b0d0c7fb5b6bfa8d
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139991"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Az alkalmazások az Azure Application Insights a Visual Studióban
A Visual Studio 2015-ös és újabb verzióiban elemezheti az ASP.NET-webalkalmazások teljesítményét és diagnosztizálhatja a problémákat hibakeresés közben és éles környezetben is az [Azure Application Insights](app-insights-overview.md) telemetriájával.

Ha az ASP.NET-webalkalmazást a Visual Studio 2017-es vagy újabb verziójával hozta létre, már a részét képezi az Application Insights SDK. Korábbi verziók esetén, ha még nem tette meg, [adja hozzá az alkalmazáshoz az Application Insights-t](app-insights-asp-net.md).

Az alkalmazás éles környezetben végzett megfigyeléséhez általában az [Azure Portalon](https://portal.azure.com) tekintheti meg az Application Insights Telemetriát, ahol riasztásokat állíthat be és hatékony megfigyelő eszközöket alkalmazhat. Hibakereséshez azonban a Visual Studióban is megkeresheti és elemezheti a telemetriát. A Visual Studio segítségével a webhelyet működtetheti élesben pedig a hibakeresés, a fejlesztői gépen fut, a telemetria elemzését. Az utóbbi esetben akkor is elemezhet hibakeresési futtatásokat, ha még nem konfigurálta az SDK-t, hogy a telemetriát az Azure Portalra továbbítsa. 

## <a name="run"></a> Hibakeresés a projektben
Futtassa a webalkalmazást helyi hibakeresési módban az F5 billentyűvel. Nyisson meg több lapot, hogy létrejöjjön valamennyi telemetria.

A Visual Studióban láthatja a projektbe az Application Insights modulja által naplózott események számát.

![A Visual Studióban megjelenik az Application Insights gomb a hibakeresés alatt.](./media/app-insights-visual-studio/appinsights-09eventcount.png)

Erre a gombra kattintva kereshet a telemetriában. 

## <a name="application-insights-search"></a>Application Insights-keresés
Az Application Insights Keresés ablaka megjeleníti a naplózott eseményeket. (Ha bejelentkezett az Azure Application Insights beállításakor, használja a keresési ugyanazokra az eseményekre az Azure Portalon.)

![Kattintson a jobb gombbal a projektre, és válassza az Application Insights, Keresés lehetőséget.](./media/app-insights-visual-studio/34.png)

> [!NOTE] 
> A szűrők bejelölése vagy kijelölésük törlése után kattintson a szöveges keresőmező végénél található Keresés gombra.
>

A szabad szöveges keresés az események minden mezőjén használható. Kereshet például egy oldal URL-jének egy részére, egy tulajdonság egy értékére (pl. az ügyfél városa) vagy egy nyomkövetési napló adott szavaira.

A részletes tulajdonságok megtekintéséhez kattintson egy eseményre.

A webalkalmazáshoz tartozó kérelmekhez végigkattinthat a kódon.

![A Request Details (Adatok kérése) alatt kattintson végig a kódon](./media/app-insights-visual-studio/31.png)

A kapcsolódó elemeket is megnyithatja a sikertelen kérések vagy a kivételek diagnosztizálásához.

![A Request Details (Adatok kérése) alatt görgessen le a vonatkozó elemekhez](./media/app-insights-visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>Nézet kivételek és sikertelen kérelmek
A keresési ablakban megjelennek a kivételekről szóló jelentések. (Az ASP.NET alkalmazás néhány régebbi típusában [be kell állítania a kivételfigyelést](app-insights-asp-net-exceptions.md) a keretrendszer által kezelt kivételek megtekintéséhez.)

Híváslánc lekéréséhez kattintson egy kivételre. Ha az alkalmazás kódja meg van nyitva a Visual Studióban, a hívásláncból végigkattinthat a kód megfelelő soráig.

![Kivétel híváslánca](./media/app-insights-visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>Megtekintheti a kérések és kivételek összegzése a kódban
Az egyes fölötti Code Lens sorban, a kérések és Application Insights által az elmúlt 24 órában naplózott kivételek száma látható.

![Kivétel híváslánca](./media/app-insights-visual-studio/21.png)

> [!NOTE] 
> A Code Lens csak akkor jeleníti meg az Application Insights adatait, ha [úgy konfigurálta az alkalmazást, hogy telemetriát küldjön az Application Insights portálra](app-insights-asp-net.md).
>

[További információk az Application Insights-telemetriáról a Code Lensben](app-insights-visual-studio-codelens.md)

## <a name="trends"></a>Trendek
A Trendek használatával megjelenítheti az alkalmazás időbeni működésének a módját. 

Válassza az **Explore Telemetry Trends** (Telemetriatrendek megtekintése) elemet az Application Insights eszköztárgombjáról vagy az Application Insights Keresés ablakában. A kezdéshez válasszon egyet az öt gyakori lekérdezés közül. A különböző adatkészleteket telemetriatípusok, időintervallumok és egyéb tulajdonságok szerint elemezheti. 

Az adatokban előforduló rendellenességek felderítéséhez válassza valamelyik rendellenességi lehetőséget a „View Type” (Nézettípus) legördülő menüben. Az ablak alján található szűrőbeállítások megkönnyítik a telemetria bizonyos részhalmazainak alaposabb vizsgálatát.

![Trendek](./media/app-insights-visual-studio/51.png)

[További információ a Trendekről](app-insights-visual-studio-trends.md).

## <a name="local-monitoring"></a>Helyi figyelés
(A Visual Studio 2015 Update 2) Ha még nem konfigurálta az SDK-t, hogy telemetriát küldjön az Application Insights portálra (így nem nincs kialakítási kulcs az applicationinsights.config fájlban), a diagnosztikai ablak megjeleníti a legújabb hibakeresési munkamenetből származó telemetria. 

Ez akkor javasolt, ha már közzétette az alkalmazás korábbi verzióit. Az nem lenne szerencsés, ha a hibakeresési munkamenetek telemetriája összekeveredjen a közzétett alkalmazás Application Insights portálján lévő telemetriával.

Az is hasznos, ha van [egyéni telemetriája](app-insights-api-custom-events-metrics.md), amelyen hibakeresést végez, mielőtt elküldené a telemetriát a portálra.

* *Először teljes körűen konfiguráltam az Application Insights szolgáltatást, hogy küldjön telemetriát a portálra. Most azonban azt szeretném, hogy a telemetria csak a Visual Studióban jelenjen meg.*
  
  * A Keresés ablak Beállításai között lehetősége van a helyi diagnosztika keresésére még akkor is, ha az alkalmazás elküldi a telemetriát a portálra.
  * Ha nem akarja, hogy a rendszer elküldje a telemetriát a portálra, tegye megjegyzésbe az `<instrumentationkey>...` sort az ApplicationInsights.config fájlban. Ha azt szeretné, hogy a rendszer megint elküldje a telemetriát a portálra, állítsa vissza a kódot.


## <a name="next-steps"></a>További lépések
|  |  |
| --- | --- |
| **[További adatok hozzáadása](app-insights-asp-net-more.md)**<br/>Figyelheti a használatot, az elérhetőséget, a függőségeket és a kivételeket. Integrálhatja a nyomkövetéseket naplózási keretrendszerekből. Egyéni telemetriát írhat. |![Visual Studio](./media/app-insights-visual-studio/64.png) |
| **[Az Application Insights-portál használata](app-insights-dashboards.md)**<br/>Tekintse meg az irányítópultok, hatékony diagnosztikai és elemző eszközök, riasztások, egy élő függőségi térkép az alkalmazáshoz, és az exportált telemetriai adatokat. |![Visual Studio](./media/app-insights-visual-studio/62.png) |

