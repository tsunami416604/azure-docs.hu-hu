---
title: "Webalkalmazás-elemzés beállítása az ASP.NET-hez az Application Insights segítségével | Microsoft Docs"
description: "Konfigurálhatja a helyszínen vagy az Azure-ban üzemeltetett ASP.NET-webhely teljesítményét, rendelkezésre állását és használatelemzését."
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: douge
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/13/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b6e5898d94a43b6859ce354f154bdb25948f7686


---
# <a name="set-up-application-insights-for-aspnet"></a>Az Application Insights beállítása az ASP.NET-hez
A [Visual Studio Application Insights](app-insights-overview.md) élő alkalmazásfigyeléssel segíti a [teljesítménybeli problémák és kivételek észlelését és diagnosztizálását](app-insights-detect-triage-diagnose.md), valamint az [alkalmazáshasználat felderítését](app-insights-overview-usage.md).  Olyan alkalmazásokkal működik, amelyeket a saját helyszíni IIS-kiszolgálóin vagy felhőbeli VM-eken futtat, másrészt Azure-webalkalmazásokkal.

## <a name="before-you-start"></a>Előkészületek
A következők szükségesek:

* Visual Studio 2013 3. frissítés vagy újabb. Az újabb jobb.
* Egy [Microsoft Azure](http://azure.com)-előfizetés. Ha a csapata vagy a szervezete rendelkezik Azure-előfizetéssel, a tulajdonosa Önt is hozzáadhatja a [Microsoft-fiókja](http://live.com) segítségével. 

További cikkeket olvashat el, ha a következők érdeklik:

* [Webalkalmazás beállítása futási időben](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="a-nameidea-1-add-application-insights-sdk"></a><a name="ide"></a> 1. Application Insights SDK hozzáadása
### <a name="if-its-a-new-project"></a>Ha ez egy új projekt...
Amikor új projektet hoz létre a Visual Studióban, győződjön meg arról, hogy be van jelölve az Application Insights lehetőség. 

![ASP.NET-projekt létrehozása](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

### <a name="-or-if-its-an-existing-project"></a>...vagy ha egy meglévő projekt
Kattintson a jobb gombbal a projektre a Solution Explorer (Megoldáskezelő) területén, és válassza az **Add Application Insights Telemetry** (Application Insights telemetria hozzáadása) vagy a **Configure Application Insights** (Application Insights konfigurálása) elemet.

![Az Application Insights hozzáadása lehetőség kiválasztása](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* ASP.NET-magprojekt? – [Kövesse ezeket az utasításokat néhány sornyi kód javításához](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs). 

## <a name="a-nameruna-2-run-your-app"></a><a name="run"></a> 2. Az alkalmazás futtatása
Futtassa az F5 billentyűvel az alkalmazást, és próbálja ki: nyisson meg több oldalt, hogy létrejöjjön valamennyi telemetria.

A Visual Studióban láthatja a naplózott események számát. 

![A Visual Studióban megjelenik az Application Insights gomb a hibakeresés alatt.](./media/app-insights-asp-net/54.png)

## <a name="3-see-your-telemetry"></a>3. A telemetria megtekintése...
### <a name="-in-visual-studio"></a>... a Visual Studióban
Nyissa meg az Application Insights ablakot a Visual Studióban: Kattintson az Application Insights gombra, vagy kattintson a jobb gombbal a projektre a Solution Explorer (Megoldáskezelő) felületén:

![A Visual Studióban megjelenik az Application Insights gomb a hibakeresés alatt.](./media/app-insights-asp-net/55.png)

Ez a nézet az alkalmazás kiszolgálói oldalán létrehozott telemetriát jeleníti meg. Kísérletezzen a szűrőkkel, és kattintson valamely eseményre további részletek megtekintéséhez.

[További tudnivalók az Application Insights-eszközökről a Visual Studióban](app-insights-visual-studio.md).

<a name="monitor"></a> 

### <a name="-in-the-portal"></a>...a portálon
Ha nem a *Csak SDK telepítése* lehetőséget választja, az Application Insights webportálon is megtekintheti a telemetriát. 

A portálon a Visual Studiónál több diagram, elemzőeszköz és irányítópult található. 

Nyissa meg az Application Insights-erőforrást az [Azure Portalon](https://portal.azure.com/).

![Kattintson a jobb gombbal a projektre, és nyissa meg az Azure portált](./media/app-insights-asp-net/appinsights-04-openPortal.png)

A portál az alkalmazásából származó telemetriai adatok nézetével nyílik meg: ![](./media/app-insights-asp-net/66.png)

* Az első telemetria az [Élő mérőszámok streame](app-insights-metrics-explorer.md#live-metrics-stream) alatt jelenik meg.
* Az egyes események a **Keresés** mezőben jelennek meg (1). Az adatok megjelenítése eltarthat néhány percig. Kattintson egy eseményre a tulajdonságai megtekintéséhez. 
* Az összesített mérőszámok a diagramokban jelennek meg (2). Egy-két percet igénybe vehet, hogy az adatok itt megjelenjenek. Kattintson valamely diagramra egy további részleteket tartalmazó panel megnyitásához.

[További tudnivalók az Application Insights használatáról az Azure Portalon](app-insights-dashboards.md).

## <a name="4-publish-your-app"></a>4. Az alkalmazás közzététele
Tegye közzé alkalmazását az IIS-kiszolgálón vagy az Azure-on. Az [Élő mérőszámok streammel](app-insights-metrics-explorer.md#live-metrics-stream) ellenőrizheti, hogy minden rendben működik-e.

Látni fogja a telemetria felépülését az Application Insights portálon, ahol figyelheti a mérőszámokat, kereshet a telemetriára és [irányítópultokat](app-insights-dashboards.md) állíthat be. Használhatja a nagy teljesítményű [Analytics lekérdezési nyelvet](app-insights-analytics.md) a használat és a teljesítmény elemzéséhez, vagy megadott események megtalálásához. 

Folytathatja a telemetria elemzését a [Visual Studióban](app-insights-visual-studio.md) olyan eszközökkel, mint a diagnosztikai keresés és a [trendek](app-insights-visual-studio-trends.md).

> [!NOTE]
> Ha az alkalmazása elég telemetriát küld a [szabályozási korlát](app-insights-pricing.md#limits-summary) eléréséhez, az automatikus [mintavételezés](app-insights-sampling.md) bekapcsol. A mintavételezés csökkenti az alkalmazásból küldött telemetria mennyiségét, míg a korrelatív adatokat diagnosztikai célból megőrzi.
> 
> 

## <a name="a-namelanda-what-did-add-application-insights-do"></a><a name="land"></a> Mit csinált az „Application Insights hozzáadása”?
Az Application Insights telemetriát küld az alkalmazástól az Application Insights portálra (amely a Microsoft Azure-ban található):

![](./media/app-insights-asp-net/01-scheme.png)

Így a parancs három dolgot tett:

1. Az Application Insights Web SDK NuGet-csomagot hozzáadta a projekthez. A Visual Studióban való megtekintéshez kattintson a jobb gombbal a projektjére és válassza a Manage NuGet Packages (NuGet-csomagok kezelése) lehetőséget.
2. Létrehozott egy Application Insights-erőforrást [az Azure Portalon](https://portal.azure.com/). Itt láthatja az adatait. Lekéri a *kialakítási kulcsot*, amely azonosítja az erőforrást.
3. Beilleszti a kialakítási kulcsot az `ApplicationInsights.config` fájlba, hogy az SDK telemetriát küldhessen a portálra.

Ha szeretné, kézzel is elvégezheti ezeket a lépéseket az [ASP.NET 4](app-insights-windows-services.md) vagy az [ASP.NET-mag](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started) esetében.

### <a name="to-upgrade-to-future-sdk-versions"></a>Frissítés a jövőbeli SDK-verziókra
Ha frissíteni szeretne egy [új SDK-kiadásra](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), nyissa meg ismét a NuGet-csomagkezelőt, és szűréssel keresse meg a telepített csomagokat. Jelölje ki a Microsoft.ApplicationInsights.Web lehetőséget, és válassza a Frissítés elemet.

Ha az ApplicationInsights.config fájlt testreszabta, mentse el egy példányát a frissítés előtt, majd egyesítse a módosításait az új verzióval.

## <a name="add-more-telemetry"></a>További telemetriai funkciók hozzáadása
### <a name="web-pages-and-singlepage-apps"></a>Weboldalak és egylapos alkalmazások
1. [Adja hozzá a JavaScript-kódrészletet](app-insights-javascript.md) weboldalaihoz, és a Böngésző és a Felhasználás panelen megjelennek az oldalmegtekintésekkel, betöltési időkkel, böngészőbeli kivételekkel, az AJAX-hívások teljesítményével, valamint a felhasználók és munkamenetek számával kapcsolatos adatok.
2. [Kódoljon egyéni eseményeket](app-insights-api-custom-events-metrics.md) a felhasználói műveletek számlálásához, időzítéséhez és méréséhez.

### <a name="dependencies-exceptions-and-performance-counters"></a>Függőségek, kivételek és teljesítményszámlálók
[Telepítse az Állapotfigyelőt](app-insights-monitor-performance-live-website-now.md) az összes kiszolgálón, hogy további telemetriai adatokat gyűjtsön alkalmazásáról. Az alábbiakhoz jut hozzá:

* [Teljesítményszámlálók](app-insights-performance-counters.md)  - 
   az alkalmazással kapcsolatos CPU-, memória-, lemez- és más teljesítményszámlálók. 
* [Kivételek](app-insights-asp-net-exceptions.md) – részletesebb telemetria bizonyos kivételekről.
* [Függőségek](app-insights-asp-net-dependencies.md) – REST API- vagy SQL-szolgáltatáshívások. Megállapíthatja, hogy a külső összetevők lassú válaszai teljesítményproblémákat okoznak-e alkalmazásában. (Ha az alkalmazás a .NET 4.6-os verzióját használja, ezekhez a telemetriai adatokhoz az Állapotfigyelő nélkül is hozzájuthat.)

### <a name="diagnostic-code"></a>Diagnosztikai kód
Problémát tapasztalt? Ha a könnyebb diagnosztizálás érdekében kódot helyezne alkalmazásába, több lehetősége van:

* [Naplókivonatok rögzítése](app-insights-asp-net-trace-logs.md): Ha már a Log4N, az NLog vagy a System.Diagnostics.Trace keretrendszert használja a nyomkövetési események naplózásához, a kimenetet az Application Insightshoz küldheti, így összehasonlíthatja azt a kérésekkel, kereshet benne, és elemezheti is. 
* [Egyéni események és a metrikák](app-insights-api-custom-events-metrics.md): Használja a TrackEvent() és a TrackMetric() függvényt a kiszolgáló- vagy weblapkódban.
* [A telemetriai adatok címkézése további tulajdonságokkal](app-insights-api-filtering-sampling.md#add-properties)

Használja a [Keresés](app-insights-diagnostic-search.md) funkciót az egyes események összehasonlításához, az [Analitika](app-insights-analytics.md) funkciót pedig hathatósabb lekérdezésék végrehajtásához.

## <a name="alerts"></a>Riasztások
Legyen az első, aki értesül arról, hogy alkalmazásának problémája van. (Ne várjon addig, míg a felhasználóitól értesüljön róla!) 

* [Hozzon létre webteszteket](app-insights-monitor-web-app-availability.md), hogy megbizonyosodjon róla, oldala látható a weben.
* [A proaktív diagnosztika](app-insights-proactive-diagnostics.md) automatikusan fut (ha az alkalmazás egy bizonyos minimális forgalmat bonyolít le). A beállításhoz semmit sem kell tennie. Értesítést kap, ha az alkalmazásában szokatlanul magas a meghiúsult kérelmek száma.
* [Állítson be metrikariasztásokat](app-insights-alerts.md), hogy figyelmeztetést kapjon, ha egy metrika átlépi a küszöbértéket. Az alkalmazás kódjába beépített egyedi metrikákhoz is állíthat be riasztásokat.

Alapértelmezés szerint a riasztási értesítéseket az Azure-előfizetés tulajdonosa kapja meg. 

![riasztási e-mail-minta](./media/app-insights-asp-net/alert-email.png)

## <a name="version-and-release-tracking"></a>Verzió- és kiadáskövetés
### <a name="track-application-version"></a>Alkalmazásverzió követése
Győződjön meg arról, hogy az MSBuild folyamat létrehozza a `buildinfo.config` fájlt. A .csproj fájlban adja hozzá a következőt:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

Ha megkapja a verzióinformációkat, az Application Insights webmodul automatikusan hozzáadja az **Alkalmazás verzióját** tulajdonságként a telemetria minden eleméhez. Ez lehetővé teszi a verziók szerinti szűrést, amikor [diagnosztikai kereséseket](app-insights-diagnostic-search.md) végez, illetve [metrikákat vizsgál](app-insights-metrics-explorer.md). 

Ne feledje viszont, hogy a buildverzió számát csak az MS Build hozza létre, a Visual Studio fejlesztői buildje nem.

### <a name="release-annotations"></a>Kiadási jegyzetek
Ha a Visual Studio Team Servicest használja, egy új verzió kibocsátásakor diagramjaihoz [kiadási jelölőt](app-insights-annotations.md) adhat hozzá.

![kiadásijegyzet-minta](./media/app-insights-asp-net/release-annotation.png)

## <a name="next-steps"></a>Következő lépések
|  |
| --- | --- |
| **[Az Application Insights használata a Visual Studióban](app-insights-visual-studio.md)**<br/>Hibakeresés telemetriával, diagnosztikai keresés, részletezés lefúrás a kódig. |
| **[Az Application Insights-portál használata](app-insights-dashboards.md)**<br/>Az irányítópultok, a hatékony diagnosztikai és elemző eszközök, riasztások, egy élő függőségi térkép az alkalmazásához, valamint a telemetria exportálása. |
| **[További adatok hozzáadása](app-insights-asp-net-more.md)**<br/>Figyelheti a használatot, az elérhetőséget, a függőségeket és a kivételeket. Integrálhatja a nyomkövetéseket naplózási keretrendszerekből. Egyéni telemetriát írhat. |




<!--HONumber=Nov16_HO2-->


