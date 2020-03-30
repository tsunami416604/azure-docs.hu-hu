---
title: Telemetriai adatok elkülönítése az Azure Application Insightsban
description: Közvetlen telemetriai adatok különböző erőforrások fejlesztési, tesztelési és termelési bélyegzők.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 3580d162f4b3955a04ffcd0f13933221bfef3b65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671460"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Telemetriai adatok és fejlesztés, teszt és termelés elkülönítése

Amikor egy webalkalmazás következő verzióját fejleszti, nem szeretné összekeverni az [Application Insights](../../azure-monitor/app/app-insights-overview.md) telemetriai adatait az új verzióból és a már kiadott verzióból. A félreértések elkerülése érdekében küldje el a telemetriai adatokat a különböző fejlesztési szakaszok külön Application Insights-erőforrások, külön instrumentation kulcsok (ikeys). Annak érdekében, hogy könnyebben módosíthassa a műszerezési kulcsot, ahogy egy verzió egyik szakaszból a másikba kerül, hasznos lehet az ikey-t kódban beállítani a konfigurációs fájl helyett. 

(Ha a rendszer egy Azure Cloud-szolgáltatás, van [egy másik módszer a beállítás külön ikeys](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Erőforrások és műszerezési kulcsok –

Amikor beállítja az Application Insights figyelése a webalkalmazáshoz, hozzon létre egy Application *Insights-erőforrást* a Microsoft Azure-ban. Nyissa meg ezt az erőforrást az Azure Portalon az alkalmazásból gyűjtött telemetriai adatok megtekintéséhez és elemzéséhez. Az erőforrást egy *instrumentation kulcs* (ikey) azonosítja. Amikor telepíti az Application Insights-csomagot az alkalmazás figyelésére, konfigurálja azt a műszerkulccsal, hogy tudja, hová küldje a telemetriai adatokat.

Általában úgy dönt, hogy külön erőforrásokat vagy egyetlen megosztott erőforrást használ különböző esetekben:

* Különböző, független alkalmazások – Minden alkalmazáshoz külön erőforrást és ikey-t használjon.
* Egy üzleti alkalmazás több összetevője vagy szerepköre – [Egyetlen megosztott erőforrás](../../azure-monitor/app/app-map.md) használata az összes összetevő-alkalmazáshoz. Telemetriai lehet szűrni vagy szegmentált a cloud_RoleName tulajdonság.
* Fejlesztés, tesztelés és kiadás – Használjon külön erőforrást és ikey-t a rendszer "bélyegző" vagy termelési szakaszban lévő verzióihoz.
* A | B tesztelés - Egyetlen erőforrás használata. Hozzon létre egy TelemettryInitializer egy tulajdonságot a telemetriai, amely azonosítja a változatokat.


## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a>Dinamikus műszerezési kulcs

Annak érdekében, hogy a kód éles környezetei között mozogjon, könnyebben módosítsák azt a konfigurációs fájl helyett.

Állítsa be a kulcsot egy inicializálási módszerben, például global.aspx.cs egy ASP.NET szolgáltatásban:

*C #*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Ebben a példában a különböző erőforrások ikeys kerülnek a web konfigurációs fájl különböző verzióiban. A webes konfigurációs fájl felcserélése - amelyet a kiadási parancsfájl részeként tehet meg - kicseréli a célerőforrást.

### <a name="web-pages"></a>Weblapok
Az iKey az alkalmazás weboldalain, a [rövid útmutató panelből kapott parancsfájlban is használatos.](../../azure-monitor/app/javascript.md) Ahelyett, hogy a kódolás szó szerint a szkriptet, generálja azt a szerver állapotát. Például egy ASP.NET alkalmazásban:

*JavaScript a Razor*

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
A különböző alkalmazás-összetevők telemetriai adatait, vagy ugyanazon összetevő különböző bélyegzőinek (fejlesztési/tesztelési/éleskörnyezet) elkülönítéséhez létre kell hoznia egy új Application Insights-erőforrást.

A [portal.azure.com](https://portal.azure.com)adjon hozzá egy Application Insights-erőforrást:

![Kattintson az Új, majd az Application Insights lehetőségre](./media/separate-resources/01-new.png)

* **Az alkalmazástípus** befolyásolja az áttekintő panelen és a [metrikus kezelőben](../../azure-monitor/app/metrics-explorer.md)elérhető tulajdonságokat. Ha nem látja az alkalmazástípusát, válasszon egyet a weblapokhoz.
* **Az erőforráscsoport** kényelmes encikóként kezeli az olyan tulajdonságokat, mint a [hozzáférés-vezérlés.](../../azure-monitor/app/resources-roles-access-control.md) A fejlesztéshez, teszteléshez és termeléshez külön erőforráscsoportokat használhat.
* **Az előfizetés** az Azure-beli fizetési fiók.
* **A tartózkodási hely** az a hely, ahol az adatait őrizzük. Jelenleg nem lehet megváltoztatni. 
* **Az irányítópulthoz való hozzáadás** egy gyors elérésű csempét biztosít az erőforráshoz az Azure kezdőlapján. 

Az erőforrás létrehozása néhány másodpercet vesz igénybe. A rendszer figyelmeztetést fog látni, ha kész van.

[(PowerShell-parancsfájl](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) írása az erőforrások automatikus létrehozásához.)

### <a name="getting-the-instrumentation-key"></a>A műszerkulcs beszerzése
A műszerezési kulcs azonosítja a létrehozott erőforrást. 

![Kattintson az Essentials gombra, kattintson a Műszerezési billentyűre, a CTRL+C billentyűkombinációra.](./media/separate-resources/02-props.png)

Szüksége van az összes olyan erőforrás instrumentation kulcsaira, amelyekre az alkalmazás adatokat küld.

## <a name="filter-on-build-number"></a>Buildszám szűrése
Amikor közzéteszi az alkalmazás új verzióját, érdemes lesz elválasztani a telemetriai adatokat a különböző buildektől.

Beállíthatja az Alkalmazás verzió tulajdonságát, hogy szűrhesse a [keresési](../../azure-monitor/app/diagnostic-search.md) és [a metrikus felfedező](../../azure-monitor/app/metrics-explorer.md) eredményeit.

![Tulajdonság szűrése](./media/separate-resources/050-filter.png)

Az Application Version tulajdonság beállításának számos különböző módja van.

* Állítsa be közvetlenül:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Tördelje a sort egy [telemetriai inicializálóban](../../azure-monitor/app/api-custom-events-metrics.md#defaults) annak érdekében, hogy az összes TelemettryClient-példány konzisztensen legyen beállítva.
* Ez az ASP.NET. Állítsa be `BuildInfo.config`a verziót a ban. A webes modul felveszi a verziót a BuildLabel csomópontról. Adja meg ezt a fájlt a projektnek, és ne felejtse el beállítani a Mindig másolása tulajdonságot a Megoldáskezelőben.

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
* Ez az ASP.NET. Hozza létre a BuildInfo.config fájlt automatikusan az MSBuild fájlban. Ehhez adjon hozzá néhány sort `.csproj` a fájlhoz:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Ezzel létrehoz egy *yourProjectName*nevű fájlt. BuildInfo.config. A közzétételi folyamat buildinfo.config-ra nevezi át.

    A buildcímke helyőrzőt (AutoGen_...) tartalmaz, amikor a Visual Studio segítségével épít. De amikor az MSBuild-el készült, a megfelelő verziószámmal van feltöltve.

    Annak engedélyezéséhez, hogy az MSBuild `1.0.*` verziószámokat generáljon, állítsa be a verziót, mint a AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Verzió- és kiadáskövetés
Az alkalmazásverzió nyomon követéséhez győződjön meg arról, hogy a Microsoft Build Engine folyamat létrehozza a `buildinfo.config` fájlt. A `.csproj` fájlban adja hozzá a következőket:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Ha megkapja a verzióinformációkat, az Application Insights webmodul automatikusan hozzáadja az **Alkalmazás verzióját** tulajdonságként a telemetria minden eleméhez. Ez lehetővé teszi a verziók szerinti szűrést, amikor [diagnosztikai kereséseket](../../azure-monitor/app/diagnostic-search.md) végez, illetve [metrikákat vizsgál](../../azure-monitor/app/metrics-explorer.md).

Figyelje meg azonban, hogy a buildverziószámát csak a Microsoft Build Engine hozza létre, nem pedig a Visual Studio fejlesztői buildje.

### <a name="release-annotations"></a>Kiadási jegyzetek
Ha az Azure DevOps, akkor [kap egy jegyzetjelölő](../../azure-monitor/app/annotations.md) hozzá a diagramok, amikor egy új verzió kiadása. Az alábbi képen látható, hogy jelenik meg a jelölő.

![Diagramon található példa kiadási jegyzet képernyőképe](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>További lépések

* [Több szerepkör megosztott erőforrásai](../../azure-monitor/app/app-map.md)
* [Telemetriai inicializáló létrehozása az A| B változatok](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
