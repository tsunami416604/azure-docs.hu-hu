---
title: "Telemetria mappától fejlesztési, tesztelése és Azure Application Insights megjelenése |} Microsoft Docs"
description: "Közvetlen telemetriai különböző erőforrásokra vonatkozó fejlesztői, tesztelési és éles stampek."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: 8d95958bce0597bfb16ef1c6b99b72ce9134e66f
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Telemetria mappától fejlesztői, tesztelési és éles

A webes alkalmazás a következő verziójában fejleszt, ha nem kívánja összekeveri az [Application Insights](app-insights-overview.md) telemetriai adatokat az új verzió és a már kiadott verzióját. Félreértések elkerülése érdekében a telemetriai adatokat küldhet az Application Insights-erőforrások, külön külön instrumentation kulcsokkal (ikeys) a különböző fejlesztési fázisból áll. Könnyebb a instrumentation kulcs módosítható, mert a verziót egy egy lépésben helyez át egy másik, hasznos lehet beállítani a ikey kód ahelyett, hogy a konfigurációs fájlban. 

(Ha a rendszer egy Azure-Felhőszolgáltatásban nincs [más módszerrel kell beállítania külön ikeys](app-insights-cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Erőforrások és a rendszerállapot-kulcsok

Az Application Insights a webalkalmazás figyelésének beállításakor hoz létre az Application Insights *erőforrás* a Microsoft Azure-ban. Az Azure portálon ahhoz, hogy tekintse meg és elemezheti az alkalmazásban gyűjtött telemetriáját nyissa meg a ehhez az erőforráshoz. Az erőforrás által azonosított egy *instrumentation kulcs* (ikey). Ha telepíti az Application Insights csomagot a figyelheti az alkalmazást, akkor konfigurálnia a rendszerállapot-kulcsban, így az tudni fogja, hogy hova küldje a telemetriai adatok.

Általában használata mellett dönt külön erőforrások vagy egy megosztott erőforrás különböző helyzetekben:

* Különböző, független alkalmazások - alkalmazásokra vonatkozó különálló erőforrás és ikey használata.
* Több összetevő vagy egy üzleti alkalmazás - szerepkörök használja egy [egyetlen megosztott erőforrás](app-insights-monitor-multi-role-apps.md) az összetevő-alkalmazásokhoz. Telemetriai adatok szűrhetők és szegmentált cloud_RoleName tulajdonság által.
* Fejlesztői, tesztelési és verzió - használata egy külön erőforrás és ikey a rendszer a "stamp" verzióiban vagy az éles szakasza.
* A |} B tesztelés - azonosítónak egyetlen erőforrásra használja. Hozzon létre egy TelemetryInitializer tulajdonság hozzáadása a telemetriai adat, amely azonosítja a Variant adattípusban.


## <a name="dynamic-ikey"></a>Dinamikus instrumentation kulcs

Könnyebb a ikey módosítható, mert a termelési szakaszokat között helyezi a kódot, állítani a kódban ahelyett, hogy a konfigurációs fájlban.

A kulcs egy inicializálási metódust, például egy ASP.NET-szolgáltatásban Global.aspx.cs osztályból meg:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Ebben a példában a különböző erőforrások ikeys a webes konfigurációs fájl különböző verzióinak kerülnek. A webes konfigurációs fájl – amely megteheti azt is, a feloldási parancsprogram részeként - csere felcserélni a célerőforrás.

### <a name="web-pages"></a>Weblapok
A iKey is használatban van az alkalmazás weblapok, az [portáltól kapott a gyors üzembe helyezési panel parancsfájl](app-insights-javascript.md). Helyett megadás szó a parancsfájlba, hozza létre azt a kiszolgáló állapotát. Például egy ASP.NET alkalmazásban:

*JavaScript Razor*

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
Külön telemetriai különböző alkalmazás-összetevők, vagy másik bélyegzők (fejlesztési/tesztelési/éles) az adott összetevő, ezzel meglesz az hozzon létre egy új Application Insights-erőforrást.

Az a [portal.azure.com](https://portal.azure.com), vegyen fel egy Application Insights-erőforrást:

![Kattintson az Új, majd az Application Insights lehetőségre](./media/app-insights-separate-resources/01-new.png)

* **Az alkalmazástípus** elemnél mi jelenik a áttekintése panel megnyitásához, és a tulajdonságok érhetők el a [metrika explorer](app-insights-metrics-explorer.md). Ha nem látja a típusú alkalmazást, válassza ki a web típusú weblapokra vonatkozó.
* **Erőforráscsoport** kezeléséhez a tulajdonságokat, például a könnyebb van [hozzáférés-vezérlés](app-insights-resources-roles-access-control.md). Fejlesztési, tesztelési és éles külön erőforráscsoportok használható.
* **Előfizetés** a fizetési fiók az Azure-ban.
* **Hely** van, ahol azt megőrizni az adatokat. Jelenleg nem módosítható. 
* **Irányítópult felvétele** helyezi az erőforrás egy gyors elérést csempe az Azure kezdőlapján. 

Az erőforrás létrehozása néhány másodpercet vesz igénybe. Ha elkészült, akkor megjelenik egy riasztás.

(Írhat egy [PowerShell-parancsfájl](app-insights-powershell-script-create-resource.md) erőforrás automatikus létrehozása.)

### <a name="getting-the-instrumentation-key"></a>A rendszerállapot-kulcs beolvasása
A instrumentation kulcs azonosítja az erőforrás, amelyet létrehozott. 

![Essentials kattintson, majd a Instrumentation kulcsot, a CTRL + C](./media/app-insights-separate-resources/02-props.png)

Az összes erőforrást, amelyre az alkalmazás elküldi az adatokat instrumentation kulcsok van szüksége.

## <a name="filter-on-build-number"></a>Szűrhet buildszám
Ha közzéteszi a az alkalmazás egy új verziója, érdemes tudni a telemetriai adatok és a különböző buildek elválasztása.

Beállíthatja az alkalmazás verziója tulajdonságát, hogy szűrheti [keresési](app-insights-diagnostic-search.md) és [metrika explorer](app-insights-metrics-explorer.md) eredmények.

![A tulajdonság szűrése](./media/app-insights-separate-resources/050-filter.png)

Nincsenek számos különböző módszer a Alkalmazásverzió tulajdonság beállítását.

* Közvetlenül beállítani:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* A sor burkolása egy [telemetriai inicializáló](app-insights-api-custom-events-metrics.md#defaults) annak érdekében, hogy példányainak TelemetryClient egységesen van beállítva.
* [AZ ASP.NET] A verzió beállítása `BuildInfo.config`. A modul felveszi a verziót az BuildLabel csomópontból. Ez a fájl tartalmazza a projekt, és ne feledje másolási mindig tulajdonságának beállítása a Solution Explorer.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [AZ ASP.NET] Automatikusan generálhat a BuildInfo.config MSBuild. Ehhez az szükséges, írjon be néhány sor számára a `.csproj` fájlt:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Ezt követően nevű fájlba *com.example.baidutest*. BuildInfo.config. A közzétételi folyamat nevezi át BuildInfo.config.

    A build címke tartalmaz egy helyőrző (AutoGen_...), a Visual Studio összeállításakor. De MSBuild-val készült, ha a telepítéskor a megfelelő verziószámot.

    Ahhoz, hogy a verziószámok létrehozásához MSBuild, állítsa be például a verzió `1.0.*` a AssemblyReference.cs

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

* [Több szerepkör megosztott erőforrások](app-insights-monitor-multi-role-apps.md)
* [Hozzon létre egy Telemetriai inicializáló segítségével különböztetheti meg egymástól A |} B Variant típusú adatok](app-insights-api-filtering-sampling.md#add-properties)
