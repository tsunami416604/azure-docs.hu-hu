<properties 
    pageTitle="Az Application Insights használata a Visual Studióban" 
    description="Teljesítményelemzés és diagnosztika hibakeresés közben és éles környezetben." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="06/21/2016" 
    ms.author="awills"/>


# Az Application Insights használata a Visual Studióban

A Visual Studio 2015-ös és újabb verzióiban elemezheti a teljesítményt és diagnosztizálhat problémákat hibakeresés közben és éles környezetben is, a [Visual Studio Application Insights](app-insights-overview.md) telemetriájával.

Ha még nem [telepítette az Application Insights szolgáltatást az alkalmazásában](app-insights-asp-net.md), tegye meg azt most.

## <a name="run"></a> Hibakeresés a projektben

Futtassa az F5 billentyűvel az alkalmazást, és próbálja ki: nyisson meg több oldalt, hogy létrejöjjön valamennyi telemetria.

A Visual Studióban láthatja a naplózott események számát.

![A Visual Studióban megjelenik az Application Insights gomb a hibakeresés alatt.](./media/app-insights-visual-studio/appinsights-09eventcount.png)

A diagnosztikai keresés megnyitásához kattintson erre a gombra. 



## Diagnosztikai keresés

A Keresés ablak megjeleníti a naplózott eseményeket. (Ha regisztrált az Azure-ba az Application Insights beállításakor, lehetősége lesz rákeresni ugyanazokra az eseményekre a portálon.)

![Kattintson a jobb gombbal a projektre, és válassza az Application Insights, Keresés lehetőséget.](./media/app-insights-visual-studio/34.png)

A szabad szöveges keresés az események minden mezőjén használható. Kereshet például egy oldal URL-jének egy részére, egy tulajdonság egy értékére (pl. az ügyfél városa) vagy egy nyomkövetési napló adott szavaira.

A részletes tulajdonságok megtekintéséhez kattintson egy eseményre.

Megnyithatja a Kapcsolódó elemek lapot a sikertelen kérések vagy kivételek diagnosztizálásához.


![](./media/app-insights-visual-studio/41.png)



## Diagnosztikai központ

A Diagnosztikai központ (Visual Studio 2015 vagy újabb verzió esetében) megjeleníti az Application Insights kiszolgálói telemetriáját, amikor az létrejön. Ez még akkor is működik, ha csak az SDK telepítését választotta, és nem csatlakoztatta az Azure portál egyik erőforrásához sem.

![Nyissa meg a Diagnosztikai eszközök ablakot, és vizsgálja meg az Application Insights-eseményeket.](./media/app-insights-visual-studio/31.png)


## Kivételek

Ha [beállította a kivételek figyelését](app-insights-asp-net-exceptions.md), a Keresés ablakban megjelennek a kivételekről szóló jelentések. 

Híváslánc lekéréséhez kattintson egy kivételre. Ha az alkalmazás kódja meg van nyitva a Visual Studióban, a hívásláncból végigkattinthat a kód megfelelő soráig.


![Kivétel híváslánca](./media/app-insights-visual-studio/17.png)

Ezenfelül az egyes módszerek fölötti Code Lens sorban látható az Application Insights által az elmúlt 24 órában naplózott kivételek száma.

![Kivétel híváslánca](./media/app-insights-visual-studio/21.png)


## Helyi figyelés



(Visual Studio 2015 Update 2 esetében) Ha még nem konfigurálta az SDK-t a telemetria Application Insights portálra küldéséhez (azaz nincs telepítve kialakítási kulcs az ApplicationInsights.config fájlban), akkor most a diagnosztikai ablak megjeleníti a telemetriát a legújabb hibakeresési munkamenetből. 

Ez akkor javasolt, ha már közzétette az alkalmazás korábbi verzióit. Az nem lenne szerencsés, ha a hibakeresési munkamenetek telemetriája összekeveredjen a közzétett alkalmazás Application Insights portálján lévő telemetriával.

Az is hasznos, ha van [egyéni telemetriája](app-insights-api-custom-events-metrics.md), amelyen hibakeresést végez, mielőtt elküldené a telemetriát a portálra.


* *Először teljes körűen konfiguráltam az Application Insights szolgáltatást, hogy küldjön telemetriát a portálra. Most azonban azt szeretném, hogy a telemetria csak a Visual Studióban jelenjen meg.*

 * A Keresés ablak Beállításai között lehetősége van a helyi diagnosztika keresésére még akkor is, ha az alkalmazás elküldi a telemetriát a portálra.
 * Ha nem akarja, hogy a rendszer elküldje a telemetriát a portálra, tegye megjegyzésbe az `<instrumentationkey>...` sort az ApplicationInsights.config fájlban. Ha azt szeretné, hogy a rendszer megint elküldje a telemetriát a portálra, állítsa vissza a kódot.

## Trendek

A Trendek használatával megjelenítheti az alkalmazás időbeni működésének a módját. 

Válassza az **Explore Telemetry Trends** (Telemetriatrendek megtekintése) elemet az Application Insights eszköztárgombjáról vagy az Application Insights Keresés ablakában. A kezdéshez válasszon egyet az öt gyakori lekérdezés közül. A különböző adatkészleteket telemetriatípusok, időintervallumok és egyéb tulajdonságok szerint elemezheti. 

Az adatokban előforduló rendellenességek felderítéséhez válassza valamelyik rendellenességi lehetőséget a „View Type” (Nézettípus) legördülő menüben. Az ablak alján található szűrőbeállítások megkönnyítik a telemetria bizonyos részhalmazainak alaposabb vizsgálatát.

![Trendek](./media/app-insights-visual-studio/51.png)

[További információ a Trendekről](app-insights-visual-studio-trends.md).

## A következő lépések

||
|---|---
|**[További adatok hozzáadása](app-insights-asp-net-more.md)**<br/>Figyelheti a használatot, az elérhetőséget, a függőségeket és a kivételeket. Integrálhatja a nyomkövetéseket naplózási keretrendszerekből. Egyéni telemetriát írhat. | ![Visual Studio](./media/app-insights-asp-net/64.png)
|**[Az Application Insights portál használata](app-insights-dashboards.md)**<br/>Az irányítópultok, a hatékony diagnosztikai és elemző eszközök, riasztások, egy élő függőségi térkép az alkalmazásához, valamint a telemetria exportálása. |![Visual Studio](./media/app-insights-asp-net/62.png)


 



<!--HONumber=sep16_HO1-->


