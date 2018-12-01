---
title: Azure Application Insights Snapshot Debugger .NET-alkalmazások |} A Microsoft Docs
description: Hibakeresési pillanatképek automatikusan gyűjteni, amikor a .NET-alkalmazások éles kivételek
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/10/2018
ms.reviewer: pharring
ms.author: mbullwin
ms.openlocfilehash: 2c5e99c16ed6ae0df9af4783fb6431edcbb6f100
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725403"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>A .NET-alkalmazások kivételeinek hibakeresési pillanatképei

Ha egy kivétel lép fel, automatikusan gyűjtheti hibakereső pillanatképek az élő webalkalmazások. A pillanatkép abban a pillanatban a kivétel történt a Forráskód és a változók állapotát mutatja. A pillanatkép-hibakereső (előzetes verzió) a [Azure Application Insights](app-insights-overview.md) kivétel telemetriát a webalkalmazás figyeli. Ez adatokat gyűjt a pillanatképek a felső értesítő kivételek, hogy rendelkezik diagnosztizálhatja a problémákat, éles környezetben a szükséges információkat. Tartalmazza a [Snapshot collector NuGet-csomag](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) az alkalmazásban, és szükség esetén konfigurálja a gyűjtési paramétereket lévő [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). A pillanatképek jelennek meg [kivételek](app-insights-asp-net-exceptions.md) az Application Insights portálon.

