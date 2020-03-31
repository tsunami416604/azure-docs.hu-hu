---
title: Az Azure Cloud Services-alkalmazások konvertálása Service Fabric-é
description: Ez az útmutató összehasonlítja a Cloud Services web- és feldolgozói szerepkörök és a Service Fabric állapotmentes szolgáltatások segítségével migrálni a Cloud Services service fabric.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: caf067f793ca2086bc068907e86a82266627d128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463343"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Útmutató a webes és feldolgozói szerepkörök szolgáltatásfabric állapotmentes szolgáltatásokká történő konvertálásához
Ez a cikk ismerteti, hogyan telepítheti át a Cloud Services webes és feldolgozói szerepkörök Service Fabric állapotmentes szolgáltatások. Ez a legegyszerűbb áttelepítési útvonal a Cloud Services-től a Service Fabric-ig olyan alkalmazások hoz, amelyek teljes architektúrája nagyjából ugyanaz marad.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Felhőszolgáltatás-projekt a Service Fabric alkalmazásprojektjéhez
 A Cloud Service-projekt és a Service Fabric-alkalmazás projekt hasonló szerkezetű, és mindkettő képviseli az alkalmazás központi telepítési egysége - azaz mindegyik határozza meg a teljes csomagot, amely telepítve van az alkalmazás futtatásához. A Cloud Service-projekt egy vagy több webes vagy feldolgozói szerepkört tartalmaz. Hasonlóképpen egy Service Fabric-alkalmazás projekt egy vagy több szolgáltatást tartalmaz. 

A különbség az, hogy a Cloud Service-projekt az alkalmazás üzembe helyezését egy virtuális gép központi telepítésével választja, és így tartalmazza a virtuális gép konfigurációs beállításait, míg a Service Fabric-alkalmazás projekt csak egy olyan alkalmazást határoz meg, amely egy meglévő virtuális gépek et egy Service Fabric-fürtben. Maga a Service Fabric-fürt csak egyszer van üzembe helyezve, akár egy Resource Manager-sablonon keresztül, akár az Azure Portalon keresztül, és több Service Fabric-alkalmazás is telepíthető.

![A Szolgáltatás fabric és a felhőszolgáltatások projektjának összehasonlítása][3]

## <a name="worker-role-to-stateless-service"></a>Feldolgozói szerepkör az állapotnélküli szolgáltatáshoz
Fogalmilag a feldolgozói szerepkör egy állapot nélküli számítási feladatot jelent, ami azt jelenti, hogy a munkaterhelés minden példánya azonos, és a kérelmek bármikor átirányíthatók bármely példányhoz. Minden példány nak nem kell megjegyeznie az előző kérést. Azt állítják, hogy a számítási feladatok működik egy külső állapottároló, például az Azure Table Storage vagy az Azure Cosmos DB által kezelt. A Service Fabric ilyen típusú számítási feladatok egy állapotmentes szolgáltatás képviseli. A feldolgozói szerepkör Service Fabric-be való migrálásának legegyszerűbb megközelítése a feldolgozói szerepkör kód állapotnélküli szolgáltatássá alakításával végezhető el.

![Feldolgozói szerepkör állapotnélküli szolgáltatáshoz][4]

## <a name="web-role-to-stateless-service"></a>Webes szerepkör az állapotnélküli szolgáltatáshoz
A feldolgozói szerepkörhöz hasonlóan a webes szerepkör is állapotnélküli számítási feladatot jelent, és így fogalmilag is leképezhető egy Service Fabric állapotmentes szolgáltatáshoz. A webes szerepköröktől eltérően azonban a Service Fabric nem támogatja az IIS szolgáltatást. Ahhoz, hogy egy webalkalmazást webes szerepkörből állapotmentes szolgáltatásba telepítsen, először olyan webes keretrendszerbe kell áttérni, amely saját üzemeltethető, és nem függ az IIS-től vagy a System.Web-től, például ASP.NET Core 1-től.

| **Alkalmazás** | **Támogatott** | **Áttelepítési útvonal** |
| --- | --- | --- |
| webes űrlapok ASP.NET |Nem |Konvertálás ASP.NET Core 1 MVC-vé |
| ASP.NET, MVC |Áttelepítéssel |Frissítés ASP.NET Core 1 MVC-re |
| ASP.NET, webes API |Áttelepítéssel |Saját üzemeltetésű kiszolgáló vagy ASP.NET Core 1 használata |
| ASP.NET 1. |Igen |N/A |

## <a name="entry-point-api-and-lifecycle"></a>Belépési pont API és életciklus
A feldolgozói szerepkör és a service fabric szolgáltatás API-jai hasonló belépési pontokat kínálnak: 

| **Belépési pont** | **Dolgozói szerepkör** | **Szolgáltatás fabric szolgáltatás** |
| --- | --- | --- |
| Feldolgozás |`Run()` |`RunAsync()` |
| Virtuális gép indítása |`OnStart()` |N/A |
| Virtuális gép leállítása |`OnStop()` |N/A |
| Figyelő megnyitása ügyfélkérelmekhez |N/A |<ul><li> `CreateServiceInstanceListener()`a stateless</li><li>`CreateServiceReplicaListener()`az állapotalapú</li></ul> |

