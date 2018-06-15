---
title: Azure Cloud Services alkalmazások átalakítása mikroszolgáltatások |} Microsoft Docs
description: Ez az útmutató a felhőalapú szolgáltatások webes és feldolgozói szerepkörök és a Service Fabric állapotmentes szolgáltatások Felhőszolgáltatások telepítenek át a Service Fabric hasonlítja össze.
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
ms.openlocfilehash: c6bdd6f88c9008a8d9c15d22bdcf263190424649
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206682"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Útmutató a Service Fabric állapotmentes szolgáltatások webes és feldolgozói szerepkörök alakítása
A cikkből megtudhatja, hogyan telepíthetők át a felhőalapú szolgáltatások webes és feldolgozói szerepkörök a Service Fabric állapotmentes szolgáltatásokhoz. Ez az a legegyszerűbb áttelepítési út a felhőalapú szolgáltatások a Service Fabric az alkalmazások, amelyek általános architektúrája érintetlen marad többé-kevésbé megegyezik.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>A Service Fabric-alkalmazás projekt felhőszolgáltatás-projekt
 Egy Felhőszolgáltatás-projektet és a Service Fabric-alkalmazás projekt hasonló struktúrával rendelkezik, és mindkét jelentik a telepítési egység, ez azt jelenti, hogy minden egyes alkalmazás - meghatározásához futtassa az alkalmazást a rendszer a teljes csomag. Egy Felhőszolgáltatás-projekt tartalmazza a legalább egy webes vagy feldolgozói szerepköröket. Hasonlóképpen a Service Fabric-alkalmazás projekt tartalmaz egy vagy több szolgáltatás. 

A különbség az, hogy a Felhőszolgáltatás-projekt párok az alkalmazás központi telepítése egy virtuális gép üzembe helyezéséhez, és így tartalmazza a virtuális gép konfigurációs beállításai, mivel a Service Fabric-alkalmazás projekt csak egy telepített alkalmazás a Service Fabric-fürt a meglévő virtuális gépeken. A Service Fabric-fürt maga csak a rendszer egyszer, egy Resource Manager-sablon vagy az Azure-portálon keresztül, és több Service Fabric-alkalmazások telepíthetők.

![A Service Fabric és a Felhőszolgáltatások projekt összehasonlítása][3]

## <a name="worker-role-to-stateless-service"></a>Az állapotmentes szolgáltatások feldolgozói szerepkör
Elméleti szinten a feldolgozói szerepkör jelöli egy állapot nélküli alkalmazások és szolgáltatások, azaz a munkaterhelés minden példánya azonos és a kérelmek bármikor átirányítható tetszőleges példányra. Minden példány jegyezze meg az előző kérést nem várt. Külső állapottárolóhoz, például az Azure Table Storage vagy Azure Document DB rendszerbe, amely az alkalmazások és szolgáltatások állapotát kezeli. A Service Fabric alkalmazások és szolgáltatások az ilyen típusú állapotmentes szolgáltatások helyőrző jelzi. A legegyszerűbb módja a feldolgozói szerepkör Service Fabric áttelepíti egy állapotmentes szolgáltatások feldolgozói szerepkör kódját alakításával végezhető.

![Az állapotmentes szolgáltatások feldolgozói szerepkör][4]

## <a name="web-role-to-stateless-service"></a>Webes szerepkör állapotmentes szolgáltatások
Feldolgozói szerepkör hasonló, a webes szerepkör is képviseli egy állapot nélküli alkalmazások és szolgáltatások, és így fogalmilag azt túl képezhető le a Service Fabric állapotmentes szolgáltatások. Azonban eltérően webes szerepkörök a Service Fabric nem támogatja az IIS. Telepítse át a webes alkalmazás a webes szerepkör állapotmentes szolgáltatások megköveteli a egy webes keretrendszer, amely önállóan üzemel, és nem függ az IIS vagy System.Web, például az ASP.NET Core 1 első áthelyezését.

| **Alkalmazás** | **Támogatott** | **Áttelepítési útvonal** |
| --- | --- | --- |
| Az ASP.NET Web Forms keretrendszerre |Nem |Az ASP.NET Core 1 MVC átalakítása |
| ASP.NET MVC |Az áttelepítés |Frissítés az ASP.NET Core 1 MVC |
| ASP.NET Web API |Az áttelepítés |Üzemeltetett önálló kiszolgáló vagy az ASP.NET Core 1 |
| Az ASP.NET Core 1 |Igen |– |

## <a name="entry-point-api-and-lifecycle"></a>Belépési pont API és életciklusa
Feldolgozói szerepkör és a Service Fabric szolgáltatás az API-k ajánlat hasonló belépési pontok: 

