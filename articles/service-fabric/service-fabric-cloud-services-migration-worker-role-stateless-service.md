---
title: Konvertálja az Azure Cloud Services, Service Fabric-alkalmazások |} A Microsoft Docs
description: Ez az útmutató a Cloud Services webes és feldolgozói szerepkörök és a Service Fabric állapotmentes szolgáltatások az áttelepítés a Cloud Servicesből a Service Fabric segítségével hasonlítja össze.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 5880ebb3-8b54-4be8-af4b-95a1bc082603
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: f23f29d15c4c8f05551b20d42b92dda5632cde08
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58078737"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Útmutató a webes és feldolgozói szerepkörök a Service Fabric állapotmentes szolgáltatások alakítása
Ez a cikk ismerteti, hogyan telepítheti át a Cloud Services webes és feldolgozói szerepkörök a Service Fabric állapotmentes szolgáltatások. Ez az a legegyszerűbb áttelepítési út a Cloud Servicesből a Service Fabric alkalmazásokhoz, amelyek általános architektúrát fog maradni nagyjából azonos.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>A Service Fabric-projektet felhőszolgáltatás-projekt
 Egy Cloud Service-projekt és a egy Service Fabric-alkalmazás projekt hasonló struktúrával rendelkezik, és mindkét jelölik az alkalmazás -, ezek mindegyike a központi telepítési egység határozza meg a teljes csomag, amelyet az alkalmazás futtatásához. Egy Felhőszolgáltatás-projekt tartalmazza a webes vagy feldolgozói szerepkörök. Hasonlóképpen egy Service Fabric-alkalmazás projektet tartalmaz egy vagy több szolgáltatás. 

A különbség az, hogy a Felhőszolgáltatás-projekt-párok az alkalmazás telepítése egy virtuális gép üzembe helyezése, és így tartalmazza a virtuális gép konfigurációs beállításait, mivel a Service Fabric-alkalmazás projekt csak egy telepített alkalmazás meglévő virtuális gépek a Service Fabric-fürtön. Van a Service Fabric-fürthöz csak egyszer, rendszerbe, Resource Manager-sablonok vagy az Azure Portalon keresztül, és több Service Fabric-alkalmazások telepíthetők.

![A Service Fabric és Cloud Services, project összehasonlítása][3]

## <a name="worker-role-to-stateless-service"></a>Feldolgozói szerepkör állapotmentes szolgáltatásnak
Elméleti szinten feldolgozói szerepkör jelöli az állapot nélküli munkaterhelés, azaz a számítási feladat összes példánya azonos és a kérések átirányítható bármelyik példányra bármikor. Minden példány várhatóan nem ne felejtse el az előző kérelem. Egy külső állapot tárolásának, például az Azure Table Storage vagy Azure dokumentum-adatbázis, amely a számítási feladatok állapotát kezeli. A Service Fabric az ilyen típusú számítási feladatok állapotmentes szolgáltatás által jelölt. A feldolgozói szerepkör Service Fabric-ba való migrálás legegyszerűbb módja a feldolgozói szerepkör kódját áttérve egy állapotmentes szolgáltatás teheti meg.

![Feldolgozói szerepkör állapotmentes szolgáltatásnak][4]

## <a name="web-role-to-stateless-service"></a>Az állapotmentes szolgáltatás a webes szerepkör
Feldolgozói szerepkör hasonlóan egy webes szerepkörben is jelenti állapot nélküli számítási feladatok, és így elméletben ez túl is le lehet képezni egy Service Fabric állapotmentes szolgáltatás. Azonban ellentétben a webes szerepkörök esetében a Service Fabric nem támogatja az IIS. Áttelepíteni a webes alkalmazás a webes szerepkör egy állapotmentes szolgáltatás szükséges egy webes keretrendszer, amely a saját üzemeltetésű, és nem függ az IIS vagy System.Web, például az ASP.NET Core 1 első áthelyezése.

| **Alkalmazás** | **Támogatott** | **Áttelepítés** |
| --- | --- | --- |
| ASP.NET Web Forms |Nem |1. az ASP.NET Core MVC átalakítása |
| ASP.NET, MVC |A Migrálást |Frissítés ASP.NET Core 1 MVC |
| ASP.NET, webes API |A Migrálást |Használja a helyi kiszolgáló vagy az ASP.NET Core-1 |
| ASP.NET Core 1 |Igen |– |

## <a name="entry-point-api-and-lifecycle"></a>Belépési pont API- és életciklus
Feldolgozói szerepkör és a Service Fabric-szolgáltatás API-k ajánlat hasonló belépési pontok: 

