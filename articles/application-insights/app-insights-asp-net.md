---
title: "Webalkalmazás-elemzés beállítása az ASP.NET-hez az Azure Application Insights segítségével | Microsoft Docs"
description: "Konfigurálhatja a helyszínen vagy az Azure-ban üzemeltetett ASP.NET-webhely teljesítményét, rendelkezésre állását és használatelemzését."
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 05b642949205d7698fbcf791a5d2f06528ff239e
ms.openlocfilehash: e827bddfa0bcc1c1e46d06c6856033e77859c7fb
ms.lasthandoff: 02/28/2017


---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Az Application Insights beállítása az ASP.NET-webhelyhez
Az [Azure Application Insights](app-insights-overview.md) élő alkalmazásfigyeléssel segíti a [teljesítménybeli problémák és kivételek észlelését és diagnosztizálását](app-insights-detect-triage-diagnose.md). Emellett támogatja [az alkalmazáshasználat felderítését](app-insights-overview-usage.md) is. Az Azure App Service Web Apps szolgáltatásával, valamint olyan alkalmazásokkal működik, amelyeket a saját helyszíni IIS-kiszolgálóin vagy felhőbeli VM-eken futtat.

## <a name="before-you-start"></a>Előkészületek
A következők szükségesek:

* Visual Studio 2013 3. frissítés vagy újabb. Az újabb jobb.
* Egy [Microsoft Azure](http://azure.com)-előfizetés. Ha a csapata vagy a szervezete rendelkezik Azure-előfizetéssel, a tulajdonosa Önt is hozzáadhatja a [Microsoft-fiókja](http://live.com) segítségével.

További témaköröket is elolvashat, ha a következők érdeklik:

* [Webalkalmazás beállítása futási időben](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="a-nameidea-step-1-add-the-application-insights-sdk"></a><a name="ide"></a> 1. lépés: Az Application Insights SDK hozzáadása

Kattintson a jobb gombbal a webalkalmazás-projektre a Solution Explorer (Megoldáskezelő) területén, és válassza az **Add** (Hozzáadás), **Application Insights Telemetry** (Application Insights-telemetria) vagy a **Configure Application Insights** (Application Insights konfigurálása) elemet.

![A Solution Explorer (Megoldáskezelő) képernyőképe, a kiemelt Add Application Insights Telemetry (Application Insights telemetria hozzáadása) elemmel](./media/app-insights-asp-net/appinsights-03-addExisting.png)

(A Visual Studio 2015-ben emellett lehetőség van az Application Insights hozzáadására a New Project (Új projekt) párbeszédpanelen.)

Lépjen tovább az Application Insights konfigurációs oldalára:

![Képernyőkép az alkalmazásregisztrációs szakaszról az Application Insights oldalon](./media/app-insights-asp-net/visual-studio-register-dialog.png)

1. Válassza ki az Azure eléréséhez használt fiókot és előfizetést.
2. Válassza ki azt az Azure-beli erőforrást, amelyben látni szeretné az alkalmazásából származó adatokat. Általában minden alkalmazáshoz külön erőforrást kell létrehozni. Ha szeretné beállítani az erőforráscsoportot vagy az adatok tárolásának helyét, kattintson a **Configure settings** (Beállítások konfigurálása) lehetőségre. Az erőforráscsoportok az adatokhoz való hozzáférés szabályozásához használhatóak. Ha például több alkalmazása is van, amelyek egy adott rendszer részét képezik, azok Application Insights-adatait ugyanabba az erőforráscsoportba helyezheti.
3. Az Application Insights egy bizonyos telemetriamennyiségig ingyenes. Ha nem szeretne fizetni érte, beállíthatja ezt a mennyiséget korlátként. Az erőforrás létrehozása után módosíthatja a választást a portálon a **Szolgáltatások + díjszabás**, **Adatmennyiség kezelése**, **Napi mennyiségkorlát** lehetőség kiválasztásával.
4. A folytatáshoz és az Application Insights a webapphoz való konfigurálásához kattintson a **Regisztrálás** gombra. A telemetria az [Azure Portalra](https://portal.azure.com) lesz küldve a hibakeresés során és az alkalmazás közzététele után is.
5. Másik megoldásként egyszerűen hozzáadhatja az Application Insights SDK-t az alkalmazáshoz. Ebben az esetben a telemetria a hibakeresés során a Visual Studióban lesz megtekinthető. Később visszatérhet erre a konfigurációs oldalra, vagy megvárhatja az alkalmazás üzembe helyezését, és [bekapcsolhatja a telemetriát a futtatás során](app-insights-monitor-performance-live-website-now.md).


## <a name="a-nameruna-step-2-run-your-app"></a><a name="run"></a> 2. lépés: Az alkalmazás futtatása
Futtassa az alkalmazást az F5 billentyűvel. Nyisson meg több lapot, hogy létrejöjjön valamennyi telemetria.

A Visual Studióban láthatja a naplózott események számát.

![A Visual Studio képernyőképe. Megjelenik az Application Insights gomb a hibakeresés alatt.](./media/app-insights-asp-net/54.png)

## <a name="step-3-see-your-telemetry-in-visual-studio-or-application-insights"></a>3. lépés: Telemetria megtekintése a Visual Studióban vagy az Application Insightsban
A telemetriát a Visual Studióban vagy az Application Insights webportálon tekintheti meg.

**A Visual Studióban** nyissa meg az Application Insights ablakot. Kattintson az **Application Insights** gombra, vagy kattintson a jobb gombbal a projektre a Solution Explorer (Megoldáskezelő) felületén, válassza az **Application Insights** elemet, majd kattintson a **Search Live Telemetry** (Élő telemetria keresése) gombra.

A Visual Studio Application Insights keresőablakában a **Data from Debug session** (Hibakeresési munkamenetből származó adatok) nézetben tekintheti meg az alkalmazás kiszolgálói oldalán létrehozott telemetriát. Kísérletezzen a szűrőkkel, és kattintson valamely eseményre további részletek megtekintéséhez.

![A Data from debug session (Hibakeresési munkamenetből származó adatok) nézet képernyőképe az Application Insights ablakban.](./media/app-insights-asp-net/55.png)

> [!NOTE]
> Ha nem jelennek meg adatok, ellenőrizze, hogy megfelelő-e az időtartomány, majd kattintson a Search (Keresés) ikonra.

[További tudnivalók az Application Insights-eszközökről a Visual Studióban](app-insights-visual-studio.md).

<a name="monitor"></a>
### <a name="the-application-insights-web-portal"></a>Az Application Insights webportál
A telemetriát **az Application Insights webportálon** is megtekintheti, ha nem csak az SDK telepítése mellett döntött. A portálon a Visual Studiónál több diagram, elemzőeszköz és irányítópult található.

Nyissa meg az Application Insights-erőforrást. Bejelentkezhet az [Azure Portalra](https://portal.azure.com/), és itt megkeresheti, vagy a jobb gombbal kattinthat a projektre a Visual Studióban, amely odavezeti.

![A Visual Studio képernyőképe, amelyen az Application Insights portál megnyitásának módja látható](./media/app-insights-asp-net/appinsights-04-openPortal.png)

> [!NOTE]
> Ha hibaüzenetet kapott, lehet, hogy több microsoftos hitelesítőadat-készlettel rendelkezik, és nem a megfelelővel jelentkezett be. Jelentkezzen ki, majd be a portálon.

A portál az alkalmazásából származó telemetriai adatok nézetével nyílik meg.
![Az Application Insights áttekintési oldalának képernyőképe](./media/app-insights-asp-net/66.png)

Az egyik csempére vagy diagramra kattintva további részleteket tekinthet meg.

### <a name="more-details-in-the-application-insights-web-portal"></a>Az Application Insights webportálon látható további információk
Néhány példa arra, hogyan nyújt további információt a portál.

* Az [**Élő metrikastream** ](app-insights-live-stream.md) szinte azonnal megjeleníti a telemetriát.

    ![A portál képernyőképe. Az Áttekintés panelen kattintson az Élő streamre.](./media/app-insights-asp-net/livestream.png)

    Az Élő mérőszámstreamet az alkalmazás futásával egy időben megnyitva engedélyezheti a csatlakozást.

    Az Élő mérőszámstream a küldés után csupán egy percig jeleníti meg a telemetriát. További előzményvizsgálatokhoz használja a Keresést, a Metrikaböngészőt és az Analitikát. Az adatok megjelenítése e helyeken eltarthat néhány percig.

* A [**Keresés**](app-insights-diagnostic-search.md) egyes eseményeket, például kérelmeket, kivételeket és lapmegtekintéseket jeleníthet meg. A szűrés elvégezhető az eseménytípusok, a kifejezési egyezések és a tulajdonságok értékei alapján. Kattintson valamely eseményre a tulajdonságai és a kapcsolódó eseményei megtekintéséhez.

    ![A portál képernyőképe. Az Áttekintés panelen kattintson a Keresés elemre.](./media/app-insights-asp-net/search.png)

 * A fejlesztői módban számos függő (AJAX) esemény megtekintésére van lehetőség. Ezek a böngésző és a kiszolgálóemulátor közötti szinkronizálások. Az elrejtésükhöz kattintson a **Függőség** szűrőre.
* A diagramokban az [**összesített mérőszámok**](app-insights-metrics-explorer.md) (például a kérelmi és hibaarány) jelennek meg. Kattintson valamely diagramra egy további részleteket tartalmazó panel megnyitásához. Kattintson az egyik diagram **Szerkesztés** címkéjére a szűrők és a méret beállításához.

    ![Az összesített mérőszámoknak a portálon elérhető panelje](./media/app-insights-asp-net/metrics.png)

[További tudnivalók az Application Insights használatáról az Azure Portalon](app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>4. lépés: Az alkalmazás közzététele
Tegye közzé alkalmazását az IIS-kiszolgálón vagy az Azure-on. Az [Élő mérőszámok streammel](app-insights-metrics-explorer.md#live-metrics-stream) ellenőrizheti, hogy minden rendben működik-e.

A telemetria az Application Insights portálon épül fel, ahol figyelheti a mérőszámokat, kereshet a telemetriára és [irányítópultokat](app-insights-dashboards.md) állíthat be. Használhatja a nagy teljesítményű [Analytics lekérdezési nyelvet](app-insights-analytics.md) a használat és a teljesítmény elemzéséhez, vagy adott események megtalálásához.

Folytathatja a telemetria elemzését a [Visual Studióban](app-insights-visual-studio.md) olyan eszközökkel, mint például a diagnosztikai keresés és a [trendek](app-insights-visual-studio-trends.md).

> [!NOTE]
> Ha az alkalmazása elég telemetriát küld a [szabályozási korlát](app-insights-pricing.md#limits-summary) eléréséhez, az automatikus [mintavételezés](app-insights-sampling.md) bekapcsol. A mintavételezés csökkenti az alkalmazásból küldött telemetria mennyiségét, míg a korrelatív adatokat diagnosztikai célból megőrzi.
>
>

## <a name="a-namelanda-what-does-the-add-application-insights-command-do"></a><a name="land"></a> Mire használható az Application Insights hozzáadása parancs?
Az Application Insights telemetriát küld az alkalmazástól az Application Insights portálra (amely az Azure-ban található).

![A telemetria mozgásának ábrája](./media/app-insights-asp-net/01-scheme.png)

Így a parancs három dolgot tesz:

1. Hozzáadja az Application Insights Web SDK NuGet-csomagot a projekthez. A Visual Studióban való megtekintéshez kattintson a jobb gombbal a projektjére, és válassza a **Manage NuGet Packages (NuGet-csomagok kezelése)** lehetőséget.
2. Létrehoz egy Application Insights-erőforrást [az Azure Portalon](https://portal.azure.com/). Itt láthatja az adatokat. Lekéri a *kialakítási kulcsot*, amely azonosítja az erőforrást.
3. Beilleszti a kialakítási kulcsot az `ApplicationInsights.config` fájlba, hogy az SDK telemetriát küldhessen a portálra.

Ha szeretné, kézzel is elvégezheti ezeket a lépéseket az [ASP.NET 4](app-insights-windows-services.md) vagy az [ASP.NET-mag](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started) esetében.

### <a name="upgrade-to-future-sdk-versions"></a>Frissítés a jövőbeli SDK-verziókra
Ha frissíteni szeretne egy [új SDK-kiadásra](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), nyissa meg ismét a **NuGet-csomagkezelőt**, és szűréssel keresse meg a telepített csomagokat. Jelölje ki a **Microsoft.ApplicationInsights.Web** lehetőséget, és válassza a **Frissítés** elemet.

Ha az ApplicationInsights.config fájlt testreszabta, mentse el egy példányát a frissítés előtt. Ezután egyesítse a módosításait az új verzióval.

## <a name="add-more-telemetry"></a>További telemetriai funkciók hozzáadása
Az alábbiakban az egyéb hozzáadható telemetriatípusok láthatók.
### <a name="dependencies-exceptions-and-performance-counters"></a>Függőségek, kivételek és teljesítményszámlálók

[Telepítse az állapotfigyelőt](http://go.microsoft.com/fwlink/?LinkId=506648) az összes IIS-kiszolgálón, hogy további telemetriai adatokat gyűjthessen webalkalmazásáról. Ha már telepítve van, semmit sem kell tennie. (Előfordulhat, hogy már használta az állapotfigyelőt egy alkalmazás futásidőben történő megfigyeléséhez.)

Azzal, hogy a felépítés során alkalmazott SDK mellett állapotfigyelőt is használ, jóval összetettebb telemetriai adatokat gyűjthet, amelyek a következőket tartalmazzák:

* [Teljesítményszámlálók](app-insights-performance-counters.md): az alkalmazással kapcsolatos CPU-, memória-, lemez- és más teljesítményszámlálók.
* [Kivételek](app-insights-asp-net-exceptions.md): részletesebb telemetria bizonyos kivételekről.
* [Függőségek](app-insights-asp-net-dependencies.md): például a visszatérítési értékek.

### <a name="webpages-and-single-page-apps"></a>Weboldalak és egylapos alkalmazások
1. [Adja hozzá a JavaScript-kódrészletet](app-insights-javascript.md) weboldalaihoz az oldalmegtekintésekkel, betöltési időkkel, böngészőbeli kivételekkel, az AJAX-hívások teljesítményével, valamint a felhasználók és munkamenetek számával kapcsolatos adatok megjelenítéséhez. Ezen adatok a Böngésző és a Felhasználás panelen jelennek meg.
2. [Kódoljon egyéni eseményeket](app-insights-api-custom-events-metrics.md) a felhasználói műveletek számlálásához, időzítéséhez és méréséhez.


### <a name="diagnostic-code"></a>Diagnosztikai kód
Problémát tapasztalt? Ha a könnyebb diagnosztizálás érdekében kódot helyezne alkalmazásába, több lehetősége van:

* [Naplókivonatok rögzítése](app-insights-asp-net-trace-logs.md): Ha már a Log4N, az NLog vagy a System.Diagnostics.Trace keretrendszert használja a nyomkövetési események naplózásához, a kimenet elküldhető az Application Insightsba. A kimenetet összehasonlíthatja a kérésekkel, kereshet benne, és elemezheti is.
* [Egyéni események és a metrikák](app-insights-api-custom-events-metrics.md): Használja a TrackEvent() és a TrackMetric() függvényt a kiszolgáló- vagy weblapkódban.
* [A telemetriai adatok címkézése további tulajdonságokkal](app-insights-api-filtering-sampling.md#add-properties).

Használja a [Keresés](app-insights-diagnostic-search.md) funkciót az egyes események összehasonlításához, az [Analitika](app-insights-analytics.md) funkciót pedig hathatósabb lekérdezésék végrehajtásához.

## <a name="alerts"></a>Riasztások
Legyen az első, aki értesül arról, hogy alkalmazásának problémája van.

* [Rendelkezésre állási tesztek](app-insights-monitor-web-app-availability.md): Hozzon létre teszteket, hogy megbizonyosodjon róla, oldala látható a weben.
* [Intelligens diagnosztika](app-insights-proactive-diagnostics.md): Ezek a tesztek automatikusan futnak, a beállításukhoz semmit sem kell tennie. Értesítést kap, ha az alkalmazásában szokatlanul magas a meghiúsult kérelmek száma.
* [Metrikariasztások](app-insights-alerts.md): Állítsa be ezeket, hogy figyelmeztetést kapjon, ha egy metrika átlépi a küszöbértéket. Az alkalmazás kódjába beépített egyedi metrikákhoz is állíthat be riasztásokat.

Alapértelmezés szerint a riasztási értesítéseket az Azure-előfizetés tulajdonosa kapja meg.

![Példa riasztási e-mail képernyőképe](./media/app-insights-asp-net/alert-email.png)

## <a name="version-and-release-tracking"></a>Verzió- és kiadáskövetés
Az alkalmazásverzió nyomon követéséhez győződjön meg arról, hogy a Microsoft Build Engine folyamat létrehozza a `buildinfo.config` fájlt. A .csproj fájlban adja hozzá a következőt:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Ha megkapja a verzióinformációkat, az Application Insights webmodul automatikusan hozzáadja az **Alkalmazás verzióját** tulajdonságként a telemetria minden eleméhez. Ez lehetővé teszi a verziók szerinti szűrést, amikor [diagnosztikai kereséseket](app-insights-diagnostic-search.md) végez, illetve [metrikákat vizsgál](app-insights-metrics-explorer.md).

Ne feledje azonban, hogy a buildverzió számát csak a Microsoft Build Engine hozza létre, a Visual Studio fejlesztői buildje nem.

### <a name="release-annotations"></a>Kiadási jegyzetek
Ha a Visual Studio Team Servicest használja, egy új verzió kibocsátásakor diagramjaihoz [kiadási jelölőt](app-insights-annotations.md) adhat hozzá. Az alábbi képen látható, hogy jelenik meg a jelölő.

![Diagramon található példa kiadási jegyzet képernyőképe](./media/app-insights-asp-net/release-annotation.png)

## <a name="next-steps"></a>Következő lépések
**[Az Application Insights használata a Visual Studióban](app-insights-visual-studio.md)**<br/>A telemetriával végzett hibakereséssel, diagnosztikai kereséssel és a kódig való részletezés lefúrással kapcsolatos információkat tartalmaz.

**[Az Application Insights-portál használata](app-insights-dashboards.md)**<br/> Az irányítópultokkal, a hatékony diagnosztikai és elemzési eszközökkel, riasztásokkal, az alkalmazás élő függőségi térképével, valamint a telemetria exportálásával kapcsolatos információkat tartalmaz.