| **A belépési pont** | **Feldolgozói szerepkör** | **Service Fabric-szolgáltatás** |
| --- | --- | --- |
| Feldolgozás |`Run()` |`RunAsync()` |
| Virtuális gép indítása |`OnStart()` |– |
| Virtuális gép leállítása |`OnStop()` |– |
| Nyissa meg figyelő az ügyféli kérelmek részére |– |<ul><li> `CreateServiceInstanceListener()` az állapot nélküli</li><li>`CreateServiceReplicaListener()` az állapotalapú alkalmazások és szolgáltatások</li></ul> |

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

### <a name="service-fabric-stateless-service"></a>Service Fabric állapotmentes szolgáltatások
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

Egy elsődleges "Futtatás" felülbírálás feldolgozási kezdőpontjaként egyaránt rendelkezik. A Service Fabric szolgáltatások egyesítése `Run`, `Start`, és `Stop` egy-egy belépési pont, a `RunAsync`. A szolgáltatás működik, ha használatba `RunAsync` elindul, és le kell állnia, ha működik a `RunAsync` metódus CancellationToken leállítási jelzést kapott. 

Az életciklus és feldolgozói szerepkörök és a Service Fabric szolgáltatás élettartama között számos fontos különbség van:

* **Életciklus:** a legnagyobb különbség az, hogy a feldolgozói szerepkör egy virtuális Gépet, és így a virtuális gép, ha a virtuális gép indítása és leállítása eseményeket tartalmazó életciklus kötődik. A Service Fabric-szolgáltatás egy életciklussal, amely nem a virtuális gép életciklusát, ezért nem tartalmazza a események a gazdagép, VM vagy a gép indítása és leállítása, mert nem áll(nak) kapcsolatban van.
* **Élettartam:** A feldolgozói szerepkör példánya indul, ha a `Run` metódus kilép. A `RunAsync` metódus a Service Fabric-szolgáltatás azonban is végrehajtása, és a szolgáltatáspéldány fel maradnak. 

A Service Fabric egy választható kommunikációs telepítő belépési pontot nyújt az ügyfélkérelmek szolgáltatások. A RunAsync és a kommunikáció belépési pont a Service Fabric szolgáltatások – a szolgáltatás dönthetnek úgy, hogy az ügyfelek kéréseire csak figyelje, illetve csak futtassa egy feldolgozási ciklus, vagy mindkettőt -, ezért a RunAsync metódusában engedélyezett újraindítása nélküli kilépéshez választható felülbírálások a szolgáltatás példány, mert a figyelő az ügyféli kérelmek részére továbbra is.

## <a name="application-api-and-environment"></a>Alkalmazás API és a környezet
A Cloud Services környezet API információkat és az aktuális Virtuálisgép-példány, valamint a más VM szerepkörpéldányokat kapcsolatos információkat biztosít a. A Service Fabric a futásidejű kapcsolatos információkat tartalmaz, és némi információt a csomópont szolgáltatásként fut rajta. 

