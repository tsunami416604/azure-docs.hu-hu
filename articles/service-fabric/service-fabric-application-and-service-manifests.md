---
title: Ismertető az Azure Service Fabric-alkalmazások és szolgáltatások |} A Microsoft Docs
description: Ismerteti, hogyan történik a jegyzékek írja le a Service Fabric-alkalmazások és szolgáltatások használata.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/19/2018
ms.author: ryanwi
ms.openlocfilehash: 9e8ca50970ff4a845174d7061b60a88a8f5ce578
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465623"
---
# <a name="service-fabric-application-and-service-manifests"></a>Service Fabric-alkalmazás- és szolgáltatásjegyzékek
Ez a cikk bemutatja, hogyan történik a Service Fabric-alkalmazásokat és szolgáltatásokat, hogy meghatározott és verziószámmal ApplicationManifest.xml és ServiceManifest.xml fájlokat használja.  További részletes példák: [alkalmazás és a service manifest példák](service-fabric-manifest-examples.md).  Ezek a jegyzékfájlok XML-séma leírása itt található [ServiceFabricServiceModel.xsd séma dokumentáció](service-fabric-service-model-schema.md).

> [!WARNING]
> A jegyzékfájl XML-fájl séma kikényszeríti a gyermekelemek helyes sorrendje.  Részleges Áthidaló megoldásként nyissa meg a "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd" a Visual Studio szerzői vagy bármely, a Service Fabric jegyzékek módosítása közben. A és lehetővé teszi, hogy ellenőrizze az eredménykészlet sorrendjét gyermekelemek intelli-érzékelő.