### <a name="worker-role"></a>Dolgozói szerepkör
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

### <a name="service-fabric-stateless-service"></a>Szolgáltatásfabric állapotmentes szolgáltatás
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

Mindkettő rendelkezik egy elsődleges "Run" felülbírálása, amelyben a feldolgozás megkezdéséhez. A Service `Run`Fabric `Start`szolgáltatásai `Stop` egyetlen belépési `RunAsync`pontba, a . A szolgáltatásnak meg `RunAsync` kell kezdenie a `RunAsync` munkát, amikor elindul, és le kell állítania a munkát, ha a metódus CancellationToken jelzést kap. 

A feldolgozói szerepkörök és a Service Fabric-szolgáltatások életciklusa és élettartama között számos fő különbség van:

* **Életciklus:** A legnagyobb különbség az, hogy a feldolgozói szerepkör egy virtuális gép, és így az életciklusa a virtuális géphez van kötve, amely magában foglalja a virtuális gép indításakor és leállításakor eseményeket. A Service Fabric-szolgáltatás rendelkezik egy életciklus, amely elkülönül a virtuális gép életciklusa, így nem tartalmazza az eseményeket, amikor a gazdagép virtuális gép vagy a gép elindul, és leáll, mivel azok nem kapcsolódnak.
* **Élettartam:** A feldolgozói szerepkör példánya újrahasznosítja, ha a `Run` metódus kilép. A `RunAsync` metódus egy Service Fabric szolgáltatás azonban futtatható a befejezésig, és a szolgáltatáspéldány marad fel. 

A Service Fabric egy opcionális kommunikációs beállítási belépési pontot biztosít az ügyfélkéréseket figyelő szolgáltatásokhoz. Mind a RunAsync, mind a kommunikációs belépési pont opcionális felülbírálása a Service Fabric-szolgáltatásokban – a szolgáltatás dönthet úgy, hogy csak az ügyfélkérelmeket hallgatja, vagy csak egy feldolgozási hurkot futtat, vagy mindkettőt –, ezért a RunAsync metódus újraindítás nélkül kiléphet a szolgáltatáspéldányt, mert továbbra is figyelheti az ügyfélkérelmeket.

## <a name="application-api-and-environment"></a>Alkalmazás API és környezet
A Cloud Services-környezet API-t az aktuális virtuálisgép-példány, valamint a többi virtuálisgép-szerepkör példányok információkat és funkciókat biztosít. A Service Fabric a futtatóórával kapcsolatos információkat és a szolgáltatás jelenleg futó csomópontjára vonatkozó információkat tartalmazza. 

| **Környezeti feladat** | **Felhőszolgáltatások** | **Service Fabric** |
| --- | --- | --- |
| Konfigurációs beállítások és értesítés módosítása |`RoleEnvironment` |`CodePackageActivationContext` |
| Helyi tárhely |`RoleEnvironment` |`CodePackageActivationContext` |
| Végpont adatai |`RoleInstance` <ul><li>Jelenlegi példány:`RoleEnvironment.CurrentRoleInstance`</li><li>Egyéb szerepkörök és példányok:`RoleEnvironment.Roles`</li> |<ul><li>`NodeContext`az aktuális csomópontcímhez</li><li>`FabricClient`és `ServicePartitionResolver` a szolgáltatásvégpont-felderítéshez</li> |
| Környezeti emuláció |`RoleEnvironment.IsEmulated` |N/A |
| Egyidejű változási esemény |`RoleEnvironment` |N/A |

## <a name="configuration-settings"></a>Konfigurációs beállítások
A Felhőszolgáltatások konfigurációs beállításai egy virtuálisgép-szerepkörhöz vannak beállítva, és az adott virtuálisgép-szerepkör összes példányára vonatkoznak. Ezek a beállítások a ServiceConfiguration.*.cscfg fájlokban beállított kulcs-érték párok, és közvetlenül a RoleEnvironment-en keresztül érhetők el. A Service Fabric beállításai egyenként vonatkoznak az egyes szolgáltatásokra és az egyes alkalmazásokra, nem pedig egy virtuális gépre, mert a virtuális gép több szolgáltatást és alkalmazást is üzemeltethet. A szolgáltatás három csomagból áll:

* **Kód:** tartalmazza a szolgáltatás végrehajtható fájljait, bináris fájljait, DEL-jeit és minden más fájlt, amelyet a szolgáltatásnak futtatnia kell.
* **Konfiguráció:** a szolgáltatás összes konfigurációs fájlja és beállítása.
* **Adatok:** a szolgáltatáshoz társított statikus adatfájlok.

