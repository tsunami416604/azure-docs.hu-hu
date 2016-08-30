<properties
    pageTitle="Az Application Insights SDK hozzáadása az ASP.NET alkalmazás figyelésére | Microsoft Azure"
    description="Az Application Insights segítségével elemezheti a használati adatokat, a rendelkezésre állást és a teljesítményt a helyszíni vagy Microsoft Azure webalkalmazásán."
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
    ms.date="05/19/2016"
    ms.author="awills"/>


# Az Application Insights SDK hozzáadása az ASP.NET alkalmazás figyelésére

*Az Application Insights jelenleg még előzetes verziójú kiadásban érhető el.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


A Visual Studio Application Insights élő alkalmazásfigyeléssel segíti a [teljesítménybeli problémák és kivételek észlelését és diagnosztizálását][detect], valamint az [alkalmazáshasználat megismerését][knowUsers]. Használható számos különböző alkalmazástípussal. Egyrészt olyan alkalmazásokkal működik, amelyeket a saját helyszíni IIS-kiszolgálóin vagy Azure VM-eken futtat, másrészt Azure webalkalmazásokkal.



![Példa teljesítményfigyelő diagramokra](./media/app-insights-asp-net-manual/10-perf.png)

*Lásd még:*

* [ASP.NET 5](app-insights-asp-net-five.md)
* [Eszközalkalmazások és Java kiszolgálók][platformok]

#### Előkészületek