| **Környezet feladat** | **Felhőszolgáltatások** | **Service Fabric** |
| --- | --- | --- |
| A konfigurációs beállítások és módosítási értesítés |`RoleEnvironment` |`CodePackageActivationContext` |
| Helyi tároló |`RoleEnvironment` |`CodePackageActivationContext` |
| Végpont |`RoleInstance` <ul><li>Jelenlegi példány: `RoleEnvironment.CurrentRoleInstance`</li><li>Egyéb szerepköröket és példány: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` az aktuális csomópont-címe</li><li>`FabricClient` és `ServicePartitionResolver` a szolgáltatási végpont felderítése</li> |
| Az emuláció környezet |`RoleEnvironment.IsEmulated` |– |
| Egyidejű esemény |`RoleEnvironment` |– |

## <a name="configuration-settings"></a>Konfigurációs beállítások
Konfigurációs beállításai a Felhőszolgáltatások Virtuálisgép-szerepkör állítja be, és a Virtuálisgép-szerepkör minden példányára vonatkoznak. Ezek a beállítások olyan ServiceConfiguration.*.cscfg fájlok beállított kulcs-érték párok, és segítségével érhető el közvetlenül roleenvironment-et. A Service Fabric beállítások érvényesek külön-külön minden szolgáltatáshoz, és mindegyik alkalmazás, nem pedig egy virtuális géphez, mert egy virtuális Gépet, rendelkezhet több szolgáltatásokat és alkalmazásokat. A szolgáltatás három csomagok áll:

* **Kód:** a szolgáltatás végrehajtható fájlok, bináris fájljai, dll-EK és a szolgáltatás futtatásához szükséges egyéb fájlokat tartalmazza.
* **A Config:** összes konfigurációs fájlokat és a szolgáltatás beállításait.
* **Adatok:** a szolgáltatáshoz társított statikus adatfájlokat.

Ezeket a csomagokat mindegyikének lehet függetlenül rendszerverzióval ellátott és frissített. Cloud Services hasonló, a konfigurációs csomag programozott módon a következőkkel érhetők el az API-k és események állnak rendelkezésre a szolgáltatás konfigurációs csomag változás értesíteni. A Settings.xml fájlban, mind az alkalmazás beállításainak szakaszában az App.config fájl hasonló programozott hozzáférés kulcs-érték használható. Azonban eltérően Felhőszolgáltatásokat, a Service Fabric config csomag bármely konfigurációs fájlokat tartalmazza tetszőleges méretű XML, JSON, YAM vagy egyéni bináris formátumot. 

### <a name="accessing-configuration"></a>Konfigurációs elérése
#### <a name="cloud-services"></a>Cloud Services
Konfigurációs beállítások ServiceConfiguration.*.cscfg keresztül elérhető `RoleEnvironment`. Ezek a beállítások globálisan elérhetők összes szerepkörpéldányt azonos Cloud Service-környezetben.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Minden szolgáltatás van a saját egyéni konfigurációs csomagot. Nincs olyan beépített mechanizmus globális konfigurációs beállítások érhető el a fürt valamennyi alkalmazás. A Service Fabric különleges Settings.xml konfigurációs fájl belül a konfigurációs csomag használata esetén a Settings.xml értékek írhatja felül az alkalmazás szintjén, amely lehetővé teszi az alkalmazás-konfigurációs beállítások.

Konfigurációs beállítások állnak belül minden szolgáltatáspéldány, a szolgáltatáson keresztül fér hozzá `CodePackageActivationContext`.

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

### <a name="configuration-update-events"></a>A Configuration (update) események
#### <a name="cloud-services"></a>Cloud Services
A `RoleEnvironment.Changed` eseménnyel összes szerepkörpéldányt értesítése, ha olyan változás esetén a környezetben, például a konfiguráció módosítása. Ennek segítségével konfigurációfrissítések felhasználása nélkül újrahasznosítási szerepkörpéldányt beállítani, vagy indítsa újra a munkavégző folyamat.

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
Egy szolgáltatás - kódot, konfiguráció és adatok - három csomag típusa rendelkező események, amelyek tájékoztatást adnak a szolgáltatáspéldány, a csomag frissítése, hozzáadásakor vagy eltávolításakor. Egy szolgáltatás minden típusú több csomagot tartalmazhat. Előfordulhat például, hogy szolgáltatás több konfigurációs csomagot, minden egyes külön-külön rendszerverzióval ellátott és bővíthető. 

Ezek az események felhasználásához szolgáltatáscsomagok változásairól a szolgáltatáspéldány újraindítása nélkül érhetők el.

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
Indítási feladatokat is az alkalmazás indítása előtt végzett műveleteket. Egy indítási tevékenységhez általában emelt szintű jogosultságokkal telepítő parancsfájlok futtatásához használt. Cloud Services és a Service Fabric támogatja a kezdeti feladatok. A fő különbség, hogy a felhőalapú szolgáltatások indítási feladat van kötve egy virtuális gép már része egy szerepkörpéldányt, mert mivel a Service Fabric egy indítási feladat a szolgáltatást, amely nem kötődik azonban bármely adott virtuális géphez van kötve.

| Service Fabric | Cloud Services |
| --- | --- | --- |
| Konfiguráció helye |ServiceDefinition.csdef |
| Jogosultságok |"korlátozott" vagy "emelt szintű" |
| Alkalmazás-előkészítés |"egyszerű", "Háttér", "előtér" |

### <a name="cloud-services"></a>Cloud Services
Cloud Services egy indítási belépési pont úgy van konfigurálva, a ServiceDefinition.csdef szerepkör /. 

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
A Service Fabric egy indítási belépési pont úgy van konfigurálva, a ServiceManifest.xml-szolgáltatás esetében:

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

## <a name="a-note-about-development-environment"></a>Fejlesztői környezet Megjegyzés
Cloud Services és a Service Fabric vannak integrálva a Visual Studio projektsablonjai, és támogatja a hibakeresés konfigurálását, és a helyi és központi telepítése mind az Azure-bA. Cloud Services és a Service Fabric is adja meg a helyi futtatási környezet. Az különbség, hogy a Felhőszolgáltatás fejlesztői futtatókörnyezetet az Azure környezetbe, az azt futtató emulálja, amíg a Service Fabric nem használja az emulátor – a teljes Service Fabric-futtatókörnyezet fogja használni. A Service Fabric futtatja a helyi fejlesztési számítógépén környezete ugyanabban a környezetben, amely a termelésben futtatja.

## <a name="next-steps"></a>További lépések
További információk a Service Fabric Reliable Services és a Cloud Services és a Service Fabric-alkalmazás architektúra annak megértése, hogyan előnyeit a Service Fabric-szolgáltatások teljes készletének alapvető eltérései.

* [Bevezetés a Service Fabric Reliable Services használatába](service-fabric-reliable-services-quick-start.md)
* [Cloud Services és a Service Fabric közötti különbségekről fogalmi útmutató](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