| **Belépési pont** | **Feldolgozói szerepkör** | **Service Fabric-szolgáltatás** |
| --- | --- | --- |
| Feldolgozás |`Run()` |`RunAsync()` |
| Virtuális gép indítása |`OnStart()` |– |
| Virtuális gép leállítása |`OnStop()` |– |
| Nyissa meg figyelő az ügyféli kérelmek részére |– |<ul><li> `CreateServiceInstanceListener()` az állapot nélküli</li><li>`CreateServiceReplicaListener()` az állapotalapú</li></ul> |

### <a name="worker-role"></a>Feldolgozói szerepkör
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>A Service Fabric állapotmentes szolgáltatás
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Egy elsődleges "Futtatás" felülbírálást, amelyben a feldolgozás megkezdése is rendelkezik. A Service Fabric-szolgáltatások egyesítése `Run`, `Start`, és `Stop` be egy adott belépési pontot `RunAsync`. A munka mikor kezdő `RunAsync` elindul, és kell működni, ha a `RunAsync` metody CancellationToken leállítási jelzést kapott. 

Az életciklus és feldolgozói szerepkörök és a Service Fabric services élettartama közötti számos fontos különbség van:

* **Életciklus:** A legnagyobb különbség az, hogy egy feldolgozói szerepkör egy virtuális Gépet, és a életciklus úgy vannak kötve, a virtuális gép, amely tartalmazza az eseményeket, amikor a virtuális gép indítása és leállítása a. Service Fabric-szolgáltatás egy életciklussal, amely nem azonos azzal a virtuális gép életciklus, ezért nem tartalmaz eseményeket, ha a gazdagép, virtuális gép vagy a gép elindul, és állítsa le, mert nem áll(nak) rendelkezik.
* **Élettartam:** Feldolgozói szerepkörpéldányok újraindítása lesz, ha a `Run` metódus kilép. A `RunAsync` metódus a Service Fabric-szolgáltatás befejezését azonban futtathatja, és a szolgáltatáspéldány be fog maradni. 

A Service Fabric egy választható kommunikációs telepítő belépési pontot nyújt az ügyfélkérelmek szolgáltatásokhoz. A RunAsync és a kommunikáció belépési pont a Service Fabric services – a szolgáltatás is választhat, csak figyelik az ügyfelektől érkező kérelmeket, vagy csak futtatni egy feldolgozási ciklus és / vagy -, ezért a RunAsync metódusában engedélyezett újraindítása nélkül kilép választható felülbírálások a szolgáltatás-példányt, mert előfordulhat, hogy továbbra is az ügyfél kérelmek figyelésére.

## <a name="application-api-and-environment"></a>Alkalmazás API-t és a környezet
A Cloud Services környezet API információkat és az aktuális Virtuálisgép-példány, valamint a többi virtuális gép szerepkörpéldányainak funkciókat biztosít. A Service Fabric a futtatókörnyezet kapcsolatos információkat tartalmaz, és némi információt a csomópont egy szolgáltatás fut rajta. 

