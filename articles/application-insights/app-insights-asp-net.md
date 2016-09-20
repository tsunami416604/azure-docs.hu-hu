<properties 
    pageTitle="Webalkalmazás-elemzés beállítása az ASP.NET-hez az Application Insights segítségével" 
    description="Konfigurálhatja a helyszínen vagy az Azure-ban üzemeltetett ASP.NET-webhely teljesítményét, rendelkezésre állását és használatelemzését." 
    services="application-insights" 
    documentationCenter=".net"
    authors="NumberByColors" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/09/2016" 
    ms.author="daviste"/>


# Az Application Insights beállítása az ASP.NET-hez

A [Visual Studio Application Insights](app-insights-overview.md) élő alkalmazásfigyeléssel segíti a [teljesítménybeli problémák és kivételek észlelését és diagnosztizálását](app-insights-detect-triage-diagnose.md), valamint az [alkalmazáshasználat felderítését](app-insights-overview-usage.md).  Olyan alkalmazásokkal működik, amelyeket a saját helyszíni IIS-kiszolgálóin vagy felhőbeli VM-eken futtat, másrészt Azure-webalkalmazásokkal.


## Előkészületek

A következők szükségesek:

* Visual Studio 2013 3. frissítés vagy újabb. Az újabb jobb.
* Egy [Microsoft Azure](http://azure.com)-előfizetés. Ha a csapata vagy a szervezete rendelkezik Azure-előfizetéssel, a tulajdonosa Önt is hozzáadhatja a [Microsoft-fiókja](http://live.com) segítségével. 

További cikkeket olvashat el, ha a következők érdeklik:

* [Webalkalmazás beállítása futási időben](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="ide"></a> 1. Application Insights SDK hozzáadása


### Ha ez egy új projekt...

Amikor új projektet hoz létre a Visual Studióban, győződjön meg arról, hogy be van jelölve az Application Insights lehetőség. 


![ASP.NET-projekt létrehozása](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


### ...vagy ha egy meglévő projekt

Kattintson a jobb gombbal a projektre a Solution Explorer (Megoldáskezelő) területén, és válassza az **Add Application Insights Telemetry** (Application Insights telemetria hozzáadása) vagy a **Configure Application Insights** (Application Insights konfigurálása) elemet.

![Az Application Insights hozzáadása lehetőség kiválasztása](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* ASP.NET-magprojekt? – [Kövesse ezeket az utasításokat néhány sornyi kód javításához](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs). 



## <a name="run"></a> 2. Az alkalmazás futtatása

Futtassa az F5 billentyűvel az alkalmazást, és próbálja ki: nyisson meg több oldalt, hogy létrejöjjön valamennyi telemetria.

A Visual Studióban láthatja a naplózott események számát. 

![A Visual Studióban megjelenik az Application Insights gomb a hibakeresés alatt.](./media/app-insights-asp-net/54.png)

## 3. A telemetria megtekintése...

### ... a Visual Studióban

Nyissa meg az Application Insights ablakot a Visual Studióban: Kattintson az Application Insights gombra, vagy kattintson a jobb gombbal a projektre a Solution Explorer (Megoldáskezelő) felületén:

![A Visual Studióban megjelenik az Application Insights gomb a hibakeresés alatt.](./media/app-insights-asp-net/55.png)

Ez a nézet az alkalmazás kiszolgálói oldalán létrehozott telemetriát jeleníti meg. Kísérletezzen a szűrőkkel, és kattintson valamely eseményre további részletek megtekintéséhez.

[További tudnivalók az Application Insights-eszközökről a Visual Studióban](app-insights-visual-studio.md).

<a name="monitor"></a> 
### ...a portálon

Ha nem a *Csak SDK telepítése* lehetőséget választja, az Application Insights webportálon is megtekintheti a telemetriát. 

A portálon a Visual Studiónál több diagram, elemzőeszköz és irányítópult található. 


Nyissa meg az Application Insights-erőforrást az [Azure Portalon](https://portal.azure.com/).

![Kattintson a jobb gombbal a projektre, és nyissa meg az Azure portált](./media/app-insights-asp-net/appinsights-04-openPortal.png)

A portál az alkalmazásából a telemetria egy nézetével nyílik meg:
![](./media/app-insights-asp-net/66.png)

* Az első telemetria az [Élő mérőszámok streame](app-insights-metrics-explorer.md#live-metrics-stream) alatt jelenik meg.
* Az egyes események a **Keresés** mezőben jelennek meg (1). Az adatok megjelenítése eltarthat néhány percig. Kattintson egy eseményre a tulajdonságai megtekintéséhez. 
* Az összesített mérőszámok a diagramokban jelennek meg (2). Egy-két percet igénybe vehet, hogy az adatok itt megjelenjenek. Kattintson valamely diagramra egy további részleteket tartalmazó panel megnyitásához.

[További tudnivalók az Application Insights használatáról az Azure Portalon](app-insights-dashboards.md).

## 4. Az alkalmazás közzététele

Tegye közzé alkalmazását az IIS-kiszolgálón vagy az Azure-on. Az [Élő mérőszámok streammel](app-insights-metrics-explorer.md#live-metrics-stream) ellenőrizheti, hogy minden rendben működik-e.

Látni fogja a telemetria felépülését az Application Insights portálon, ahol figyelheti a mérőszámokat, kereshet a telemetriára és [irányítópultokat](app-insights-dashboards.md) állíthat be. Használhatja a nagy teljesítményű [Analytics lekérdezési nyelvet](app-insights-analytics.md) a használat és a teljesítmény elemzéséhez, vagy megadott események megtalálásához. 

Folytathatja a telemetria elemzését a [Visual Studióban](app-insights-visual-studio.md) olyan eszközökkel, mint a diagnosztikai keresés és a [trendek](app-insights-visual-studio-trends.md).

> [AZURE.NOTE] Ha az alkalmazása elég telemetriát küld a [szabályozási korlát](app-insights-pricing.md#limits-summary) eléréséhez, az automatikus [mintavételezés](app-insights-sampling.md) bekapcsol. A mintavételezés csökkenti az alkalmazásból küldött telemetria mennyiségét, míg a korrelatív adatokat diagnosztikai célból megőrzi.


##<a name="land"></a> Mit csinált az „Application Insights hozzáadása”?

Az Application Insights telemetriát küld az alkalmazástól az Application Insights portálra (amely a Microsoft Azure-ban található):

![](./media/app-insights-asp-net/01-scheme.png)

Így a parancs három dolgot tett:

1. Az Application Insights Web SDK NuGet-csomagot hozzáadta a projekthez. A Visual Studióban való megtekintéshez kattintson a jobb gombbal a projektjére és válassza a Manage NuGet Packages (NuGet-csomagok kezelése) lehetőséget.
2. Létrehozott egy Application Insights-erőforrást [az Azure Portalon](https://portal.azure.com/). Itt láthatja az adatait. Lekéri a *kialakítási kulcsot*, amely azonosítja az erőforrást.
3. Beilleszti a kialakítási kulcsot az `ApplicationInsights.config` fájlba, hogy az SDK telemetriát küldhessen a portálra.

Ha szeretné, kézzel is elvégezheti ezeket a lépéseket az [ASP.NET 4](app-insights-asp-net-manual.md) vagy az [ASP.NET-mag](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started) esetében.

### Frissítés a jövőbeli SDK-verziókra

Ha frissíteni szeretne egy [új SDK-kiadásra](app-insights-release-notes-dotnet.md), nyissa meg ismét a NuGet-csomagkezelőt, és szűréssel keresse meg a telepített csomagokat. Jelölje ki a Microsoft.ApplicationInsights.Web lehetőséget, és válassza a Frissítés elemet.

Ha az ApplicationInsights.config fájlt testreszabta, mentse el egy példányát a frissítés előtt, majd egyesítse a módosításait az új verzióval.



## A következő lépések

| | 
|---|---
|**[Az Application Insights használata a Visual Studióban](app-insights-visual-studio.md)**<br/>Hibakeresés telemetriával, diagnosztikai keresés, részletezés lefúrás a kódig.|![Visual Studio](./media/app-insights-asp-net/61.png)
|**[Az Application Insights portál használata](app-insights-dashboards.md)**<br/>Az irányítópultok, a hatékony diagnosztikai és elemző eszközök, riasztások, egy élő függőségi térkép az alkalmazásához, valamint a telemetria exportálása. |![Visual Studio](./media/app-insights-asp-net/62.png)
|**[További adatok hozzáadása](app-insights-asp-net-more.md)**<br/>Figyelheti a használatot, az elérhetőséget, a függőségeket és a kivételeket. Integrálhatja a nyomkövetéseket naplózási keretrendszerekből. Egyéni telemetriát írhat. | ![Visual Studio](./media/app-insights-asp-net/64.png)









<!--HONumber=sep16_HO1-->