A portálon a hibakeresési pillanatfelvételeket megtekintve láthatja a hívásvermet és megvizsgálhatja a változókat az egyes hívásveremkeretekre vonatkozóan. A hatékonyabb hibakeresési környezetben a forráskóddal lekéréséhez nyissa meg a Visual Studio 2017 Enterprise pillanatképeket. A Visual Studióban is [állítsa be az interaktív módon pillanatfelvételeket Snappoints](https://aka.ms/snappoint) kivétel nélkül.

Hibakeresési pillanatképek hét napig tárolja. A megőrzési házirend van beállítva a alkalmazásonként. Ha ez az érték növelése van szüksége, kérheti növelését az Azure Portalon nyissa meg egy támogatási esetet.

Pillanatkép-gyűjtemény érhető el:
* .NET-keretrendszer és az ASP.NET alkalmazások futtatása a .NET-keretrendszer 4.5-ös vagy újabb.
* A Windows futó .NET core 2.0 és az ASP.NET Core 2.0 alkalmazásokat.

A következő környezetekben támogatottak:
* Azure App Service.
* Az Azure Cloud Service futó operációsrendszer-család, 4 vagy újabb verzióját.
* Az Azure Service Fabric-szolgáltatások a Windows Server 2012 R2 vagy újabb rendszerű.
* Azure Virtual machines szolgáltatásban futó Windows Server 2012 R2 vagy újabb.
* A helyi virtuális vagy fizikai gépeken futó Windows Server 2012 R2 vagy újabb.

> [!NOTE]
> Ügyfélalkalmazások (például a WPF, Windows Forms vagy UWP) használata nem támogatott.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Az ASP.NET-alkalmazások pillanatkép gyűjtésének konfigurálása

1. [Az Application Insights engedélyezése a webalkalmazásokban](app-insights-asp-net.md), ha még nem tette azt.

2. Tartalmazza a [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomagot az alkalmazásban.

3. Tekintse át az alapértelmezett beállításokat, a csomaghoz hozzáadott [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
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

4. A pillanatképek összegyűjtése csak az Application insights jelentett kivételek. Bizonyos esetekben (például a .NET-platformról a régebbi verzióiban), szüksége lehet [kivételek gyűjtésének konfigurálása](app-insights-asp-net-exceptions.md#exceptions) kivételek a portál pillanatképek megtekintéséhez.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Pillanatkép-gyűjtemény ASP.NET Core 2.0-alkalmazások konfigurálása

1. [Az Application Insights engedélyezése az ASP.NET Core webes alkalmazásban](app-insights-asp-net-core.md), ha még nem tette azt.

    > [!NOTE]
    > Arról, hogy az alkalmazás hivatkozik 2.1.1 verzió vagy újabb verziójú, a Microsoft.ApplicationInsights.AspNetCore csomag lehet.

2. Tartalmazza a [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomagot az alkalmazásban.

3. Módosítsa az alkalmazást, `Startup` osztály hozzáadása és konfigurálása a Snapshot Collector telemetriai processzor.

    Adja hozzá a következő using utasításokat a `Startup.cs`

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Adja hozzá a következő `SnapshotCollectorTelemetryProcessorFactory` osztályt az `Startup` osztály.

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
    Adja hozzá a `SnapshotCollectorConfiguration` és `SnapshotCollectorTelemetryProcessorFactory` szolgáltatások a rendszerindítási folyamat számára:

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

4. A Snapshot Collector konfigurálása felvételével appsettings.json SnapshotCollectorConfiguration szakasz. Példa:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Pillanatkép-gyűjtemény más .NET-alkalmazások konfigurálása

1. Ha az alkalmazás már nincs kialakítva az Application Insights, első lépésként [az Application Insights engedélyezése és a kialakítási kulcs beállítása](app-insights-windows-desktop.md).

2. Adja hozzá a [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomagot az alkalmazásban.

3. A pillanatképek összegyűjtése csak az Application insights jelentett kivételek. Előfordulhat, hogy módosítania a kódokat a jelentés azokat. A kivételkezelő kód függ az alkalmazás felépítését, de például nem éri el:
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

A pillanatképek hozzáférést szerepköralapú hozzáférés-vezérlés (RBAC) védi. Vizsgálhatja meg a pillanatképet, meg kell először adni a megfelelő szerepkör előfizetés tulajdonosa.

> [!NOTE]
> Tulajdonosok és a közreműködőknek nem automatikusan rendelkezik ehhez a szerepkörhöz. Ha szeretne pillanatképek megtekintése, hozzá kell magukat a szerepkört.

Előfizetés-tulajdonost kell rendelni a `Application Insights Snapshot Debugger` szerepkör a felhasználók számára, akik pillanatképek vizsgálata. Ehhez a szerepkörhöz is hozzárendelhető az egyes felhasználók vagy csoportok szerint az előfizetés-tulajdonosokat a cél Application Insights-erőforrást, vagy a erőforráscsoportba vagy előfizetésbe.

1. Keresse meg az Application Insights-erőforrást az Azure Portalon.
1. Kattintson a **hozzáférés-vezérlés (IAM)**.
1. Kattintson a **+ szerepkör-hozzárendelés hozzáadása** gombra.
1. Válassza ki **Application Insights Snapshot Debugger** származó a **szerepkörök** legördülő listából.
1. Keresse meg, és adja meg a hozzáadni kívánt felhasználó nevét.
1. Kattintson a **mentése** gombra kattintva adja hozzá a felhasználót a szerepkörhöz.


> [!IMPORTANT]
> A pillanatképek potenciálisan tartalmazó változót és a paraméter értékét a személyes és egyéb bizalmas adatokat.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Az Application Insights portálon készült pillanatképek hibakeresése

Ha egy adott kivétel vagy probléma azonosítója, érhető el egy pillanatkép- **hibakeresési pillanatfelvétel megnyitása** gomb jelenik meg a [kivétel](app-insights-asp-net-exceptions.md) az Application Insights portálon.

![Hibakeresési pillanatfelvétel megnyitása gombjára kivétel](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

A hibakeresési pillanatfelvétel nézetben láthatja a hívási verem és a egy változók panelen. Keretek, a hívási veremben a hívási verem panelen válassza ki, ha helyi változók is megtekintheti, és a Változók panelen hívja a függvény paramétereit.

![Hibakeresési Pillanatfelvétel megtekintése a portálon](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

A pillanatképek bizalmas információkat tartalmazhatnak, és alapértelmezés szerint nem látható. Pillanatképek megtekintése, rendelkeznie kell a `Application Insights Snapshot Debugger` Önhöz hozzárendelt szerepkörrel.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>A Visual Studio 2017 Enterprise készült pillanatképek hibakeresése
1. Kattintson a **Pillanatfelvétel letöltése** gombra kattintva töltse le a `.diagsession` fájlt, amely a Visual Studio 2017 Enterprise nyithatja meg.

2. Megnyitásához a `.diagsession` fájl, rendelkeznie kell a pillanatkép-hibakereső VS összetevő telepítve van. A pillanatkép-hibakereső összetevő egy szükséges összetevő az ASP.net munkaterhelés a VS-ben és a VS-telepítő egyes összetevők listájából választható ki. Ha a Visual Studio 15.5 előtti verziót használ, telepítenie kell a bővítmény a [VS marketplace](http://aka.ms/snapshotdebugger).

3. A pillanatkép-fájl megnyitása után az tömörített memóriaképet hibakeresés a Visual Studióban megjelenik. Kattintson a **felügyelt kód hibakeresése** a pillanatkép-hibakeresés elindításához. A pillanatkép megnyitása, ahol a kivétel lépett fel, hogy a folyamat aktuális állapota is hibakeresése kódsort.

    ![A Visual Studióban nézet hibakeresési pillanatkép](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

A letöltött pillanatképet tartalmaz szimbólum fájlokat, a webalkalmazás-kiszolgáló található. A szimbólum fájlok a pillanatkép adatainak társítandó forráskód szükségesek. Az App Service-alkalmazások esetén ügyeljen arra, hogy engedélyezi a szimbólum központi telepítést, a web apps közzétételekor.

## <a name="how-snapshots-work"></a>A pillanatképek működése

A pillanatképek adatgyűjtője van megvalósítva egy [Application Insights Telemetria processzor](app-insights-configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Az alkalmazás futtatásakor az alkalmazás telemetriai folyamat hozzáadódik a Snapshot Collector Telemetriai processzor.
Minden alkalommal, amikor az alkalmazás hívások [TrackException](app-insights-asp-net-exceptions.md#exceptions), a pillanatképek adatgyűjtője kiszámítja a kivétel és a rtesítő metódus probléma azonosítója.
Minden alkalommal, amikor az alkalmazás meghívja a TrackException, a számláló értéke akkor nő, a megfelelő probléma azonosítóját. Ha a számláló elérte a `ThresholdForSnapshotting` érték, a probléma azonosítója bekerül a gyűjtési terv.

A Snapshot Collector is figyeli a kivételek, mivel azokat már a való feliratkozással lépett fel a [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) esemény. Az esemény akkor következik be, ha a probléma azonosítója, a kivétel számított, és összehasonlítja a gyűjtési tervben probléma azonosítóját.
Ha nincs egyezés, a futó folyamat egy pillanatkép jön létre. A pillanatkép van rendelve egy egyedi azonosítót, és a kivételt, hogy az azonosító van megjelölve. A FirstChanceException kezelő adja vissza, miután a thrown kivétel normál feldolgozása. Végül a kivétel eléri a TrackException metódus újra ahol, a pillanatkép azonosítója, valamint a rendszer jelenti az Application Insights.

A fő folyamat tovább fut, és a forgalmat a felhasználók számára a kis megszakítás szolgálja ki. Eközben a pillanatkép lesz továbbítva a pillanatkép Adatfeltöltő folyamat. A pillanatkép feltöltő tömörített memóriaképet hoz létre, és feltölti azt az Application insights bármely megfelelő szimbólum (.pdb) fájlokkal együtt.

> [!TIP]
> - Egy folyamat pillanatkép a futó folyamatot felfüggesztett klónja.
> - A pillanatkép létrehozása ezredmásodperc alatt körülbelül 10 és 20.
> - Az alapértelmezett érték a `ThresholdForSnapshotting` : 1. Ez egyben a minimális érték. Ezért az alkalmazás is ugyanazt a kivételt kiváltó **kétszer** előtt egy pillanatkép jön létre.
> - Állítsa be `IsEnabledInDeveloperMode` igaz értékre, ha szeretné létrehozni a pillanatképek hibakeresése a Visual Studióban során.
> - A pillanatkép létrehozása sebessége korlátozza a `SnapshotsPerTenMinutesLimit` beállítás. Alapértelmezés szerint a korlát a egy pillanatképet minden tíz perc.
> - Előfordulhat, hogy naponta legfeljebb 50 pillanatképek tölthető fel.

## <a name="current-limitations"></a>Aktuális korlátozások

### <a name="publish-symbols"></a>Szimbólumok közzététele
A pillanatkép-hibakereső van szükség, szimbólumfájlok való dekódolandó változók és a hibakeresés érdekében a Visual Studióban az üzemi kiszolgálón.
Verzió 15.2 (vagy újabb) a Visual Studio 2017 tesz közzé az szimbólumok, a kiadási alapértelmezés szerint hoz létre, amikor közzéteszi az App Service-ben. Az előzetes verziók, adja hozzá az alábbi sort a közzétételi profilt kell `.pubxml` fájlt úgy, hogy a kiadási módban közzétett szimbólumokat tartalmazhatja:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Az Azure Compute és más típusú, ellenőrizze, hogy a szimbólumfájlok a főalkalmazása .dll fájl ugyanabba a mappába (általában `wwwroot/bin`) vagy az aktuális elérési úton érhető el.

### <a name="optimized-builds"></a>Optimalizált buildek
Bizonyos esetekben helyi változók nem lehet megjeleníteni a kiadási buildek a fordító által alkalmazott optimalizálás miatt.
Azonban az Azure App Servicesben, a pillanatképek adatgyűjtője is deoptimize a gyűjtési terv részét képező rtesítő módszereket.

> [!TIP]
> Telepítse az Application Insights-Webhelybővítményt a deoptimization támogatás az App Service-ben.

## <a name="troubleshooting"></a>Hibaelhárítás

Ezek a tippek segítségével a pillanatkép-hibakereső kapcsolatos problémák elhárításához.

### <a name="use-the-snapshot-health-check"></a>Használja a pillanatkép állapotának ellenőrzése
Számos gyakori problémára eredményez a hibakeresési pillanatfelvétel megnyitása nem jelenik meg. Egy elavult Snapshot Collector, például; használatával a napi feltöltési korlát elérése vagy esetleg a pillanatkép van csak hosszú ideig tart a feltöltendő. A pillanatkép állapotát ellenőrző használatával kapcsolatos gyakori problémák elhárítása.

A teljes körű nyomkövetési nézet, amellyel a pillanatkép állapotának ellenőrzése a kivétel ablaktáblán egy kapcsolat áll fenn.

![Adja meg a pillanatkép-állapot-ellenőrzése](./media/app-insights-snapshot-debugger/enter-snapshot-health-check.png)

Az interaktív, csevegés-szerű felületet keres a gyakori problémákat, és végigvezeti Önt felé azok javításához.

![Állapot-ellenőrzése](./media/app-insights-snapshot-debugger/healthcheck.png)

Ha ez nem oldja meg a problémát, majd tekintse meg a következő manuális hibaelhárítási lépéseket.

### <a name="verify-the-instrumentation-key"></a>A kialakítási kulcs ellenőrzése

Ellenőrizze, hogy a megfelelő kialakítási kulcsot használ a közzétett alkalmazásban. Általában a kialakítási kulcs az ApplicationInsights.config fájlban olvasható. Ellenőrizze, hogy értéke ugyanaz, mint a rendszerállapotkulcsot az Application Insights-erőforrás számára, a portálon.

### <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>A legújabb verzióra a NuGet-csomag frissítése

A Visual Studio NuGet-Csomagkezelő használatával győződjön meg arról, hogy a Microsoft.ApplicationInsights.SnapshotCollector legújabb verzióját használja. Kibocsátási megjegyzések található https://github.com/Microsoft/ApplicationInsights-Home/issues/167

### <a name="check-the-uploader-logs"></a>Az adatfeltöltő naplókat

Pillanatkép létrehozása után egy kis memóriakép-fájl (.dmp) jön létre a lemezen. Egy külön adatfeltöltő-folyamatot hoz létre a tömörített memóriaképet fájlt, és feltölti azt, együtt minden társított PDBs, az Application Insights Snapshot Debugger storage. Miután a tömörített memóriaképet sikeres feltöltését követően a lemezről törlődik. Az adatfeltöltő-folyamat a naplófájlok őrzi meg a lemezen. App Service Environment-környezetben, keresse meg ezeket a naplókat a `D:\Home\LogFiles`. A Kudu felügyeleti hely az App Service használatával kereshet ezekben a naplófájlokban.

1. Nyissa meg az App Service-alkalmazás az Azure Portalon.
2. Kattintson a **speciális eszközök**, vagy keressen **Kudu**.
3. Kattintson a **Go**.
4. Az a **hibakereső konzol** legördülő listában jelölje ki **CMD**.
5. Kattintson a **LogFiles**.

Legalább egy fájlt névvel kezdődő kell megjelennie `Uploader_` vagy `SnapshotUploader_` és a egy `.log` bővítmény. Kattintson a megfelelő ikonra, töltse le a naplófájlokat vagy a böngészőben megnyitja őket.
A fájl nevét tartalmazza, amely azonosítja az App Service-példány egyedi utótagja. Az App Service-példányhoz egynél több gépen üzemel, van-e az egyes gépek külön naplófájlokat. Amikor a feltöltő egy új tömörített memóriaképet fájlt észlel, azt a naplófájl rögzíti. Íme egy példa a sikeres pillanatkép és a feltöltés:

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
> A fenti példában a Microsoft.ApplicationInsights.SnapshotCollector NuGet-csomag 1.2.0-s vagy annál újabb verziójából származik. A korábbi verziókban a adatfeltöltő folyamatot nevezik `MinidumpUploader.exe` és a napló kevésbé részletes.

Az előző példában a műszerezettségi kulcs jelenleg `c12a605e73c44346a984e00000000000`. Ezt az értéket meg kell egyeznie a kialakítási kulcsot az alkalmazáshoz.
A tömörített memóriaképet társítva a Azonosítóval rendelkező pillanatkép `139e411a23934dc0b9ea08a626db16c5`. Keresse meg a kapcsolódó kivétel telemetriát az Application Insights-elemzési később használhatja ezt az Azonosítót.

Az adatfeltöltő új PDBs 15 percenként egyszer kapcsolatban keres. Például:

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

Alkalmazások, amelyek _nem_ adatfeltöltő naplók vannak az App Service-ben üzemeltetett, a tömörített memóriaképek ugyanabban a mappában: `%TEMP%\Dumps\<ikey>` (ahol `<ikey>` a kialakítási kulcs).

### <a name="troubleshooting-cloud-services"></a>A Cloud Services hibaelhárítása
Szerepkörhöz a Felhőszolgáltatásokban az alapértelmezett ideiglenes mappa esetleg túl kicsi ahhoz, hogy a kis memóriaképfájl, vezető elveszett pillanatképek.
A szükséges hely az alkalmazás és a párhuzamos pillanatképek számát teljes munkakészletének függ.
32 bites ASP.NET webes szerepkör munkakészletének általában 200 MB-TAL és 500 MB között van.
Legalább két egyidejű pillanatképek lehetővé.
Például ha az alkalmazás használja a teljes munkakészletet az 1 GB, akkor gondoskodnia kell, hogy nincs-e tárolni a pillanatképek lemezterület legalább 2 GB.
Kövesse az alábbi lépéseket a felhőalapú szolgáltatás szerepkör konfigurálása a pillanatképek egy dedikált helyi erőforrás.

1. Adjon hozzá egy új helyi erőforrást a Felhőszolgáltatás a felhőalapú szolgáltatás definíciós (.csdef) fájl szerkesztésével. Az alábbi példa meghatározza nevű erőforrás `SnapshotStore` 5 GB-os mérettel.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Módosítsa a szerepkör indítási kódot, amellyel hozzáadja egy környezeti változó, amely a `SnapshotStore` helyi erőforrás. A feldolgozói szerepkörök esetében a kód fel kell venni a szerepkör `OnStart` módszer:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   A webes szerepkörök (ASP.NET), a kód hozzá kell adni a webalkalmazás `Application_Start` módszer:
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

3. A szerepkör ApplicationInsights.config fájl felülírásához használja az ideiglenes mappa frissítése `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

### <a name="overriding-the-shadow-copy-folder"></a>Az árnyékmásolat-mappa felülbírálása

A Snapshot Collector indításakor próbál egy mappát a lemezen, amely lehetővé teszi a pillanatkép Adatfeltöltő folyamat futtatásához. A kiválasztott mappát a árnyékmásolat mappa néven ismert.

A Snapshot Collector ellenőrzi néhány jól ismert helyek, gondoskodik róla, hogy a pillanatkép Adatfeltöltő bináris fájlok másolását engedélyekkel rendelkezik. Az alábbi környezeti változókat használják:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Nem található a megfelelő mappát, ha a Snapshot Collector egy hiba üzenettel jelentések _"Nem található a megfelelő árnyékmásolat mappa másolása."_

Ha a példány nem sikerül, pillanatképek adatgyűjtője jelentések egy `ShadowCopyFailed` hiba.

Ha nem lehet elindítani a feltöltő, pillanatképek adatgyűjtője jelentések egy `UploaderCannotStartFromShadowCopy` hiba. Az üzenet törzsében gyakran tartalmaznak `System.UnauthorizedAccessException`. Ez a hiba általában akkor fordul elő, mert az alkalmazás korlátozott engedélyekkel rendelkező fiókkal futtatja. A fiók az árnyékmásolat mappa másolása írási engedéllyel rendelkezik, de nem rendelkezik engedéllyel a kódot.

Ezek a hibák általában indítása során fordulhat elő, mert azok fog általában následovat egy `ExceptionDuringConnect` hiba üzenettel _"Feltöltése nem sikerült elindítani."_

Ezek a hibák megoldása, adja meg manuálisan keresztül mappa árnyékmásolat másolása a `ShadowCopyFolder` konfigurációs beállítást. Például az applicationinsights.config fájlban:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Vagy ha egy .NET Core-alkalmazást az appsettings.json használja:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Az Application Insights keresés használatával pillanatképekkel kivételek keresése

Egy pillanatkép jön létre, amikor a rtesítő kivétel van-e megjelölve a pillanatkép-azonosítót. A pillanatkép-azonosító alkalmazás egyéni tulajdonság része, a kivétel telemetriát az Application Insights jelentésekor. Használatával **keresési** az Application Insights, az összes telemetriai adat található a `ai.snapshot.id` egyéni tulajdonság.

1. Keresse meg az Application Insights-erőforrást az Azure Portalon.
2. Kattintson a **keresési**.
3. Típus `ai.snapshot.id` a keresőmezőbe, és nyomja le az Enter.

![Keresse meg a telemetriai adatok a portálon egy pillanatkép-azonosító](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Ha a keresés eredménytelen, majd pillanatképek nem jelzett az Application insights szolgáltatásba az alkalmazás a kijelölt időtartományban található.

Keresse meg a naplók feltöltése egy adott pillanatkép-azonosító, a keresőmezőbe írja be Azonosítóval. Ha telemetriai adatokat, amelyeket feltöltött egy pillanatkép nem találja, kövesse az alábbi lépéseket:

1. Ellenőrizze, hogy dolgozik a megfelelő Application Insights-erőforrást a kialakítási kulcsot ellenőrzésével.

2. Az időtartomány szűrő ahhoz, hogy biztosítsák az adott időtartományt a keresés használatával történő küldés időbélyegzője legyen a Adatfeltöltő-naplóból, állítsa be.

Ha továbbra sem látja, hogy a pillanatkép-Azonosítóval rendelkező kivételt, a kivétel telemetriát az Application Insights nem jelentett. Ez a helyzet akkor fordulhat elő, ha az alkalmazás összeomlott ideig tartott a pillanatkép után, de mielőtt azt jelentette, hogy a kivétel telemetriát. Ebben az esetben ellenőrizze az App Service-naplók alatt `Diagnose and solve problems` megtekintéséhez, hogy voltak-e váratlan újraindítást vagy nem kezelt kivételeket.

### <a name="edit-network-proxy-or-firewall-rules"></a>Hálózati proxy vagy tűzfal-szabályok szerkesztése

Ha az alkalmazás csatlakozik az interneten keresztül proxy vagy tűzfal, szükség lehet ahhoz, hogy a pillanatkép-hibakereső szolgáltatással folytatott kommunikációhoz az alkalmazás a szabályok szerkesztése. Íme [IP-címek és a pillanatkép-hibakereső által használt portok listáját](app-insights-ip-addresses.md#snapshot-debugger).

## <a name="next-steps"></a>További lépések

* [A kódban snappoints beállítása](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) beolvasni a pillanatképek kivétel nélkül.
* [A webalkalmazások kivételeinek diagnosztizálása](app-insights-asp-net-exceptions.md) azt ismerteti, hogyan további kivételek láthatóvá az Application Insightsba.
* [Intelligens detektálás](app-insights-proactive-diagnostics.md) automatikusan felderíti a teljesítményanomáliákat.
