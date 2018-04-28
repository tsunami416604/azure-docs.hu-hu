---
title: Az Azure Application Insights pillanatkép-hibakereső .NET-alkalmazások |} Microsoft Docs
description: Debug pillanatképek vannak automatikusan gyűjti a program kivételek éles .NET-alkalmazásokban
services: application-insights
documentationcenter: ''
author: pharring
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: mbullwin
ms.openlocfilehash: 0ba58f1384d7c93af30f9b175a5a154811c9a1e0
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>A .NET-alkalmazásokban kivételek pillanatképek hibakeresése

Ha kivétel lép, a működés közbeni webes alkalmazás automatikusan begyűjtheti a hibakeresési pillanatképet. A pillanatkép a kivétel időpontjában forráskódját és a változók állapotát mutatja. A pillanatkép-hibakereső (előzetes verzió) a [Azure Application Insights](app-insights-overview.md) kivételtelemetria a webes alkalmazás figyeli. A pillanatképek a felső értesítő kivételek a összegyűjti az, hogy éles környezetben problémák felderítéséhez szükséges információk. Tartalmazza a [pillanatkép adatgyűjtő NuGet-csomag](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) az alkalmazásban, és szükség esetén adja meg a gyűjtemény paramétereit [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). A pillanatképek jelennek meg [kivételek](app-insights-asp-net-exceptions.md) az Application Insights portálon.