Számos alkalmazástípusnál a [Visual Studio képes hozzáadni az Application Insights szolgáltatást az alkalmazáshoz](#ide) úgy, hogy szinte észre sem veszi. De minthogy ennek az anyagnak acélja épp az, hogy alaposan megértse, mi is történik, végigvezetjük a lépéseken manuálisan.


A következők szükségesek:

* Egy [Microsoft Azure](http://azure.com)-előfizetés. Ha a csapata vagy a szervezete rendelkezik Azure-előfizetéssel, a tulajdonosa Önt is hozzáadhatja a [Microsoft-fiókja](http://live.com) segítségével.
* Visual Studio 2013 vagy újabb.

## <a name="add"></a>Application Insights-erőforrás létrehozása

Jelentkezzen be az [Azure portálra][portal], és hozzon létre egy új Application Insights-erőforrást. Az alkalmazás típusának válassza az ASP.NET lehetőséget.

![Kattintson az Új, majd az Application Insights lehetőségre](./media/app-insights-asp-net-manual/01-new-asp.png)

Az Azure-ban egy [erőforrás][roles] lényegében egy szolgáltatáspéldány. Az alkalmazás telemetriájának elemzése és bemutatása az erőforrásban történik.

A kiválasztott alkalmazástípus adja meg az erőforráspanelek alapértelmezett tartalmát, valamint a [Metrikaböngészőben][metrics] látható tulajdonságokat.

#### A kialakítási kulcs másolása

A kulcs azonosítja az erőforrást, és hamarosan telepíteni fogja azt az SDK-ba, hogy az adatokat az erőforrásba irányíthassa.

![Kattintson a Tulajdonságok elemre, válassza ki a kulcsot, és nyomja le a ctrl+C billentyűkombinációt.](./media/app-insights-asp-net-manual/02-props-asp.png)

Az új erőforrás létrehozásához az imént elvégzett lépések jól használhatók bármely alkalmazás figyelésének megkezdéséhez. Most már küldhet ide adatokat.

## <a name="sdk"></a> Az SDK telepítése az alkalmazásban

Az Application Insights SDK telepítése és konfigurálása a használt platformtól függően eltérő lehet. ASP.NET alkalmazásoknál ez nagyon egyszerű.

1. Visual Studióban szerkessze a webalkalmazási projekt NuGet-csomagjait.

    ![Kattintson a jobb gombbal a projektre, és válassza a Manage Nuget Packages (NuGet-csomagok kezelése) lehetőséget](./media/app-insights-asp-net-manual/03-nuget.png)

2. Az Application Insights SDK telepítése a webalkalmazásokhoz

    ![Az „Application Insights” kifejezés keresése](./media/app-insights-asp-net-manual/04-ai-nuget.png)

3. Szerkessze az ApplicationInsights.config fájlt (ezt korábban a NuGet telepítése során adta hozzá). A címke zárása elé illessze be a következőt:

    `<InstrumentationKey>` *a kimásolt kialakítási kulcs* `</InstrumentationKey>`

    (Másik lehetőségként [beállíthatja a kulcsot, ha a kódot][apikey] beírja az alkalmazásba.)

#### Frissítés a jövőbeli SDK-verziókra

Időről-időre kiadunk egy új SDK-verziót.

Ha frissíteni szeretne egy [új SDK-kiadásra](app-insights-release-notes-dotnet.md), nyissa meg ismét a NuGet-csomagkezelőt, és szűréssel keresse meg a telepített csomagokat. Jelölje ki a **Microsoft.ApplicationInsights.Web** lehetőséget, és válassza a **Frissítés** elemet.

Ha az ApplicationInsights.config fájlt testreszabta, mentse el egy példányát a frissítés előtt, majd egyesítse a módosításait az új verzióval.


## <a name="run"></a> A projekt futtatása

Futtassa az **F5** billentyűvel az alkalmazást, és próbálja ki: nyisson meg több oldalt, hogy létrejöjjön valamennyi telemetria.

A Visual Studióban láthatja az elküldött események számát.

![](./media/app-insights-asp-net-manual/appinsights-09eventcount.png)

## <a name="monitor"></a> A telemetria megtekintése

Térjen vissza az [Azure portálra][portal], és keresse meg az Application Insights-erőforrását.


Az Áttekintés diagramokon keresse meg az adatot. Először csak egy vagy két pontot lát. Példa:

![Kattintson végig rajtuk a további adatokért](./media/app-insights-asp-net-manual/12-first-perf.png)

Részletesebb mérőszámokért kattintson bármelyik diagramra. [További információk a metrikákról.][perf]

#### Nincs adat?

* Az alkalmazás segítségével nyisson meg különböző oldalakat, hogy létrejöjjön némi telemetria.
* Az egyes események megtekintéséhez nyissa meg a [Keresés][diagnostic] csempét. Események esetében kicsit tovább is eltarthat a mérőszámok folyamatain való végighaladás.
* Várjon néhány másodpercet, és kattintson a **Frissítés** lehetőségre. A diagramok rendszeres időközönként frissülnek, de manuálisan is frissítheti őket, ha várja valamilyen adatok megjelenését.
* Lásd: [Hibaelhárítás][qna].

## Az alkalmazás közzététele

Most telepítse az alkalmazását az IIS-be vagy az Azure-be, és figyelje meg, hogyan gyűlnek az adatok.

![Az alkalmazás közzététele a Visual Studio segítségével](./media/app-insights-asp-net-manual/15-publish.png)

Ha hibakeresési módban futtatja az alkalmazást, az egész folyamatban szolgáltat telemetriát a rendszer, így másodperceken belül meg kell jelenniük az adatoknak. Ha Kiadás konfigurációban telepíti az alkalmazását, az adatok lassabban gyűlnek.

#### Nem lát adatokat a kiszolgálón való közzététel után?

Nyissa meg ezeket a portokat a kimenő forgalom számára a kiszolgáló tűzfalán:

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`


#### Probléma adódott a lemezképfájl-kiszolgálóján? 

Tekintse meg [ezt a Hibaelhárítási cikket](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [AZURE.NOTE] Ha az alkalmazása sok telemetriát hoz létre (és az ASP.NET SDK 2.0.0-beta3 vagy újabb verzióját használja), az adaptív mintavételezési modul automatikusan csökkenti a portálra küldött kötetet, csupán az eseményeket megjelenítő töredékeket küld. Az azonos kéréshez tartozó események azonban csoportosan lesznek kijelölve, illetve így lesz törölve a jelölésük, hogy lehessen mozogni a kapcsolódó események között. 
> [Ismerkedés a mintavételezéssel](app-insights-sampling.md).


## Függőségi nyomkövetés (és IIS teljesítményszámlálók) hozzáadása

Az SDK-nak segítségre van szüksége bizonyos adatok eléréséhez. Erre a további lépésre akkor van különösen szükség, ha automatikusan szeretné mérni az alkalmazástól az adatbázisok, REST API-k és egyéb külső összetevők felé irányuló hívásokat. Előfordul, hogy ezek a függőségi adatok felbecsülhetetlen segítséget nyújtanak a teljesítményproblémák diagnosztikája során.

Ha a saját IIS-kiszolgálóján futtatja az alkalmazást, ez a lépés lehetővé teszi, hogy a rendszer teljesítményszámlálói megjelenjenek a [Metrikaböngészőben](app-insights-metrics-explorer.md).

#### Ha az alkalmazás az IIS-kiszolgálóján fut

Jelentkezzen be a kiszolgálóra rendszergazdai jogosultságokkal, majd telepítse az [Application Insights Állapotfigyelőt](http://go.microsoft.com/fwlink/?LinkId=506648).

Előfordulhat, hogy [meg kell nyitnia további kimenő portokat a tűzfalán](app-insights-monitor-performance-live-website-now.md#troubleshooting).

Ez a lépés lehetővé teszi olyan [teljesítményszámlálók figyelését](app-insights-web-monitor-performance.md#system-performance-counters), mint a CPU, a memória vagy a hálózat foglaltsága.

#### Ha az alkalmazás egy Azure-webalkalmazás

Az Azure-webalkalmazás vezérlőpultján adja hozzá az Application Insights bővítményt.

![A webalkalmazásban Beállítások, Bővítmények, Hozzáadás, Application Insights](./media/app-insights-asp-net-manual/05-extend.png)


#### Ha ez egy Azure-felhőszolgáltatási projekt

[Adjon hozzá parancsfájlokat a webes és feldolgozói szerepkörökhöz](app-insights-cloudservices.md).



## Ügyféloldali megfigyelés hozzáadása

Telepítette az SDK-t, amely elküldi a telemetriai adatokat az alkalmazás kiszolgálójáról (a háttérből). Most hozzáadhatja az ügyféloldali megfigyelést. Ez adatokat szolgáltat a felhasználókról, munkamenetekről és az oldalak megtekintéséről, valamint a böngészőben előforduló kivételekről és összeomlásokról. Megírhatja a saját kódját is, hogy nyomon követhesse, hogyan használják a felhasználók az alkalmazását, akár részletekbe menően, a kattintásokat és a billentyűleütéseket is figyelembe véve.


Mindegyik oldalon elhelyezhet egy JavaScript kódrészletet. A kód lekérése az Application Insights-erőforrásból:

![A webalkalmazásban nyissa meg a gyors üzembe helyezést, és kattintson a „Kód lekérése a weblapok figyeléséhez” lehetőségre](./media/app-insights-asp-net-manual/02-monitor-web-page.png)

Vegye észre, hogy a kódban szerepel az alkalmazás-erőforrás kialakítási kódja.

[További tudnivalók a weblapok követéséről.](app-insights-web-track-usage.md)


## Alkalmazásverzió követése

Győződjön meg arról, hogy az MSBuild folyamat létrehozza a `buildinfo.config` fájlt. A .csproj fájlban adja hozzá a következőt:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

Ha megkapja a verzióinformációkat, az Application Insights webmodul automatikusan hozzáadja az **Alkalmazás verzióját** tulajdonságként a telemetria minden eleméhez. Ez lehetővé teszi a verziók szerinti szűrést, amikor [diagnosztikai kereséseket][diagnostic] végez, illetve [mérőszámokat derít fel][metrics]. 

Ne feledje viszont, hogy a buildverzió számát csak az MS Build hozza létre, a Visual Studio fejlesztői buildje nem.

## A telepítés befejezése

Az alkalmazás teljes körű megfigyelése érdekében elvégezhet még néhány dolgot:

* [Beállíthat webes teszteket][availability]annak biztosításához, hogy az alkalmazás mindig elérhető és válaszkész legyen.
* [Rögzítheti a naplóbejegyzéseket][netlogs] a kedvenc naplózási keretrendszeréből.
* [Nyomon követheti az egyéni eseményeket és mérőszámokat][api] az ügyfélen, a kiszolgálón vagy mindkettőn, így további információkat szerezhet az alkalmazása használatáról.

## <a name="ide"></a> Automatizált mód

Jelen cikk bevezetésénél már említettük, hogy bemutatjuk az Application Insights erőforrás létrehozásának, majd az SDK telepítésének manuális módját. Úgy véljük, fontos megismerni az eljárás két részét. ASP.NET alkalmazások (és még sok más) esetében azonban van még egy gyorsabb, automatizált módszer.

Ehhez szüksége lesz a [Visual Studióra](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (2013 update 3 vagy újabb) és egy [Microsoft Azure](http://azure.com)-fiókra.

#### Ha ez egy új projekt...

Amikor új projektet hoz létre a Visual Studióban, győződjön meg arról, hogy be van jelölve az **Add Application Insights** (Application Insights hozzáadása) lehetőség.


![ASP.NET-projekt létrehozása](./media/app-insights-asp-net-manual/appinsights-01-vsnewp1.png)

A Visual Studio létrehoz egy erőforrást az Application Insights alkalmazásban, hozzáadja az SDK-t a projekthez, és elhelyezi a kulcsot a `.config` fájlban.

Ha a projekthez tartoznak weblapok, hozzáadja a [JavaScript SDK-t][client] is a fő weblaphoz.

#### ...vagy ha egy meglévő projekt

Kattintson a jobb gombbal a projektre a Solution Explorerben (Megoldáskezelőben), és válassza az **Add Application Insights** (Application Insights hozzáadása) lehetőséget.

![Az Application Insights hozzáadása lehetőség kiválasztása](./media/app-insights-asp-net-manual/appinsights-03-addExisting.png)

A Visual Studio létrehoz egy erőforrást az Application Insights alkalmazásban, hozzáadja az SDK-t a projekthez, és elhelyezi a kulcsot a `.config` fájlban.

Ebben az esetben nem adja hozzá a [JavaScript SDK-t][client] a weblapjaihoz, ezért javasoljuk, hogy ez legyen a következő lépése.

#### Beállítási lehetőségek

Ha ez az első alkalom, a rendszer felkéri, hogy jelentkezzen be vagy regisztráljon a Microsoft Azure Preview szolgáltatásba. 

Ha ez az alkalmazás egy nagyobb alkalmazás része, érdemes lehet a **Configure settings** (Beállítások megadása) segítségével ugyanabba az erőforráscsoportba helyezni, mint a többi összetevőt.

*Nem található Application Insights lehetőség? Ellenőrizze, hogy a Visual Studio 2013 Update 3 vagy későbbi verzióját használja-e, valamint hogy az Application Insights eszközök engedélyezve vannak-e a Bővítmények és Frissítések területen.*

#### Az Application Insights megnyitása a projektből

![Kattintson a jobb gombbal a projektre, és nyissa meg az Azure portált](./media/app-insights-asp-net-manual/appinsights-04-openPortal.png)




## <a name="video"></a>Videó

> [AZURE.VIDEO getting-started-with-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[Azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[platformok]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md



<!--HONumber=jun16_HO2-->


