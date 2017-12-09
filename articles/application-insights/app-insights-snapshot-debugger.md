---
title: "Az Azure Application Insights pillanatkép-hibakereső .NET-alkalmazások |} Microsoft Docs"
description: "Debug pillanatképek vannak automatikusan gyűjti a program kivételek éles .NET-alkalmazásokban"
services: application-insights
documentationcenter: 
author: pharring
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: mbullwin
ms.openlocfilehash: 2f1f9f306d7759cbd1202c985da27a2a3b879ebd
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>A .NET-alkalmazásokban kivételek pillanatképek hibakeresése

Ha kivétel lép, a működés közbeni webes alkalmazás automatikusan begyűjtheti a hibakeresési pillanatképet. A pillanatkép a kivétel időpontjában forráskódját és a változók állapotát mutatja. A pillanatkép-hibakereső (előzetes verzió) a [Azure Application Insights](app-insights-overview.md) kivételtelemetria a webes alkalmazás figyeli. A pillanatképek a felső értesítő kivételek a összegyűjti az, hogy éles környezetben problémák felderítéséhez szükséges információk. Tartalmazza a [pillanatkép adatgyűjtő NuGet-csomag](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) az alkalmazásban, és szükség esetén adja meg a gyűjtemény paramétereit [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). A pillanatképek jelennek meg [kivételek](app-insights-asp-net-exceptions.md) az Application Insights portálon.