Ezek a csomagok egymástól függetlenül verziószámba vehethetők és frissíthetők. A Cloud Serviceshez hasonlóan a konfigurációs csomagok is programozott módon érhetők el egy API-n keresztül, és események érhetők el a konfigurációs csomagok módosításának értesítésére. A Settings.xml fájl az App.config fájl alkalmazásbeállítások szakaszához hasonló kulcsérték-konfigurációhoz és programozott hozzáféréshez használható. A Cloud Services szolgáltatással ellentétben azonban a Service Fabric konfigurációs csomag bármilyen konfigurációs fájlt tartalmazhat bármilyen formátumban, legyen az XML, JSON, YAML vagy egyéni bináris formátum. 

### <a name="accessing-configuration"></a>Hozzáférés a konfigurációhoz
#### <a name="cloud-services"></a>Cloud Services
A ServiceConfiguration.*.cscfg konfigurációs beállításai `RoleEnvironment`a . Ezek a beállítások globálisan elérhetők az azonos felhőszolgáltatás-telepítésben lévő összes szerepkörpéldány számára.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Minden szolgáltatás saját egyéni konfigurációs csomaggal rendelkezik. Nincs beépített mechanizmus a globális konfigurációs beállításokhoz, amelyeket a fürt összes alkalmazása elérhető. Ha a Service Fabric speciális Settings.xml konfigurációs fájlját használja egy konfigurációs csomagon belül, a Settings.xml fájl értékei felülírhatók az alkalmazás szintjén, így az alkalmazásszintű konfigurációs beállítások lehetségesek.

A konfigurációs beállítások az egyes szolgáltatáspéldányokon belül a szolgáltatáson keresztül érhetők `CodePackageActivationContext`el.

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

### <a name="configuration-update-events"></a>Konfigurációs frissítési események
#### <a name="cloud-services"></a>Cloud Services
Az `RoleEnvironment.Changed` esemény segítségével értesíti az összes szerepkörpéldányt, ha változás történik a környezetben, például egy konfigurációs változás. Ez a konfigurációs frissítések felhasználására szolgál a szerepkörpéldányok újrahasznosítása vagy a munkavégző folyamat újraindítása nélkül.

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
A szolgáltatás ban található három csomagtípus – a Kód, a Konfiguráció és az Adatok – mind a három csomagtípusnak olyan eseményekkel rendelkezik, amelyek értesítik a szolgáltatáspéldányt, amikor egy csomagot frissítenek, hozzáadnak vagy eltávolítanak. Egy szolgáltatás minden típusból több csomagot is tartalmazhat. Egy szolgáltatás például több konfigurációs csomaggal is rendelkezhet, amelyek mindegyike egyenként verziózott és frissíthető. 

Ezek az események a szolgáltatáspéldány újraindítása nélkül is felhasználhatók a szolgáltatáscsomagok változásainak felhasználásához.

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
Az indítási feladatok olyan műveletek, amelyeket az alkalmazás indítása előtt hajtanak végre. Az indítási feladat általában emelt szintű jogosultságokkal történő beállítási parancsfájlok futtatására szolgál. A Cloud Services és a Service Fabric egyaránt támogatja az indítási feladatokat. A fő különbség az, hogy a Cloud Services,egy indítási feladat van kötve egy virtuális gép, mert egy szerepkörpéldány része, míg a Service Fabric egy indítási feladat van kötve egy szolgáltatás, amely nem kötődik egy adott virtuális gép.

| Service Fabric | Cloud Services |
| --- | --- |
| Konfiguráció helye |ServiceDefinition.csdef |
| Jogosultságok |"korlátozott" vagy "emelkedett" |
| Alkalmazás-előkészítés |"egyszerű", "háttér", "előtér" |

### <a name="cloud-services"></a>Cloud Services
A Cloud Services szolgáltatásban egy indítási belépési pont van konfigurálva szerepkörenként a ServiceDefinition.csdef-ben. 

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
A Service Fabric fájlban szolgáltatásonként van konfigurálva egy indítási belépési pont a ServiceManifest.xml fájlban:

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

## <a name="a-note-about-development-environment"></a>Megjegyzés a fejlesztési környezetről
A Cloud Services és a Service Fabric egyaránt integrálva van a Visual Studióba projektsablonokkal, valamint a helyi és az Azure-beli hibakeresés, konfigurálás és üzembe helyezés támogatásával. A Cloud Services és a Service Fabric is helyi fejlesztési futásidejű környezetet biztosít. A különbség az, hogy míg a Cloud Service fejlesztési futásidejű emulálja az Azure-környezet, amelyen fut, a Service Fabric nem használ emulátort – a teljes Service Fabric-futási időt használja. A Service Fabric-környezet a helyi fejlesztői gépen futtatott ugyanaz a környezet, amely éles környezetben fut.

## <a name="next-steps"></a>További lépések
Tudjon meg többet a Service Fabric megbízható szolgáltatások és az alapvető különbségek a Cloud Services és a Service Fabric-alkalmazás architektúra, hogyan használhatja ki a Service Fabric-funkciók teljes készletét.

* [A Service Fabric megbízható szolgáltatásainak első lépései](service-fabric-reliable-services-quick-start.md)
* [Fogalmi útmutató a Felhőszolgáltatások és a Service Fabric közötti különbségekről](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