A portálon a hibakeresési pillanatfelvételeket megtekintve láthatja a hívásvermet és megvizsgálhatja a változókat az egyes hívásveremkeretekre vonatkozóan. Ahhoz, hogy egy sokkal hatékonyabb hibakeresési élmény a forráskódot, nyissa meg a pillanatképek a Visual Studio 2017 vállalati által [a pillanatkép-hibakereső bővítmény letöltése a Visual Studio](https://aka.ms/snapshotdebugger). A Visual Studio is [állítsa be az interaktív pillanatfelvételeket Snappoints](https://aka.ms/snappoint) kivétel várakozás nélkül.

Pillanatkép gyűjtemény érhető el:
* .NET-keretrendszer és az ASP.NET alkalmazások futó .NET-keretrendszer 4.5-ös vagy újabb.
* Windows rendszeren futó alkalmazások .NET core 2.0 és 2.0-s ASP.NET Core.

A következő környezetekben támogatottak:
* Azure App Service.
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
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>24:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>1</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
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

    Adja hozzá a következő using utasításokat a `Startup.cs`

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Adja hozzá a következő `SnapshotCollectorTelemetryProcessorFactory` az osztály `Startup` osztály.

   ```csharp
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
       ...
    ```
    Adja hozzá a `SnapshotCollectorConfiguration` és `SnapshotCollectorTelemetryProcessorFactory` az indítási folyamat szolgáltatások:

    ```csharp
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
       "IsEnabledInDeveloperMode": true,
       "ThresholdForSnapshotting": 5,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"24:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Pillanatkép gyűjtemény más .NET-alkalmazások konfigurálása

1. Ha az alkalmazás már nem tagolva az Application insights szolgáltatással, Kezdésként [Application Insights engedélyezése és a rendszerállapot-kulcs](app-insights-windows-desktop.md).

2. Adja hozzá a [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomag az alkalmazásban.

3. A pillanatképek összegyűjtése csak a kivételek az Application Insights jelentett. Szükség lehet a jelentés azokat a kód módosítása. A kivételkezelő kód függ az alkalmazás szerkezete, de például nem éri el:
    ```csharp
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

## <a name="grant-permissions"></a>Engedélyek megadása

Az Azure-előfizetés tulajdonosainak pillanatképek vizsgálhatja meg. Más felhasználók tulajdonos engedéllyel kell rendelkezni.

Adja meg az engedélyt, rendelje hozzá a `Application Insights Snapshot Debugger` a felhasználók számára, akik vizsgálja a pillanatképek szerepkör. Egyes felhasználók vagy csoportok által a cél Application Insights-erőforrás előfizetésnél tulajdonos vagy a erőforráscsoportba vagy előfizetésbe ehhez a szerepkörhöz is hozzárendelhető.

1. A hozzáférés-vezérlés (IAM) panel megnyitásához.
1. Kattintson a + Hozzáadás gombra.
1. Application Insights pillanatkép hibakereső kiválasztása a szerepkörök legördülő listából.
1. Keresse meg és írja be a hozzáadni kívánt felhasználó nevét.
1. A Mentés gombra kattintva adja hozzá a felhasználót a szerepkörhöz.


> [!IMPORTANT]
> A pillanatképek potenciálisan tartalmazó változó és a paraméter értékét a személyes és más bizalmas adatokat.

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

Pillanatkép létrehozása után egy kis memóriakép fájl (.dmp) jön létre a lemezen. Egy külön feltöltése folyamat veszi, hogy kis memóriakép fájl, és feltölti azt, és minden társított PDB-fájlok, az Application Insights pillanatkép hibakereső tárhelyre. Után a kis memóriakép sikeresen fel van töltve, hanem törli a lemezen. A naplófájlok a feltöltése folyamat megőrzi a lemezen. Egy App Service environment-környezetben található a naplók a `D:\Home\LogFiles`. A Kudu felügyeleti webhely az App Service segítségével ezekben a naplófájlokban található.

1. Nyissa meg az App Service-alkalmazás az Azure portálon.

2. Válassza ki a **speciális eszközök** panelen, vagy keressen a **Kudu**.
3. Kattintson a **Ugrás**.
4. Az a **hibakereső konzol** legördülő listáján jelölje ki **CMD**.
5. Kattintson a **naplófájlok**.

Legalább egy fájlt egy nevű kezdődő kell megjelennie `Uploader_` vagy `SnapshotUploader_` és egy `.log` bővítmény. Kattintson a megfelelő ikonra, töltse le a naplófájlokat, illetve egy böngészőben nyissa meg ezeket.
A fájl nevét tartalmazza, amely azonosítja az App Service-példány egyedi utótagot kapjon. Az App Service-példány egynél több számítógép üzemelteti, ha nincsenek az egyes gépek külön naplófájlokat. Ha a feltöltése egy új kis memóriakép fájlt észlel, azt a naplófájl rögzíti. Íme egy példa a sikeres pillanatkép és a feltöltés:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> A fenti példában a Microsoft.ApplicationInsights.SnapshotCollector Nuget-csomag 1.2.0 verziója van. A korábbi verziókban a feltöltése folyamathoz `MinidumpUploader.exe` és a napló kevésbé részletes.

Az előző példában a instrumentation kulcsa `c12a605e73c44346a984e00000000000`. Ezt az értéket meg kell felelnie a instrumentation billentyűt az alkalmazás.
A kis memóriakép társítva a azonosítójú pillanatkép `139e411a23934dc0b9ea08a626db16c5`. Ezt az Azonosítót később segítségével keresse meg a társított kivételtelemetria Application Insights Analytics.

A feltöltése 15 percenként egyszer kapcsolatos új PDB-fájlok keres. Például:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Az alkalmazások, amelyek _nem_ üzemelteti az App Service-ben, a feltöltése feldolgozásra a tömörített memóriaképek ugyanabban a mappában: `%TEMP%\Dumps\<ikey>` (ahol `<ikey>` a rendszerállapot-kulcs).

### <a name="troubleshooting-cloud-services"></a>Hibaelhárítási Cloud Services csomag
Cloud Services szerepkörénél az alapértelmezett ideiglenes mappa túl kicsi ahhoz, hogy a memóriaképfájl, ami elveszett pillanatképek lehet.
A lemezterületre van szükség az alkalmazás és a megfelelő számú párhuzamos pillanatkép teljes munkakészletének függ.
A 32 bites ASP.NET webes szerepkör munkakészletének általában 200 MB és 500 MB közé esik.
Legalább két egyidejű pillanatkép-készítési engedélyezze.
Például ha az alkalmazás teljes munkakészletének 1 GB, akkor győződjön meg arról, hogy van-e legalább 2 GB szabad lemezterület-pillanatképek.
Kövesse az alábbi lépéseket a felhőalapú szolgáltatás szerepkör konfigurálása a pillanatképek helyi dedikált erőforrással.

1. Vegyen fel egy új helyi erőforrást a felhőalapú szolgáltatás definíciós (.csdef) fájl szerkesztésével a felhőalapú szolgáltatás. Az alábbi példa meghatározza egy nevű erőforrást `SnapshotStore` 5 GB méretű.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. A szerepkör indítása kódot adjon hozzá egy környezeti változó mutat, módosítsa a `SnapshotStore` helyi erőforrás. A feldolgozói szerepkörök, fel kell venni a kódot az Ön szerepköre `OnStart` módszert:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   A webes szerepkörök (ASP.NET), a kódot kell adni a webalkalmazás `Application_Start` módszert:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. A szerepkör ApplicationInsights.config fájl az ideiglenes mappa helye által használt felülbírálására frissítése `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

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

## <a name="next-steps"></a>További lépések

* [Állítsa be a snappoints a kódban](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) kivétel várakozás nélkül pillanatképek eléréséhez.
* [Kivételek a webalkalmazások diagnosztizálásához](app-insights-asp-net-exceptions.md) ismerteti, hogyan további kivételek láthatóvá az Application Insights részére.
* [Észlelési intelligens](app-insights-proactive-diagnostics.md) automatikusan észleli a teljesítményanomáliákat.
