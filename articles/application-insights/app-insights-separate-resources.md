---
title: Telemetria elválasztja fejlesztése, tesztelése és tegye közzé az Azure Application Insights |} A Microsoft Docs
description: A fejlesztési, tesztelési és éles stampek a különböző erőforrások közvetlen telemetriai adatokat.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: 77c0baba1c30153730e87181e24137d9a20ea6b1
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012470"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Telemetria elválasztja a fejlesztési, tesztelési és éles környezetben

Fejleszt egy webalkalmazást a következő verziójára, ha nem szeretne összekeveri a [Application Insights](app-insights-overview.md) az új és a már kiadott verziója származó telemetriai adatok. A félreértések elkerülése végett a telemetriát küld külön Application Insights-erőforrást a különböző kialakítási kulcsokat (erőforráskulcsot) különböző fejlesztési szakaszban. Könnyebb a kialakítási kulcsot módosítani, mert egy verzió egy lépésben hajtaná helyez át egy másik, hasznos lehet, állítsa be a rendszerállapotkulcsot a kódban, hanem a konfigurációs fájlban. 

(Ha a rendszer egy Azure-Felhőszolgáltatás, nincs [külön erőforráskulcsot beállítása egy másik módszer](app-insights-cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Erőforrások és a kialakítási kulcs

A webalkalmazás figyelése az Application Insights beállításakor, hozzon létre egy Application Insights *erőforrás* Microsoft Azure-ban. Annak érdekében, hogy tekintse meg és elemezheti a telemetriát, gyűjtése az alkalmazásból az Azure Portalon nyissa meg a ehhez az erőforráshoz. Az erőforrás által azonosított egy *kialakítási kulcs* (rendszerállapotkulcsot). Az alkalmazás figyelése az Application Insights-csomag telepítésekor a konfigurálhatja a kialakítási kulcsot, hogy tudja, hova küldhetők a telemetriát.

Általában választja, külön erőforrásokat vagy egyetlen megosztott erőforrás használatához különböző helyzetekben:

* Különböző, a független alkalmazásokat – minden alkalmazáshoz külön erőforrást és rendszerállapotkulcsot használata.
* Több összetevő vagy egy üzleti alkalmazás - szerepkörök használatához egy [egyetlen megosztott erőforrást](app-insights-app-map.md) az összetevő-alkalmazások. Telemetriai adatok szűrhetők és cloud_RoleName tulajdonság alapján szegmentált.
* A fejlesztési, tesztelési és kiadás – használja egy külön erőforrás és a rendszerállapotkulcsot a rendszer a "időbélyegző" verzióiban vagy termelési szakaszban.
* A |} B tesztelés – egyetlen erőforrást használjon. Hozzon létre egy TelemetryInitializer tulajdonság hozzáadása, amely azonosítja a változatok telemetriai adatokat.


## <a name="dynamic-ikey"></a> A dinamikus kialakítási kulcs

Könnyebb keresztül tudja módosítani a rendszerállapotkulcsot termelési szakaszokat helyezi át a kódot, beállíthatja a programkódban helyett a konfigurációs fájlban.

A kulcsot meg például az ASP.NET-szolgáltatások Global.aspx.cs osztályból, egy inicializálási metódust:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Ebben a példában az erőforráskulcsot a különböző erőforrások különböző verzióit a webes konfigurációs fájlban kerülnek. A webes konfigurációs fájlban – amely megtehető a kiadási szkript részeként - felcserélése felcserélni a a célként megadott erőforrás.

### <a name="web-pages"></a>Weblapok
A Rendszerállapotkulcsot is használatban van az alkalmazás weblapok, az [parancsfájlt, amely a gyors üzembe helyezési panel származó](app-insights-javascript.md). Helyett kódolási, szó szerint a parancsfájlba, hozza létre az a kiszolgáló állapota. Ha például az ASP.NET-alkalmazás:

*A Razor JavaScript*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>További Application Insights-erőforrások létrehozása
Telemetriai adatok különböző alkalmazás-összetevők, illetve különböző blokkokban (fejlesztői/teszt/éles) összetevő külön, majd kell létrehozni egy új Application Insights-erőforrást.

Az a [portal.azure.com](https://portal.azure.com), Application Insights-erőforrás hozzáadása:

![Kattintson az Új, majd az Application Insights lehetőségre](./media/app-insights-separate-resources/01-new.png)

* **Az alkalmazástípus** érinti, amit lát az áttekintési panelen és az elérhető tulajdonságok [metrika explorer](app-insights-metrics-explorer.md). Ha nem látja az alkalmazás típusát, válassza ki a web típusú weblapokra vonatkozóan.
* **Erőforráscsoport** kezeléséhez a tulajdonságokat, mint a kényelem van [hozzáférés-vezérlés](app-insights-resources-roles-access-control.md). A fejlesztési, tesztelési és éles külön erőforráscsoportok használatával.
* **Előfizetés** a fizetési fiók az Azure-ban.
* **Hely** van, ahol azt tartsa adatait. Jelenleg nem módosítható. 
* **Irányítópult hozzáadása** az erőforrás gyors elérést csempe az Azure kezdőlapjának helyezi. 

Az erőforrás létrehozásához néhány másodpercet vesz igénybe. Amikor kész van, megjelenik egy riasztás.

(Írhat egy [PowerShell-parancsprogram](app-insights-powershell-script-create-resource.md) erőforrás automatikus létrehozásához.)

### <a name="getting-the-instrumentation-key"></a>A kialakítási kulcs beolvasása
A kialakítási kulcs azonosítja az erőforrást, Ön által létrehozott. 

![Kattintson az Essentials, a kialakítási kulcsot, a CTRL + C](./media/app-insights-separate-resources/02-props.png)

Szüksége lesz a kialakítási kulcs, amelyhez az összes erőforrást az alkalmazás elküldi az adatokat.

## <a name="filter-on-build-number"></a>A buildszám szűrése
Ha közzéteszi az alkalmazás új verziója, érdemes tudni a telemetriai adatok elkülönítése különböző buildeket.

Az alkalmazás verziója tulajdonság beállíthatja, hogy szűrheti [keresési](app-insights-diagnostic-search.md) és [metrika explorer](app-insights-metrics-explorer.md) eredményeket.

![Vlastnost szűrése](./media/app-insights-separate-resources/050-filter.png)

Nincsenek számos különböző módszer az alkalmazás verziója tulajdonságának beállítása.

* Állítsa be közvetlenül:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Sortörés a sort egy [telemetriainicializáló](app-insights-api-custom-events-metrics.md#defaults) annak biztosítása érdekében, hogy minden TelemetryClient példány konzisztens módon vannak-e beállítva.
* [AZ ASP.NET] Állítsa be a verzió `BuildInfo.config`. A modul a verziót a BuildLabel csomópont kiesik. Ez a fájl tartalmazza a projektben, és ne feledje, a másolási mindig tulajdonság beállítása a Megoldáskezelőben.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [AZ ASP.NET] Hozzon létre automatikusan a BuildInfo.config MSBuild. Ehhez írjon be néhány sor, a `.csproj` fájlt:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Ez létrehoz egy fájlt nevű *yourProjectName*. BuildInfo.config. A közzétételi folyamat átnevezése BuildInfo.config.

    A build címke tartalmaz egy helyőrző (AutoGen_...), a Visual Studióval készítése során. De létrehozásakor az MSBuild, akkor megjelenik a megfelelő verziószámot.

    Ahhoz, hogy a verziószámok létrehozásához MSBuild, állítsa be a verziót, például `1.0.*` AssemblyReference.cs a

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
Ha használja az Azure DevOps, [jelölőt lekérése](app-insights-annotations.md) hozzáadódik a diagramokat, amikor új verzió kiadása. Az alábbi képen látható, hogy jelenik meg a jelölő.

![Diagramon található példa kiadási jegyzet képernyőképe](media/app-insights-separate-resources/release-annotation.png)
## <a name="next-steps"></a>További lépések

* [Több szerepkör megosztott erőforrások](app-insights-monitor-multi-role-apps.md)
* [Hozzon létre egy Telemetriainicializáló különbséget tenni A |} B variantní hodnoty.](app-insights-api-filtering-sampling.md#add-properties)