## <a name="describe-a-service-in-servicemanifestxml"></a>Egy szolgáltatás a ServiceManifest.xml leírása
A szolgáltatásjegyzék deklaratív szolgáltatás típusa és verziója határozza meg. Azt adja meg a szolgáltatás metaadatokat, például a szolgáltatás típusának, egészségügyi tulajdonságok, terheléselosztási metrikák, bináris fájljait és konfigurációs fájlok.  Másképp fogalmazva, a kód, a konfiguráció és az adatok csomagok egy szolgáltatási csomagot, amely egy vagy több szolgáltatás típusok támogatása az alkotó ismerteti. A szolgáltatásjegyzék több kódot, konfiguráció és adatok csomagokat, amelyek egymástól függetlenül lehet rendszerverzióval ellátott is tartalmazhat. Itt van az ASP.NET Core webes kezelőfelületi szolgáltatás a szolgáltatás jegyzékfájl a [mintául szolgáló Szavazóalkalmazás](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (és néhány [részletesebb példákat](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

**Verzió** attribútumok strukturálatlan karakterláncok és a rendszer nem elemzi. Verzió attribútumok használt verzió az egyes összetevők frissítéseket.

**ServiceTypes** deklarálja, hogy milyen szolgáltatástípusok által támogatott **CodePackages** a jegyzékfájlban. Amikor egy szolgáltatás létrejön az említett szolgáltatás ellen, a jegyzékfájlban deklarált összes kódcsomagok a rendszer a belépési pontok futtatásával aktiválja. Az amiatt végbemenő folyamatok várhatóan a támogatott szolgáltatástípusok regisztrálja a futási időben. Szolgáltatástípusok deklarált a jegyzékfájl szintjén, és nem a kód szintű csomag. Ezért ha több kódot csomag, az összes aktiválás, amikor a rendszer figyeli a nem a deklarált szolgáltatástípusok közül bármelyik.

A megadott végrehajtható fájl **EntryPoint** általában a hosszan futó szolgáltatásgazda van. **SetupEntryPoint** egy emelt szintű belépési pontja, amely ugyanazokat a hitelesítő adatokat, mint a Service Fabric-fut (általában a *LocalSystem* fiók) bármilyen egyéb belépési pont előtt.  Egy külön szolgáltatásbeállítás belépési pontjának jelenléte elkerülhető, hogy nem kell futtatni a szolgáltatásgazda magas szintű jogosultságokkal rendelkező huzamosabb ideig. A megadott végrehajtható fájl **EntryPoint** futtatása **SetupEntryPoint** sikeresen kilép. Ha a folyamat minden eddiginél leáll vagy összeomlik, az eredményül kapott folyamat felügyelt és újraindul (újra kezdve **SetupEntryPoint**).  

A tipikus forgatókönyvek **SetupEntryPoint** is futtatni egy végrehajtható fájlt a szolgáltatás indítása előtt, vagy egy emelt szintű jogosultságokkal műveletet hajt végre. Példa:

* Beállítását, és a környezeti változók, amelyet a szolgáltatás futtatható fájlja inicializálása. Ez nem csak a Service Fabric programozási modell használatával írt végrehajtható korlátozódik. Például npm.exe kell néhány környezeti változók konfigurált node.js-alkalmazás üzembe helyezéséhez.
* Hozzáférés-vezérlés beállítása a biztonsági tanúsítványok telepítésével.

A SetupEntryPoint konfigurálásáról további információkért lásd: [a házirend-szolgáltatásbeállítás belépési pontjának konfigurálása](service-fabric-application-runas-security.md)

**EnvironmentVariables** (nem ismeretlenre van állítva az előző példában) környezeti változók vannak beállítva, a kód csomag listáját tartalmazza. A környezeti változók felülbírálhatók a `ApplicationManifest.xml` különböző szolgáltatáspéldányok a különböző értékek megadását. 

**DataPackage** (nem ismeretlenre van állítva az előző példában) deklarál egy szerint nevű mappát a **neve** futási időben a folyamat által használt tetszőleges statikus adatokat tartalmazó attribútum.

**ConfigPackage** szerint nevű mappa, deklarálja a **neve** attribútum, amely tartalmaz egy *Settings.xml* fájl. A fájl a felhasználó által definiált, a kulcs-érték párt a beállításokat, amelyeket a folyamat beolvassa a futási időben vissza szakaszokat tartalmazza. Ha csak frissítés során a **ConfigPackage** **verzió** módosult, akkor a futó folyamat nem indul újra. Ehelyett egy visszahívást, amely a konfigurációs beállításai megváltoztak, így azok dinamikusan kell tölteni a folyamat értesítést küld. Íme egy példa *Settings.xml* fájlt:

```xml
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

A Service Fabric-szolgáltatás **végpont** egy Service Fabric-erőforrás; példa A Service Fabric erőforrás lehet deklarált vagy módosítani a lefordított kód módosítása nélkül. A Service Fabric-erőforrások a szolgáltatásjegyzékben megadott hozzáférést szabályozható a **SecurityGroup** az alkalmazásjegyzékben. A szolgáltatásjegyzék-végponti erőforrás van definiálva, amikor a Service Fabric rendeli hozzá a portokat a fenntartott tartománya, ha a port nincs megadva explicit módon. Tudjon meg többet [megadásával vagy a végpont erőforrások felülírása](service-fabric-service-manifest-resources.md).


<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Egy alkalmazás az ApplicationManifest.xml leírása
Az alkalmazásjegyzék deklaratív ismerteti az alkalmazás típusát és verzióját. Azt adja meg a szolgáltatás összeállítás metaadatokat, például stabil nevek, particionálási sémát, instance count vagy replikációs tényezőt, biztonsági vagy elkülönítési szabályzat, elhelyezési korlátozások, konfigurációs felülbírálások és alkotó szolgáltatástípusok. A terheléselosztás tartományok, ahol az alkalmazás helyezni van is ismerteti.

Ennélfogva alkalmazásjegyzéket az alkalmazás szintjén-elemeket ismerteti, és egy vagy több szolgáltatásjegyzékek, hogy az alkalmazástípust hivatkozik. Itt látható az alkalmazás jegyzékfájljának a [mintául szolgáló Szavazóalkalmazás](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (és néhány [részletesebb példákat](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

Szolgáltatásjegyzékek, például **verzió** attribútumok strukturálatlan karakterláncok és a rendszer nem elemzi. Verzió attribútumokat is használt verzió az egyes összetevők frissítéseket.

**Paraméterek** a paramétereket, az alkalmazás jegyzékfájlja során használt. Ezek a paraméterek is megadni, ha az alkalmazás példányosítása, és felül lehet bírálni, alkalmazás vagy szolgáltatás konfigurációs beállításait.  A paraméter alapértelmezett értéke akkor használatos, ha az érték nem módosul az alkalmazás történt. További információk a fenntartása a különböző alkalmazás és szolgáltatás paraméterei, az egyes környezetekhez,: [kezelése több környezethez alkalmazásparamétereket](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** tento typ aplikace alkotó szolgáltatásjegyzékek mutató hivatkozásokat tartalmaz. Alkalmazásjegyzék tartalmazhat több service manifest import, egyenként is lehetnek rendszerverzióval ellátott egymástól függetlenül. Importált szolgáltatásjegyzékek határozza meg, milyen szolgáltatástípusok tento typ aplikace belül érvényesek. A ServiceManifestImport belül bírálja felül a ServiceManifest.xml fájlban Settings.xml és környezeti változók konfigurációs értékeket. **Házirendek** (az előző példában nincs beállítva) a végpont kötésének, a biztonsági és a hozzáférés és a csomag megosztási is beállíthat importált szolgáltatásjegyzékek.  További információkért lásd: [konfigurálhat biztonsági házirendeket az alkalmazás](service-fabric-application-runas-security.md).

**DefaultServices** deklarálja a szolgáltatáspéldány, amely automatikusan létrejön, amikor egy alkalmazás van példányosítva tento typ aplikace ellen. Alapértelmezett szolgáltatások csak a kényelmi célokat szolgál, és minden tekintetben normál szolgáltatások viselkednek a létrehozásuk után. Ezek az alkalmazáspéldány bármely egyéb szolgáltatásokkal együtt frissül, és is eltávolítható. Alkalmazásjegyzék alapértelmezett több szolgáltatást is tartalmazhatnak.

**Tanúsítványok** (nem ismeretlenre van állítva az előző példában) kijelenti, hogy használt tanúsítványok [beállítása HTTPS-végpontok](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) vagy [titkosítani a titkos kulcsok az alkalmazásjegyzékben](service-fabric-application-secret-management.md).

**Házirendek** (nem ismeretlenre van állítva az előző példában) ismerteti a naplógyűjtő [alapértelmezett futtató](service-fabric-application-runas-security.md), [egészségügyi](service-fabric-health-introduction.md#health-policies), és [biztonsági hozzáférési](service-fabric-application-runas-security.md) szabályzatok beállítása a alkalmazás szintjén.

**Rendszerbiztonsági tagok** (az előző példában nincs beállítva) írja le a rendszerbiztonsági tagok (felhasználók vagy csoportok) szükséges [futtatási szolgáltatások és biztonságos-szolgáltatási erőforrások](service-fabric-application-runas-security.md).  A hivatkozott rendszerbiztonsági tagok a **házirendek** szakaszokat.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>További lépések
- [Alkalmazás becsomagolása](service-fabric-package-apps.md) , és győződjön meg arról, hogy üzembe.
- [Üzembe helyezése és távolíthat el alkalmazásokat](service-fabric-deploy-remove-applications.md).
- [Paraméterek és a különböző alkalmazáspéldányok a környezeti változók konfigurálása](service-fabric-manage-multiple-environment-app-configuration.md).
- [Az alkalmazás biztonsági szabályzatainak konfigurálásához](service-fabric-application-runas-security.md).
- [HTTPS-végpontok beállítása](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Az alkalmazásjegyzékben titkok titkosítását](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



