---
title: Telemetria elválasztása az Azure Application Insightsban
description: A különböző erőforrásokhoz való közvetlen telemetria fejlesztési, tesztelési és üzemi bélyegzők.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 3580d162f4b3955a04ffcd0f13933221bfef3b65
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671460"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Telemetria elválasztása fejlesztési, tesztelési és éles környezetből

Ha egy webalkalmazás következő verzióját fejleszti, nem szeretné összekeverni a [Application Insights](../../azure-monitor/app/app-insights-overview.md) telemetria az új verzióról és a már kiadott verzióról. A félreértések elkerülése érdekében küldje el a telemetria különböző fejlesztési szakaszokból, hogy elkülönítse Application Insights erőforrásait, külön rendszerállapot-kulccsal (erőforráskulcsot). Annak érdekében, hogy könnyebb legyen módosítani a kialakítási kulcsot az egyik fázisról a másikra való váltáskor, hasznos lehet a rendszerállapotkulcsot beállítása kódban a konfigurációs fájl helyett. 

(Ha a rendszer egy Azure Cloud Service-szolgáltatás, akkor [egy másik módszer a különálló erőforráskulcsot beállítására](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Az erőforrások és a rendszerállapot-kulcsok ismertetése

A webalkalmazás Application Insights figyelésének beállításakor egy Application Insights *erőforrást* hoz létre Microsoft Azure. Ezt az erőforrást a Azure Portalban megnyitva megtekintheti és elemezheti az alkalmazásból gyűjtött telemetria. Az erőforrást egy rendszerállapot- *kulcs* (rendszerállapotkulcsot) azonosítja. Ha a Application Insights csomagot az alkalmazás figyelésére telepíti, azt a kialakítási kulccsal konfigurálja, hogy tudja, hová szeretné elküldeni a telemetria.

Általában külön erőforrásokat vagy egyetlen megosztott erőforrást használ különböző helyzetekben:

* Különböző független alkalmazások – minden alkalmazáshoz használjon külön erőforrás-és rendszerállapotkulcsot.
* Egy üzleti alkalmazás több összetevője vagy szerepköre – [egyetlen megosztott erőforrást](../../azure-monitor/app/app-map.md) használhat az összes összetevő-alkalmazáshoz. A telemetria szűrhetők vagy szegmentálható a cloud_RoleName tulajdonsággal.
* Fejlesztés, tesztelés és kiadás – használjon külön erőforrás-és rendszerállapotkulcsot a rendszer "Stamp" vagy "éles" fázisában lévő verzióihoz.
* A | B tesztelés – egyetlen erőforrás használata. Hozzon létre egy TelemetryInitializer, és adjon hozzá egy tulajdonságot a telemetria, amely azonosítja a változókat.


## <a name="dynamic-ikey"></a>Dynamic Instrumentation-kulcs

Annak érdekében, hogy könnyebb legyen módosítani a rendszerállapotkulcsot, mert a kód az éles fázisok között mozog, a konfigurációs fájl helyett a kódban állítsa be.

Állítsa be a kulcsot egy inicializálási metódusban, például global.aspx.cs egy ASP.NET-szolgáltatásban:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Ebben a példában a különböző erőforrások erőforráskulcsot a webes konfigurációs fájl különböző verzióiba helyezi. A webes konfigurációs fájl cseréje – a kiadási parancsfájl részeként is elvégezhető – a cél erőforrást fogja cserélni.

### <a name="web-pages"></a>Weblapok
A Rendszerállapotkulcsot az alkalmazás weblapjain is használják, a gyors üzembe helyezési panelen [kapott parancsfájlban](../../azure-monitor/app/javascript.md). Ahelyett, hogy a parancsfájlba kellene írnia, azt a kiszolgáló állapotától kell meghoznia. Például egy ASP.NET-alkalmazásban:

*JavaScript a Borotvában*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>További Application Insights erőforrások létrehozása
A különböző alkalmazás-összetevők telemetria elkülönítéséhez, illetve ugyanazon összetevő különböző bélyegzők (dev/test/termelés) elválasztásához létre kell hoznia egy új Application Insights erőforrást.

A [Portal.Azure.com](https://portal.azure.com)adjon hozzá egy Application Insights erőforrást:

![Kattintson az Új, majd az Application Insights lehetőségre](./media/separate-resources/01-new.png)

* Az **alkalmazás típusa** befolyásolja az Áttekintés panelen és a [metrika Explorerben](../../azure-monitor/app/metrics-explorer.md)elérhető tulajdonságokat. Ha nem látja az alkalmazás típusát, válassza ki a weblapok egyik webes típusát.
* Az **erőforráscsoport** a tulajdonságok, például a hozzáférés- [vezérlés](../../azure-monitor/app/resources-roles-access-control.md)kezelésére szolgáló kényelmi szolgáltatás. Külön erőforráscsoportokat használhat fejlesztési, tesztelési és éles környezetekhez.
* Az **előfizetés** az Azure-beli fizetési fiók.
* A **hely** , ahol megtartjuk az adatait. Jelenleg nem módosítható. 
* A **Hozzáadás az irányítópulton** egy gyors elérésű csempét helyez el az Azure kezdőlapján található erőforráshoz. 

Az erőforrás létrehozása eltarthat néhány másodpercig. Ha elkészült, megjelenik egy riasztás.

(Létrehozhat egy PowerShell- [szkriptet](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) , amely automatikusan létrehoz egy erőforrást.)

### <a name="getting-the-instrumentation-key"></a>A kialakítási kulcs beolvasása
A kialakítási kulcs azonosítja a létrehozott erőforrást. 

![Kattintson az Essentials elemre, kattintson a kialakítási kulcsra, CTRL + C](./media/separate-resources/02-props.png)

Szüksége lesz az összes olyan erőforrás rendszerállapot-kulcsaira, amelyekhez az alkalmazás elküldi az adatait.

## <a name="filter-on-build-number"></a>Kiépítés számának szűrése
Amikor közzéteszi az alkalmazás új verzióját, a különböző buildek közül választhat, hogy el szeretné-e különíteni a telemetria.

Megadhatja az alkalmazás verzió tulajdonságát, így szűrheti a [keresési](../../azure-monitor/app/diagnostic-search.md) és [metrikai Explorer](../../azure-monitor/app/metrics-explorer.md) eredményeit.

![Tulajdonság szűrése](./media/separate-resources/050-filter.png)

Az alkalmazás verzió tulajdonságának beállítása több különböző módszerrel is elvégezhető.

* Közvetlen beállítás:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Ezt a sort egy [telemetria inicializáló](../../azure-monitor/app/api-custom-events-metrics.md#defaults) sorba csomagolva biztosíthatja, hogy az összes TelemetryClient-példány konzisztens legyen.
* [ASP.NET] A verzió beállítása `BuildInfo.config`ban. A webmodul a BuildLabel csomópontból fogja kiválasztani a verziót. Adja meg ezt a fájlt a projektben, és ne feledje, hogy a másolás mindig tulajdonságot Megoldáskezelő.

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
* [ASP.NET] A BuildInfo. config automatikus előállítása az MSBuild-ben. Ehhez adjon hozzá néhány sort a `.csproj` fájlhoz:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Ez létrehoz egy *yourProjectName*nevű fájlt. BuildInfo. config. a közzétételi folyamat átnevezi a BuildInfo. config névre.

    A Build címke helyőrzőt (AutoGen_...) tartalmaz a Visual Studióval való kiépítés során. Az MSBuild-sel azonban a megfelelő verziószámmal vannak feltöltve.

    Ha engedélyezni szeretné, hogy az MSBuild verziószámokat hozzon létre, állítsa be a (z) `1.0.*` verziót a AssemblyReference.cs-ben

## <a name="version-and-release-tracking"></a>Verzió- és kiadáskövetés
Az alkalmazásverzió nyomon követéséhez győződjön meg arról, hogy a Microsoft Build Engine folyamat létrehozza a `buildinfo.config` fájlt. A `.csproj` fájlban adja hozzá a következőket:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Ha megkapja a verzióinformációkat, az Application Insights webmodul automatikusan hozzáadja az **Alkalmazás verzióját** tulajdonságként a telemetria minden eleméhez. Ez lehetővé teszi a verziók szerinti szűrést, amikor [diagnosztikai kereséseket](../../azure-monitor/app/diagnostic-search.md) végez, illetve [metrikákat vizsgál](../../azure-monitor/app/metrics-explorer.md).

Figyelje meg azonban, hogy a build verziószámát csak a Microsoft Build motorja hozza létre, nem pedig a Visual studióból származó fejlesztői Build.

### <a name="release-annotations"></a>Kiadási jegyzetek
Ha az Azure DevOps-t használja, [beolvashatja](../../azure-monitor/app/annotations.md) a diagramokhoz hozzáadott jegyzet jelölőket, amikor új verziót ad ki. Az alábbi képen látható, hogy jelenik meg a jelölő.

![Diagramon található példa kiadási jegyzet képernyőképe](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Következő lépések

* [Több szerepkör megosztott erőforrásai](../../azure-monitor/app/app-map.md)
* [Telemetria inicializáló létrehozása a következő megkülönböztetéséhez | B változatok](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