A portálon, hogy a hívási verem, és vizsgálja meg a változókat, minden egyes Hívásiverem-keret hibakeresési pillanatképek tekintheti meg. Ahhoz, hogy egy sokkal hatékonyabb hibakeresési élmény a forráskódot, nyissa meg a pillanatképek a Visual Studio 2017 vállalati által [a pillanatkép-hibakereső bővítmény letöltése a Visual Studio](https://aka.ms/snapshotdebugger). A Visual Studio is [állítsa be az interaktív pillanatfelvételeket Snappoints](https://aka.ms/snappoint) kivétel várakozás nélkül.

Pillanatkép gyűjtemény érhető el:
* .NET-keretrendszer és az ASP.NET alkalmazások futó .NET-keretrendszer 4.5-ös vagy újabb.
* Windows rendszeren futó alkalmazások .NET core 2.0 és 2.0-s ASP.NET Core.

A következő környezetekben támogatottak:
* Az Azure App Service.
* Az Azure felhőalapú szolgáltatás operációsrendszer-család fut, 4 vagy újabb verzió.
* Azure Service Fabric-szolgáltatások a Windows Server 2012 R2 vagy újabb rendszerű.
* Azure virtuális gépeken futó Windows Server 2012 R2 vagy újabb.
* Helyi fizikai vagy virtuális gépek Windows Server 2012 R2 rendszerű vagy újabb.

> [!NOTE]
> Ügyfélalkalmazások (például WPF, a Windows Forms vagy UWP) nem támogatottak.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Pillanatkép gyűjtemény az ASP.NET-alkalmazások konfigurálása

1. [Az Application Insights engedélyezéséhez a web app alkalmazásban](app-insights-asp-net.md), ha még nincs kész.

2. Tartalmazza a [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomag az alkalmazásban. 

3. Tekintse át az alapértelmezett beállításokat, a csomag hozzáadott [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in one minute. -->
        <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. A pillanatképek összegyűjtése csak a kivételek az Application Insights jelentett. Bizonyos esetekben (például a .NET platformon régebbi verzióknál), akkor módosítania [kivétel gyűjtemény konfigurálása](app-insights-asp-net-exceptions.md#exceptions) kivételeket pillanatfelvételekkel a portálon.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Az ASP.NET Core 2.0 alkalmazások pillanatkép-gyűjtésének konfigurálása

1. [Az Application Insights engedélyezése az ASP.NET Core web app alkalmazásban](app-insights-asp-net-core.md), ha még nincs kész.

> [!NOTE]
> Arról, hogy az alkalmazás hivatkozik 2.1.1 verzió vagy újabb, a Microsoft.ApplicationInsights.AspNetCore csomag lehet.

2. Tartalmazza a [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomag az alkalmazásban.

3. Módosítsa az alkalmazás `Startup` osztály hozzáadása és konfigurálása a pillanatkép-gyűjtő telemetriai processzor.

   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   ...
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }

       public Startup(IConfiguration configuration) => Configuration = configuration;

       public IConfiguration Configuration { get; }

       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. Konfigurálja a pillanatkép-gyűjtő appsettings.json SnapshotCollectorConfiguration szakasz hozzáadásával. Példa:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": true
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Pillanatkép gyűjtemény más .NET-alkalmazások konfigurálása

1. Ha az alkalmazás már nem tagolva az Application insights szolgáltatással, Kezdésként [Application Insights engedélyezése és a rendszerállapot-kulcs](app-insights-windows-desktop.md).

2. Adja hozzá a [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomag az alkalmazásban.

3. A pillanatképek összegyűjtése csak a kivételek az Application Insights jelentett. Szükség lehet a jelentés azokat a kód módosítása. A kivételkezelő kód függ az alkalmazás szerkezete, de például nem éri el:
    ```C#
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```
    
## <a name="grant-permissions"></a>Engedélyek

Az Azure-előfizetés tulajdonosainak pillanatképek vizsgálhatja meg. Más felhasználók tulajdonos engedéllyel kell rendelkezni.

Adja meg az engedélyt, rendelje hozzá a `Application Insights Snapshot Debugger` a felhasználók számára, akik vizsgálja a pillanatképek szerepkör. Egyes felhasználók vagy csoportok által a cél Application Insights-erőforrás előfizetésnél tulajdonos vagy a erőforráscsoportba vagy előfizetésbe ehhez a szerepkörhöz is hozzárendelhető.

1. A hozzáférés-vezérlés (IAM) panel megnyitásához.
1. Kattintson a + Hozzáadás gombra.
1. Application Insights pillanatkép hibakereső kiválasztása a szerepkörök legördülő listából.
1. Keresse meg és írja be a hozzáadni kívánt felhasználó nevét.
1. A Mentés gombra kattintva adja hozzá a felhasználót a szerepkörhöz.


[!IMPORTANT]
    A pillanatképek potenciálisan tartalmazó változó és a paraméter értékét a személyes és más bizalmas adatokat.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Az Application Insights portálon pillanatképek hibakeresése

Pillanatkép érhető el egy adott kivétel vagy probléma azonosítója, ha egy **Debug pillanatfelvétel megnyitása** gomb megjelenik a [kivétel](app-insights-asp-net-exceptions.md) az Application Insights portálon.

![Nyissa meg Debug pillanatkép gombra a kivételei](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

A hibakeresési pillanatkép nézetben láthatja a hívási verem és a változók ablaktábla. A hívási verem keretek a hívási verem ablaktáblán, megtekintheti a helyi változókat és paraméterek függvény hívása a Változók panelen.

![Nézet Debug pillanatkép a portálon](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

A pillanatképek kényes információt is tartalmazhat, és alapértelmezés szerint nincsenek megtekinthető. A pillanatképek megtekintéséhez rendelkeznie kell a `Application Insights Snapshot Debugger` Önhöz rendelt szerepkör.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>A Visual Studio 2017 vállalati pillanatképek hibakeresése
1. Kattintson a **letöltése pillanatkép** gombra kattintva töltse le a `.diagsession` fájlhoz, amely a Visual Studio 2017 Enterprise nyithatja meg. 

2. Lehetőségre a `.diagsession` fájl, először [töltse le és telepítse a pillanatkép-hibakereső bővítményt a Visual Studio](https://aka.ms/snapshotdebugger).

3. A pillanatfelvétel-fájl megnyitása után a Visual Studio kis memóriakép Hibakeresés lap jelenik meg. Kattintson a **felügyelt kód Debug** a hibakeresés a pillanatkép. Ha a kivétel lépett fel, hogy a folyamat az aktuális állapotát is hibakeresése kódsort a pillanatkép nyílik meg.

    ![A Visual Studio nézet hibakeresési pillanatkép](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

A letöltött pillanatkép bármely alkalmazás webkiszolgálón található szimbólum fájlokat tartalmazza. A szimbólum fájlok rendelje hozzá a pillanatkép-adatok forráskód szükségesek. App Service-alkalmazásokhoz végezze el a szimbólum központi telepítés engedélyezése a webalkalmazások közzétételekor.

## <a name="how-snapshots-work"></a>A pillanatképek működése

Az alkalmazás indításakor egy külön pillanatkép feltöltése folyamat jön létre, amely az alkalmazás a pillanatkép-kéréseket figyeli. Pillanatkép van szükség, ha a futó folyamattal árnyékmásolatát körülbelül 10-20 ezredmásodpercben történik. Az árnyékmásolat-folyamat majd elemzésnek, és egy pillanatkép jön létre, amíg a fő folyamat tovább fut és forgalmat szolgálja ki a felhasználók számára. A pillanatkép majd tölt fel az Application insights szolgáltatással együtt megfelelő szimbólum (.pdb) fájlok, amelyek szükségesek ahhoz, hogy az adatok megtekintését.

## <a name="current-limitations"></a>Aktuális korlátozások

### <a name="publish-symbols"></a>Szimbólumok közzététele
A pillanatkép-hibakereső van szükség, szimbólumfájlok dekódolni a változók és a Visual Studio hibakeresési érdekében az üzemi kiszolgálón. A Visual Studio 2017 15.2 kiadása kiadás buildek szimbólumait alapértelmezés szerint közzéteszi az App Service teszi közzé. A korábbi verziók, adja hozzá az alábbi sort a közzétételi profilt kell `.pubxml` fájlt úgy, hogy a szimbólumok közzétett kiadási módban:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Azure számítási és más típusú, győződjön meg arról, hogy a szimbólum a fő alkalmazás .dll fájl ugyanabban a mappában (általában `wwwroot/bin`) vagy az aktuális útvonalon érhetők el.

### <a name="optimized-builds"></a>Optimalizált buildek
Bizonyos esetekben helyi változók nem lehet megtekinteni kiadás buildekben létrehozása során alkalmazott optimalizálás miatt.

## <a name="troubleshooting"></a>Hibaelhárítás

Ezek a tippek a pillanatkép-hibakereső kapcsolatos problémák hibaelhárítása érdekében.

### <a name="verify-the-instrumentation-key"></a>Ellenőrizze a rendszerállapot-kulcsot

Győződjön meg arról, hogy a közzétett alkalmazást a megfelelő instrumentation kulcsot használ. Az Application Insights általában a instrumentation kulcs beolvassa az ApplicationInsights.config fájl. Győződjön meg arról, hogy az érték azonos a instrumentation kulcs az Application Insights-erőforrás, melyek megjelennek a portálon.

### <a name="check-the-uploader-logs"></a>Ellenőrizze a feltöltése naplókat

Pillanatkép létrehozása után egy kis memóriakép fájl (.dmp) jön létre a lemezen. Egy külön feltöltése folyamat veszi, hogy kis memóriakép fájl, és feltölti azt, és minden társított PDB-fájlok, az Application Insights pillanatkép hibakereső tárhelyre. Után a kis memóriakép sikeresen fel van töltve, hanem törli a lemezen. A naplófájlokat a kis memóriakép feltöltése megőrzi a lemezen. Egy App Service environment-környezetben található a naplók a `D:\Home\LogFiles\Uploader_*.log`. A Kudu felügyeleti webhely az App Service segítségével ezekben a naplófájlokban található.

1. Nyissa meg az App Service-alkalmazás az Azure portálon.

2. Válassza ki a **speciális eszközök** panelen, vagy keressen a **Kudu**.
3. Kattintson a **Ugrás**.
4. Az a **hibakereső konzol** legördülő listáján jelölje ki **CMD**.
5. Kattintson a **naplófájlok**.

Legalább egy fájlt egy nevű kezdődő kell megjelennie `Uploader_` és egy `.log` bővítmény. Kattintson a megfelelő ikonra, töltse le a naplófájlokat, illetve egy böngészőben nyissa meg ezeket.
A fájl nevét a gép nevét tartalmazza. Az App Service-példány egynél több számítógép üzemelteti, ha nincsenek az egyes gépek külön naplófájlokat. Ha a feltöltése egy új kis memóriakép fájlt észlel, azt a naplófájl rögzíti. Íme egy példa a hibátlan feltöltés:

```
MinidumpUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:08.0349846Z
MinidumpUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 329.12 MB
    DateTime=2017-05-25T14:25:16.0145444Z
MinidumpUploader.exe Information: 0 : Upload successful.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2017-05-25T14:25:44.2310982Z
MinidumpUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2017-05-25T14:25:44.5435948Z
MinidumpUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:44.6095821Z
```

Az előző példában a instrumentation kulcsa `c12a605e73c44346a984e00000000000`. Ezt az értéket meg kell felelnie a instrumentation billentyűt az alkalmazás.
A kis memóriakép társítva a azonosítójú pillanatkép `139e411a23934dc0b9ea08a626db16c5`. Ezt az Azonosítót később segítségével keresse meg a társított kivételtelemetria Application Insights Analytics.

A feltöltése 15 percenként egyszer kapcsolatos új PDB-fájlok keres. Íme egy példa:

```
MinidumpUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\home\site\wwwroot\ for local PDBs.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\local\Temporary ASP.NET Files\root\a6554c94\e3ad6f22\assembly\dl3\81d5008b\00b93cc8_dec5d201 for local PDBs.
    DateTime=2017-05-25T15:11:38.8160276Z
MinidumpUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2017-05-25T15:11:38.8316450Z
MinidumpUploader.exe Information: 0 : Deleted PDB scan marker D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\.pdbscan.
    DateTime=2017-05-25T15:11:38.8316450Z
```

Az alkalmazások, amelyek _nem_ üzemelteti az App Service-ben, a feltöltése feldolgozásra a tömörített memóriaképek ugyanabban a mappában: `%TEMP%\Dumps\<ikey>` (ahol `<ikey>` a rendszerállapot-kulcs).

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Használja az Application Insights keresési pillanatképekkel kivételek kereséséhez

Egy pillanatkép jön létre, amikor a rtesítő kivétel címkéje egy pillanatkép. Ha a kivételtelemetria bejelentések Application Insights, hogy a pillanatkép azonosítója megtalálható egyéni tulajdonság. Az összes telemetriai adat található Application Insights a Search paneljét használ, a `ai.snapshot.id` egyéni tulajdonság.

1. Keresse meg az Application Insights-erőforrást az Azure portálon.
2. Kattintson a **keresési**.
3. Típus `ai.snapshot.id` a keresőmezőben, és nyomja le az ENTER billentyűt.

![Keresse meg a telemetriai adatok egy pillanatkép-azonosító a portálon](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Ha ez a keresés eredménytelen, majd pillanatképek nem jelentette az Application Insights az alkalmazáshoz, a kijelölt időtartományban.

Keresse meg a feltöltése naplókból egy adott pillanatkép-azonosító, a keresési mezőbe írja be azonosító. Ha olyan pillanatképet, amely töltött fel tudja telemetriai adatok nem találhatók, kövesse az alábbi lépéseket:

1. Ellenőrizze, hogy van szüksége, a jobb oldali Application Insights-erőforrás a instrumentation kulcs ellenőrzésével.

2. A feltöltése naplóból időbélyeg használatával, állítsa be úgy a fedik le az adott időtartományt a keresés időtartományát szűrő.

Ha még nem látja a pillanatkép Azonosítóval rendelkező kivételek, a kivételtelemetria Application Insights nem jelzett. Ez a helyzet akkor fordulhat elő, ha az alkalmazás összeomlott követően a pillanatkép által, de a kivételtelemetria jelentette előtt. Ebben az esetben a naplófájlokban App Service alatt `Diagnose and solve problems` megtekintéséhez, hogy voltak-e váratlan újraindítása vagy a nem kezelt kivételeket.

## <a name="next-steps"></a>Következő lépések

* [Állítsa be a snappoints a kódban](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) kivétel várakozás nélkül pillanatképek eléréséhez.
* [Kivételek a webalkalmazások diagnosztizálásához](app-insights-asp-net-exceptions.md) ismerteti, hogyan további kivételek láthatóvá az Application Insights részére. 
* [Észlelési intelligens](app-insights-proactive-diagnostics.md) automatikusan észleli a teljesítményanomáliákat.
