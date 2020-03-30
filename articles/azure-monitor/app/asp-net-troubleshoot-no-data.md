---
title: Adathiány hibaelhárítása – Application Insights .NET-hez
description: Nem látja az adatokat az Azure Application Insightsban? Próbáld meg itt.
ms.topic: conceptual
ms.date: 07/23/2018
ms.openlocfilehash: 2627fde55f4177798d04aab02db169f3117d32dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665901"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>Adatok nélküli hibaelhárítás – Application Insights for .NET/.NET Core

## <a name="some-of-my-telemetry-is-missing"></a>Hiányzik néhány telemetriai adatom
*Az Application Insightsban csak az alkalmazásom által létrehozott események töredékét látom.*

* Ha következetesen ugyanazt a frakciót látja, annak valószínűleg az adaptív [mintavételezésnek](../../azure-monitor/app/sampling.md)köszönhető. Ennek megerősítéséhez nyissa meg a Keresés (az áttekintő panelről) és tekintse meg egy kérelem vagy más esemény egy példányát. A tulajdonságok szakasz alján kattintson a "..." gombra. hogy a teljes ingatlan adatait. Ha a kérelmek száma 1 >, majd a mintavétel működik.
* Ellenkező esetben lehetséges, hogy eléri az [árcsomag adatforgalmi korlátját.](../../azure-monitor/app/pricing.md#limits-summary) Ezek a határértékek percenként érvényesek.

*Véletlenszerűen adatvesztést tapasztalok.*

* Ellenőrizze, hogy adatvesztést tapasztal-e a [Telemetriai csatornában](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost)

* Az ismert problémák ellenőrzése a [GitHub-tárházban](https://github.com/Microsoft/ApplicationInsights-dotnet/issues)

*Adatvesztést tapasztalok a Konzolalkalmazásban vagy a Web Appban, amikor az alkalmazás le áll.*

* Az SDK-csatorna pufferben tartja a telemetriai adatokat, és kötegekben küldi el őket. Ha az alkalmazás leáll, előfordulhat, hogy explicit módon meg kell hívnia a [Flush() .](api-custom-events-metrics.md#flushing-data) A `Flush()` viselkedése a ténylegesen használt [csatornától](telemetry-channels.md#built-in-telemetry-channels) függ.

## <a name="no-data-from-my-server"></a>Nincs adat az én szerver
*Telepítettem az alkalmazást a webkiszolgálómra, és most nem látok belőle telemetriát. Ez munkás RENDBEN VAN -ra az én -m dev gép.*

* Valószínűleg tűzfal probléma. [Tűzfal-kivételek beállítása az Application Insights számára az adatok küldéséhez.](../../azure-monitor/app/ip-addresses.md)
* Előfordulhat, hogy az IIS Server néhány előfeltételből hiányzik: .NET Bővítő 4.5 és ASP.NET 4.5.

*[Telepítettem állapotfigyelő](../../azure-monitor/app/monitor-performance-live-website-now.md) az én webszerver figyelemmel kíséri a meglévő alkalmazásokat. Nem látok semmilyen eredményt.*

* Lásd: [Hibaelhárítási állapotfigyelő](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>Nincs "Application Insights hozzáadása" lehetőség a Visual Studióban
*Amikor a jobb gombbal egy meglévő projektre kattintok a Megoldáskezelőben, nem jelennek meg az Application Insights-beállítások.*

* Az eszközök nem minden típusú .NET projektet támogatnak. A web- és WCF-projektek támogatottak. Más projekttípusok, például asztali vagy szolgáltatási alkalmazások esetén manuálisan is [hozzáadhat Egy Application Insights SDK-t a projekthez.](../../azure-monitor/app/windows-desktop.md)
* Ellenőrizze, hogy rendelkezik-e [a Visual Studio 2013 3-as vagy újabb frissítésével.](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs) Előre telepített fejlesztői elemzési eszközökkel, amelyek biztosítják az Application Insights SDK.It comes pre-installed with Developer Analytics tools, which provide the Application Insights SDK.
* Válassza **az Eszközök**, Bővítmények és frissítések **lehetőséget,** és ellenőrizze, hogy a **Fejlesztői elemzési eszközök** telepítve és engedélyezve vannak-e. Ha igen, kattintson **a Frissítések** gombra, és nézze meg, hogy van-e elérhető frissítés.
* Nyissa meg az Új projekt párbeszédpanelt, és válassza ASP.NET webalkalmazás lehetőséget. Ha ott látja az Application Insights lehetőséget, majd az eszközök telepítve vannak. Ha nem, próbálja meg eltávolítani, majd újratelepíteni a Fejlesztői elemzési eszközöket.

## <a name="adding-application-insights-failed"></a><a name="q02"></a>Az Application Insights hozzáadása nem sikerült
*Amikor megpróbálom hozzáadni az Application Insightsot egy meglévő projekthez, hibaüzenet jelenik meg.*

Valószínű okok:

* Az Application Insights-portállal való kommunikáció nem sikerült; Vagy
* Van egy kis probléma az Azure-fiókjával;
* Csak [ahhoz az előfizetéshez vagy csoporthoz van olvasási hozzáférése, amelyben az új erőforrást megpróbálta létrehozni.](../../azure-monitor/app/resources-roles-access-control.md)

Erősít:

* Ellenőrizze, hogy megadta-e a bejelentkezési hitelesítő adatokat a megfelelő Azure-fiókhoz.
* A böngészőben ellenőrizze, hogy van-e hozzáférése az [Azure Portalhoz.](https://portal.azure.com) Nyissa meg a Beállítások at, és nézze meg, hogy van-e korlátozás.
* [Alkalmazáselemzéshozzáadása a meglévő projekthez:](../../azure-monitor/app/asp-net.md)A Megoldáskezelőben kattintson a jobb gombbal a projektre, és válassza az "Application Insights hozzáadása" parancsot.

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>"Instrumentation key cannot be empty" (Instrumentation key) (Instrumentation key cannot be empty)
Úgy néz ki, mintha valami elromlott, miközben az Application Insights telepítése, vagy talán egy naplózási adapter.

A Megoldáskezelőben kattintson a jobb gombbal a projektre, és válassza **az Application Insights > Az Application Insights konfigurálása parancsot.** Megjelenik egy párbeszédablak, amely meghívja, hogy jelentkezzen be az Azure-ba, és hozzon létre egy Application Insights-erőforrást, vagy használjon fel újra egy meglévőt.

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a>"NuGet csomag(ok) hiányoznak" a build szerver
*Minden épít OK, ha én vagyok hibakeresés az én fejlesztőgép, de kapok egy NuGet hiba a build szerver.*

Kérjük, olvassa el [a NuGet csomag-visszaállítás](https://docs.nuget.org/Consume/Package-Restore) és [az automatikus csomagvisszaállítás .](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore)

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Hiányzó menüparancs az Application Insights visual Studio-ból való megnyitásához
*Amikor a jobb gombbal a projektmegoldás-kezelőre kattintok, nem jelennek meg az Application Insights-parancsok, vagy nem látom az Open Application Insights parancsot.*

Valószínű okok:

* Ha az Application Insights-erőforrást manuálisan hozta létre, vagy ha a projekt olyan típusú, amelyet az Application Insights eszközök nem támogatnak.
* A Fejlesztői elemzési eszközök le vannak tiltva a Visual Studio alkalmazásban.
* A Visual Studio régebbi, mint a 2013-as 3.

Erősít:

* Győződjön meg arról, hogy a Visual Studio 2013-as 3-as vagy újabb verziója.
* Válassza **az Eszközök**, Bővítmények és frissítések **lehetőséget,** és ellenőrizze, hogy a Developer **Analytics-eszközök** telepítve és engedélyezve vannak-e. Ha igen, kattintson **a Frissítések** gombra, és nézze meg, hogy van-e elérhető frissítés.
* Kattintson a jobb gombbal a projektre a Megoldáskezelőben. Ha az **Application Insights > Az Application Insights konfigurálása**parancsot látja, használja azt a projektet az Application Insights szolgáltatás erőforrásához való csatlakoztatásához.

Ellenkező esetben a fejlesztői elemzési eszközök nem támogatják közvetlenül a projekt típusát. A telemetriai adatok megtekintéséhez jelentkezzen be az [Azure Portalon,](https://portal.azure.com)válassza az Application Insights a bal oldali navigációs sávon, és válassza ki az alkalmazást.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Hozzáférés megtagadva" az Application Insights Visual Studio-ból való megnyitásakor
*Az "Application Insights megnyitása" menü parancs az Azure Portalra vezet, de "hozzáférés megtagadva" hibaüzenet jelenik meg.*

Az alapértelmezett böngészőben utoljára használt Microsoft-bejelentkezés nem fér hozzá [az alkalmazáshoz való hozzátöltéskor létrehozott erőforráshoz.](../../azure-monitor/app/asp-net.md) Két oka lehet:

* Több Microsoft-fiókkal is rendelkezik – talán egy munkahelyi és egy személyes Microsoft-fiókkal? Az alapértelmezett böngészőben utoljára használt bejelentkezés egy másik fiókhoz volt, mint amely hozzáféréssel rendelkezik [az Application Insights projekthez való hozzáadásához.](../../azure-monitor/app/asp-net.md)
  * Javítás: Kattintson a nevére a böngészőablak jobb felső részén, és jelentkezzen ki. Ezután jelentkezzen be a hozzáféréssel rendelkező fiókkal. Ezután a bal oldali navigációs sávon kattintson az Application Insights elemre, és válassza ki az alkalmazást.
* Valaki más hozzáadta az Application Insights-ot a projekthez, és elfelejtette megadni [a hozzáférést ahhoz az erőforráscsoporthoz,](../../azure-monitor/app/resources-roles-access-control.md) amelyben létrehozták.
  * Javítás: Ha szervezeti fiókot használtak, hozzáadhatnak a csapathoz; vagy egyéni hozzáférést adhatnak az erőforráscsoporthoz.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>"Az eszköz nem található" az Application Insights Visual Studio-ból való megnyitásakor
*Az "Application Insights megnyitása" menü parancs az Azure Portalra vezet, de "az eszköz nem található" hibaüzenet jelenik meg.*

Valószínű okok:

* Az Application Insights-erőforrás törölve lett; Vagy
* A instrumentation kulcs be van állítva, vagy megváltozott ApplicationInsights.config szerkesztésével közvetlenül, frissítése nélkül a projekt fájlt.

Az ApplicationInsights.config instrumentation kulcsa szabályozza, hogy a telemetriai adatokat a rendszer hova küldi. A projektfájl egyik sora határozza meg, hogy a Visual Studio parancsának használatakor melyik erőforrás nyílik meg.

Erősít:

* A Megoldáskezelőben kattintson a jobb gombbal a projektre, és válassza az Application Insights parancsot, az Application Insights konfigurálása parancsra. A párbeszédpanelen választhatja a telemetriai adatok elküldésének módját egy meglévő erőforrásnak, vagy létrehozhat egy újat. Vagy:
* Nyissa meg közvetlenül az erőforrást. Jelentkezzen be [az Azure Portalon,](https://portal.azure.com)kattintson az Application Insights elemre a bal oldali navigációs sávon, majd válassza ki az alkalmazást.

## <a name="where-do-i-find-my-telemetry"></a>Hol találom a telemetriai adatokat?
*Bejelentkeztem a [Microsoft Azure portálra](https://portal.azure.com), és az Azure otthoni irányítópultját nézem. Így hol találom az Application Insights-adatokat?*

* A bal oldali navigációs sávon kattintson az Application Insights elemre, majd az alkalmazás nevére. Ha nincs ott projektje, hozzá kell [adnia vagy konfigurálnia kell az Application Insights ot a webes projektben.](../../azure-monitor/app/asp-net.md)  
  Itt látni fog néhány összefoglaló diagramot. A további részletek megtekintéséhez kattintson rájuk.
* A Visual Studio-ban az alkalmazás hibakeresése közben kattintson az Application Insights gombra.

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a>Nincsenek kiszolgálói adatok (vagy egyáltalán nincs adat)
*Futtattam az alkalmazást, majd megnyitottam az Application Insights szolgáltatást a Microsoft Azure-ban, de az összes diagramon az látható, hogy "Ismerje meg, hogyan gyűjthet..." vagy "Nincs beállítva".* Vagy *csak az Oldalnézet és a felhasználói adatok, kiszolgálóadatok nélkül.*

* Az alkalmazás futtatása hibakeresési módban a Visual Studio (F5) alkalmazásban. Használja az alkalmazást, hogy hozzon létre néhány telemetriai adatokat. Ellenőrizze, hogy a Visual Studio kimeneti ablakában bejelentkezett események láthatók-e.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* Az Application Insights portálon nyissa meg a [Diagnosztikai keresés .](../../azure-monitor/app/diagnostic-search.md) Az adatok általában itt jelennek meg először.
* Kattintson a Frissítés gombra. A penge rendszeresen frissíti magát, de manuálisan is megteheti. A frissítési időköz hosszabb a nagyobb időtartományok esetében.
* Ellenőrizze, hogy a műszerkulcsok egyeznek-e. Az alkalmazás fő paneljén az Application Insights portálon, az **Essentials** legördülő menüben tekintse meg **a Instrumentation kulcsot.** Ezután a Visual Studio projektjében nyissa meg az `<instrumentationkey>`ApplicationInsights.config webhelyet, és keresse meg a . Ellenőrizze, hogy a két billentyű egyenlő-e. Ha nem:  
  * A portálon kattintson az Application Insights elemre, és keresse meg a megfelelő kulccsal rendelkező alkalmazás-erőforrást; Vagy
  * A Visual Studio Solution Explorer alkalmazásban kattintson a jobb gombbal a projektre, és válassza az Application Insights, Configure parancsot. Állítsa alaphelyzetbe az alkalmazást, hogy telemetriát küldjön a megfelelő erőforrásnak.
  * Ha nem találja a megfelelő kulcsokat, ellenőrizze, hogy ugyanazt a bejelentkezési hitelesítő adatokat használja-e a Visual Studióban, mint a portálon.
* A [Microsoft Azure otthoni irányítópultján](https://portal.azure.com)tekintse meg a Szolgáltatásállapot-térképet. Ha vannak riasztási jelzések, várja meg, amíg azok visszatértek az OK gombra, majd zárja be, majd nyissa meg újra az Application Insights alkalmazás panel.
* Ellenőrizze még [a status blog](https://blogs.msdn.microsoft.com/servicemap-status/).
* Írt olyan kódot a [kiszolgálóoldali SDK-hoz,](../../azure-monitor/app/api-custom-events-metrics.md) amely megváltoztathatja a `TelemetryContext`műszerezési kulcsot példányokban `TelemetryClient` vagy ? Vagy írt egy [szűrőt vagy mintavételi konfigurációt,](../../azure-monitor/app/api-filtering-sampling.md) amely túl sokat szűr?
* Ha szerkesztette az ApplicationInsights.config fájlt, gondosan ellenőrizze a [TelemettryInitializers és a TelemettryProcessors konfigurációját.](../../azure-monitor/app/api-filtering-sampling.md) Egy helytelenül elnevezett típus vagy paraméter adatküldéséhez az SDK nem küld adatokat.

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>Nincs adat az oldalmegtekintésekről, a böngészőkről, a használatról
*Adatok jelennek meg a kiszolgálói válaszidő és a kiszolgálói kérelmek diagramokon, de az oldalnézet betöltési ideje, illetve a Böngésző vagy a Használat panelen nem jelennek meg adatok.*

Az adatok a weboldalak parancsfájljaiból származnak. 

* Ha egy meglévő webes projekthez adott hozzá Application Insights-ot, [kézzel kell hozzáadnia a parancsfájlokat.](../../azure-monitor/app/javascript.md)
* Ellenőrizze, hogy az Internet Explorer nem jeleníti-e meg webhelyét kompatibilitási módban.
* Használja a böngésző hibakeresési funkcióját (F12 egyes böngészőkben, majd válassza a `dc.services.visualstudio.com`Hálózat lehetőséget) annak ellenőrzéséhez, hogy az adatokat a rendszer elküldi-e a rendszer.

## <a name="no-dependency-or-exception-data"></a>Nincsfüggőségvagy kivételadat
Lásd: [függőségi telemetriai adatok](../../azure-monitor/app/asp-net-dependencies.md) és [kivételtelemetria.](asp-net-exceptions.md)

## <a name="no-performance-data"></a>Nincsenek teljesítményadatok
A teljesítményadatok (CPU, IO-sebesség és így tovább) java [webszolgáltatásokhoz,](../../azure-monitor/app/java-collectd.md) [windowsos asztali alkalmazásokhoz,](../../azure-monitor/app/windows-desktop.md) [IIS-webalkalmazásokhoz és -szolgáltatásokhoz](../../azure-monitor/app/monitor-performance-live-website-now.md)érhetők el az állapotfigyelő telepítésekor és az [Azure Cloud Services szolgáltatáshoz.](../../azure-monitor/app/app-insights-overview.md) megtalálja a Beállítások, Szerverek.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Nincs (szerver) adat, mióta közzétettem az alkalmazást a kiszolgálómon
* Ellenőrizze, hogy valóban másolta-e az összes Microsoftot. ApplicationInsights DLL-ek a kiszolgálóra a Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll-lel együtt
* Előfordulhat, hogy a tűzfalon meg kell [nyitnia néhány TCP-portot.](../../azure-monitor/app/ip-addresses.md)
* Ha a vállalati hálózatról való kiküldéshez proxyt kell használnia, állítsa be a [defaultProxy-t](https://msdn.microsoft.com/library/aa903360.aspx) a Web.config fájlban
* Windows Server 2008: Győződjön meg arról, hogy telepítette a következő frissítéseket: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Régebben láttam az adatokat, de leállt
* Ellenőrizze az [állapot blog](https://blogs.msdn.com/b/applicationinsights-status/).
* Elérte az adatpontok havi kvótáját? A beállítások/kvóta és az árak megnyitásával megtudhatja. Ha igen, frissítheti a csomagot, vagy fizethet a további kapacitásért. Tekintse meg az [árképzési rendszert](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Nem látom az összes várt adatot
Ha az alkalmazás sok adatot küld, és az Application Insights SDK-t használja ASP.NET 2.0.0-beta3 vagy újabb verziójához, az [adaptív mintavételezési](../../azure-monitor/app/sampling.md) szolgáltatás működhet, és csak a telemetriai adatok egy százalékát küldheti el.

Letilthatja, de ez nem ajánlott. A mintavételezés úgy van kialakítva, hogy a kapcsolódó telemetriai adatok diagnosztikai célokra megfelelően továbbítódnak.

## <a name="client-ip-address-is-0000"></a>Az ügyfél IP-címe 0.0.0

2018. február 5-én bejelentettük, hogy eltávolítottuk az Ügyfél IP-címének naplózását. Ez nincs hatással a földrajzi helyre.

> [!NOTE]
> Ha az IP-cím első 3 oktettére van szüksége, [telemetriai inicializáló](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer) használatával egyéni attribútumot adhat hozzá.
> Ez nincs hatással a 2018. február 5. előtt összegyűjtött adatokra.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Hibás földrajzi adatok a felhasználói telemetriai adatokban
A város, a régió és az ország dimenziói IP-címekből származnak, és nem mindig pontosak. Ezeket az IP-címeket a program először a helyhez dolgozza fel, majd 0.0.0.0-ra módosítja a tároláshoz.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>„A metódus nem található” kivétel az Azure Cloud Servicesben futó rendszeren
A .NET 4.6-os verziójára készítette el az alkalmazást? Az Azure Cloud Services szerepkörei nem támogatják automatikusan a 4.6-os verziót. [Telepítse a 4.6-os verziót mindegyik szerepkörön](../../cloud-services/cloud-services-dotnet-install-dotnet.md), mielőtt futtatná az alkalmazást.

## <a name="troubleshooting-logs"></a>Naplók – hibaelhárítás

Kövesse ezeket az utasításokat a keretrendszer hibaelhárítási naplóinak rögzítéséhez.

### <a name="net-framework"></a>.NET-keretrendszer

1. Telepítse a [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) csomagot a NuGet-ből. A telepített verziónak meg kell egyeznie a telepített verzióval.`Microsoft.ApplicationInsighs`

2. Módosítsa az applicationinsights.config fájlt úgy, hogy az tartalmazza a következőket:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Az alkalmazásnak írási engedéllyel kell rendelkeznie a konfigurált helyre

3. Indítsa újra a folyamatot, hogy ezeket az új beállításokat az SDK

4. Ha végzett, visszaállítja ezeket a módosításokat.

### <a name="net-core"></a>.NET Core

1. Telepítse a [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) csomagot a NuGet-ből. A telepített verziónak meg kell egyeznie a telepített verzióval.`Microsoft.ApplicationInsights`

A Microsoft.ApplicationInsights.AspNetCore legújabb verziója a 2.8.2, és a Microsoft.ApplicationInsights 2.11.2-es verziójára hivatkozik. Ezért a Microsoft.AspNet.ApplicationInsights.HostingStartup telepítendő verziójának 2.11.2-nek kell lennie.

2. Módosítsa `ConfigureServices` a `Startup.cs` metódust az osztályában.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Az alkalmazásnak írási engedéllyel kell rendelkeznie a konfigurált helyre

3. Indítsa újra a folyamatot, hogy ezeket az új beállításokat az SDK

4. Ha végzett, visszaállítja ezeket a módosításokat.


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a>Naplók gyűjtése a PerfView segítségével
[A PerfView](https://github.com/Microsoft/perfview) egy ingyenes diagnosztikai és teljesítményelemző eszköz, amely számos forrásból származó diagnosztikai információk gyűjtésével és megjelenítésével segít elkülöníteni a CPU-t, a memóriát és más problémákat.

Az Application Insights SDK napló EventSource önhibaelhárítási naplók, amelyek a PerfView által rögzíthető.

A naplók gyűjtéséhez töltse le a PerfView letöltését, és futtassa a parancsot:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Szükség szerint módosíthatja ezeket a paramétereket:
- **MaxCollectSec**. Állítsa be ezt a paramétert, hogy a PerfView ne fusson korlátlan ideig, és befolyásolja a kiszolgáló teljesítményét.
- **Csakszolgáltatók .** Állítsa be ezt a paramétert úgy, hogy csak az SDK-ból gyűjtsön naplókat. Ezt a listát az adott vizsgálatok alapján testreszabhatja. 
- **NoGui.** Állítsa be ezt a paramétert, hogy a Gui nélkül gyűjtse a naplókat.


További információk:
- [Teljesítmény-nyomkövetések rögzítése a PerfView segítségével.](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView)
- [Az Application Insights eseményforrásai](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/ETW)

## <a name="still-not-working"></a>Még mindig nem működik...
* [Az Application Insights fóruma](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