| **Környezet feladat** | **Felhőszolgáltatások** | **Service Fabric** |
| --- | --- | --- |
| A konfigurációs beállítások és a módosítási értesítés |`RoleEnvironment` |`CodePackageActivationContext` |
| Helyi tároló |`RoleEnvironment` |`CodePackageActivationContext` |
| Végpont |`RoleInstance` <ul><li>A jelenlegi példány: `RoleEnvironment.CurrentRoleInstance`</li><li>Más szerepkörök és példányok: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` az aktuális csomópont címe</li><li>`FabricClient` és `ServicePartitionResolver` a szolgáltatási végpont felderítése</li> |
| Környezet Emulation technológiával |`RoleEnvironment.IsEmulated` |– |
| Egyidejű esemény |`RoleEnvironment` |– |

## <a name="configuration-settings"></a>Konfigurációs beállítások
Konfigurációs beállításai a Cloud Services Virtuálisgép-szerepkör vannak beállítva, és a alkalmazni a Virtuálisgép-szerepkör az összes példányát. Ezek a beállítások kulcs-érték párok ServiceConfiguration.*.cscfg fájlokban állítsa be, és közvetlenül RoleEnvironment keresztül érhetők el. A Service Fabric segítségével a beállítások vonatkoznak külön-külön minden szolgáltatáshoz, és mindegyik alkalmazás helyett egy virtuális géphez, mert a virtuális gép több szolgáltatásokat és alkalmazásokat is üzemeltethet. A szolgáltatás három csomagot tevődik össze:

* **Kód:** a szolgáltatás végrehajtható fájlok, bináris fájljai, dll-EK és a szolgáltatás futtatásához szükséges egyéb fájlokat tartalmazza.
* **Config:** konfigurációs fájlokat és a egy szolgáltatás beállításait.
* **Adatok:** a szolgáltatáshoz társított statikus adatfájlok.

Ezeket a csomagokat mindegyike külön rendszerverzióval ellátott és frissített lehet. Cloud Serviceshez hasonlóan, a konfigurációs csomag programozott módon keresztül elérhető API-k és események állnak rendelkezésre a szolgáltatás a konfigurációs csomag megváltoztatása értesíteni. Konfigurációs kulcs-érték és a egy App.config fájl alkalmazás beállítások szakaszában hasonló programozás alapú hozzáférést Settings.xml fájl használható. Azonban ellentétben a Cloud Services, a Service Fabric-konfigurációs csomag bármely konfigurációs fájlokat tartalmazza tetszőleges méretű-e, XML, JSON, YAML vagy egyéni bináris formátumot. 

### <a name="accessing-configuration"></a>Konfiguráció elérése
#### <a name="cloud-services"></a>Cloud Services
Konfigurációs beállítások ServiceConfiguration.*.cscfg keresztül érhetők el `RoleEnvironment`. Ezek a beállítások világszerte elérhetők az összes szerepkörpéldány ugyanazon Cloud Service-környezetben.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Mindegyiknek megvannak a saját egyéni konfigurációs csomag feltöltése. Nincs nincs beépített mechanizmus a globális konfigurációs beállításoknak az elérhető összes alkalmazás egy fürtben. A Service Fabric speciális Settings.xml konfigurációs fájlt a konfigurációs csomagon belül használatakor Settings.xml szereplő értékek felülírható legyen az alkalmazás szintjén, amely lehetővé teszi az alkalmazás-szintű konfigurációs beállítások.

Konfigurációs beállítások is hozzáfér a szolgáltatás minden szolgáltatáspéldány belül `CodePackageActivationContext`.

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>Configuration update események
#### <a name="cloud-services"></a>Cloud Services
A `RoleEnvironment.Changed` eseménnyel az összes szerepkörpéldány értesítése, ha változás történik a környezetben, például a konfiguráció módosítása. Ez szolgál a konfigurációfrissítések lefoglalhatja a szerepkörpéldányok újrahasznosítás vagy egy munkavégző folyamat újraindítása nélkül.

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
A - Code, Config és az adatok - szolgáltatáson három csomagtípusok mindegyike rendelkezik eseményeket, amelyek egy szolgáltatáspéldány értesítése, ha a csomag frissítése, hozzáadva vagy eltávolítva. A szolgáltatás minden típusú több csomag is tartalmazhat. Egy szolgáltatás Előfordulhat például, több konfigurációs csomag, minden egyes külön-külön rendszerverzióval ellátott és bővíthető. 

Ezek az események felhasználásához szolgáltatáscsomagok változásai a szolgáltatáspéldány újraindítása nélkül érhetők el.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Indítási feladatok
Indítási feladatok olyan műveletek, amelyeket a rendszer egy alkalmazás indítása előtt. Egy indítási feladat általában emelt szintű jogosultságokkal a telepítő szkriptek futtatásához használt. A Cloud Services és a Service Fabric támogatja az indítási feladatok. A fő különbség, hogy a Cloud Services, egy indítási feladat vannak kötve, egy virtuális Gépet egy szerepkörpéldány része, mert ezzel szemben a Service Fabric egy indítási feladat egy szolgáltatás, amely nem kötődik bármely adott virtuális gép van kötve.

| Service Fabric | Cloud Services |
| --- | --- |
| Konfigurációs hely |ServiceDefinition.csdef |
| Jogosultságok |"korlátozott" vagy "emelt szintű" |
| Alkalmazás-előkészítés |"egyszerű", "Háttér", "előtér" |

### <a name="cloud-services"></a>Cloud Services
A Cloud Services egy indítási belépési pont úgy van konfigurálva a ServiceDefinition.csdef szerepkörönként. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
A Service Fabric egy indítási belépési pont úgy van beállítva, a ServiceManifest.xml szolgáltatásonként:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>Megjegyzés a fejlesztési környezet
A Cloud Services és a Service Fabric vannak integrálva a Visual Studio projektsablonjai és támogatja a hibakeresés, konfigurálását és a fentiekben helyileg és az Azure-bA. A Cloud Services és a Service Fabric is adja meg a futtatókörnyezet helyi fejlesztési környezetet. A különbség az, hogy a Felhőszolgáltatás fejlesztői futtatókörnyezet az azt futtató Azure-környezet emulálja, amíg a Service Fabric nem használja az emulátor – Ez a teljes Service Fabric-futtatókörnyezet. A Service Fabric-környezetben helyi fejlesztői gépen futtatja ugyanabban a környezetben, amely futtatja az üzemi.

## <a name="next-steps"></a>További lépések
További információk a Service Fabric Reliable Services és a Cloud Services és a Service Fabric-alkalmazás architektúra megismerése a Service Fabric-szolgáltatások teljes körű kihasználása alapvető különbségeit.

* [Bevezetés a Service Fabric Reliable Services használatába](service-fabric-reliable-services-quick-start.md)
* [Fogalmi útmutató a Cloud Services és a Service Fabric közötti különbségek](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
