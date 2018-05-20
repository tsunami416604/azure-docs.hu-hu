---
title: Az Azure Service Fabric-alkalmazások és szolgáltatások leíró |} Microsoft Docs
description: Ismerteti, hogyan történik a jegyzékfájlban Service Fabric-alkalmazások és szolgáltatások használata.
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
ms.date: 2/23/2018
ms.author: ryanwi
ms.openlocfilehash: b79206b9d456226d14984e8a1c1002c07c4f626a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-application-and-service-manifests"></a>Service Fabric-alkalmazás és szolgáltatás jegyzékfájlokban
Ez a cikk ismerteti, hogyan történik a Service Fabric-alkalmazások és szolgáltatások, hogy meghatározott és rendszerverzióval ellátott ApplicationManifest.xml és ServiceManifest.xml fájlokat használja.  Az XML-séma a jegyzék fájlok részletes ismertetését lásd: [ServiceFabricServiceModel.xsd séma dokumentáció](service-fabric-service-model-schema.md).

## <a name="describe-a-service-in-servicemanifestxml"></a>ServiceManifest.xml szolgáltatás leírása
A szolgáltatás deklarációval jegyzékfájl szolgáltatás típusától és verziójától. Azt adja meg a szolgáltatás metaadatokat, például szolgáltatás típusa, a rendszerállapot-tulajdonságai, a terheléselosztás metrikák, a bináris fájljait és a konfigurációs fájlok.  Másképp fogalmazva, a kódot, a konfiguráció és az adatok csomagok a service-csomag egy vagy több szolgáltatás típusainak használatát lehetővé alkotó ismerteti. A szolgáltatás jegyzék több kódot, konfiguráció és adatok csomagok, amelyek egymástól függetlenül lehet rendszerverzióval ellátott tartalmazhat. Ez a szolgáltatás az ASP.NET Core előtér webszolgáltatás a jegyzék a [mintaalkalmazás szavazás](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart):

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

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
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

**Verzió** attribútumok strukturálatlan karakterláncok, és a rendszer nem elemzi. Verzió attribútumok használt verziója minden egyes összetevő frissítésre.

**ServiceTypes** kijelenti, hogy milyen típusú szolgáltatásokat által támogatott **CodePackages** a jegyzékfájlban. Amikor egy szolgáltatás létrejön az említett szolgáltatás ellen, a jegyzékfájlban deklarált összes kódot csomag aktívak a belépési pontok futtatásával. Az eredményül kapott folyamatok várhatóan a támogatott típusok regisztrálása a futási időben. A jegyzék szintjét és a nem a kód csomag szintjén deklarált típusú szolgáltatásokat. Ezért ha több kód csomagot, az összes aktiválás amikor a rendszer megkeresi a deklarált szolgáltatás típusok egyikét sem.

A megadott végrehajtható fájl **EntryPoint** általában a hosszan futó szolgáltatás gazdagép legyen. **SetupEntryPoint** egy jogosultsági szintű belépési pontja, amelynek ugyanazokat a hitelesítő adatokat, mint a Service Fabric (általában a *LocalSystem* fiók) más belépési pont előtt.  Egy külön telepítő belépési pont jelenléte elkerülhető, hogy rendelkezik jogosultságokkal történő futtatása az adatszolgáltatás gazdájának magas huzamosabb ideig. A megadott végrehajtható fájl **EntryPoint** futtatása **SetupEntryPoint** sikeresen kilép. Ha valaha is a folyamat leáll, vagy összeomlik, az eredményül kapott folyamat ellenőrzött és újraindítása (újra kezdve **SetupEntryPoint**).  

A jellemző forgatókönyvek **SetupEntryPoint** Biztosan futtatni egy végrehajtható fájlt a szolgáltatás indítása előtt, vagy egy emelt szintű jogosultságokkal műveletét úgy végezheti el. Példa:

* Beállítását, valamint a környezeti változókat, amelyek a szolgáltatás futtatható fájlja kell inicializálása. Ez nem csak a Service Fabric programozási modell használatával írt végrehajtható korlátozódik. Például npm.exe kell néhány környezetiblokk-változót, egy node.js-alkalmazás telepítéséhez konfigurált.
* Hozzáférés-vezérlés beállítása biztonsági tanúsítványok telepítésével.

Konfigurálásával kapcsolatos további részletekért a **SetupEntryPoint**, lásd: [egy szolgáltatás-telepítés belépési pont vonatkozó házirend konfigurálása](service-fabric-application-runas-security.md)

**Változók** (nincs beállítva az előző példában) a környezeti változókat, amelyek be vannak állítva a kódcsomag listáját tartalmazza. Környezeti változók felülbírálhatók a `ApplicationManifest.xml` különböző szolgáltatáspéldány különböző értékének megadására. 

**DataPackage** (nincs beállítva az előző példában) deklarál egy által nevű mappát a **neve** attribútum, amely tartalmazza a futási időben a folyamat által felhasznált tetszőleges statikus adatok.

**ConfigPackage** deklarál egy által nevű mappát a **neve** attribútum, amely tartalmaz egy *Settings.xml* fájlt. A fájl a felhasználó által definiált, a kulcs-érték pár beállítások a folyamat futási időben vissza olvasó részeket tartalmazza. Ha csak frissítés során a **ConfigPackage** **verzió** változott, és nem indítják újra a futó folyamattal. Ehelyett egy visszahívási értesíti, hogy a konfigurációs beállításai módosultak, így azok dinamikusan lehetett újra a folyamatot. Példa *Settings.xml* fájlt:

```xml
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

**Erőforrások**, például a végpontok, használt a szolgáltatás kell lennie a deklarálva, illetve nem módosítható a lefordított kód módosítása nélkül.  A szolgáltatás jegyzékben megadott az erőforrásokhoz való hozzáférés szabályozható a **a(z) biztonsági csoporthoz** az alkalmazásjegyzékben.  Ha egy **végpont** erőforrás van definiálva, a szolgáltatás jegyzékben, a Service Fabric rendeli hozzá portokat a fenntartott alkalmazás porttartományát, ha a port nincs megadva explicit módon.  Tudjon meg többet az [megadásával, vagy a végpont erőforrások felülbírálása](service-fabric-service-manifest-resources.md).


<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Az alkalmazás ApplicationManifest.xml leírása
Az alkalmazás jegyzékében deklarációval ismerteti az alkalmazástípus és -verzió. Azt adja meg a szolgáltatás összeállítás metaadatai köztük, példányok száma vagy replikációs tényező, biztonsági/elkülönítési házirenddel, placement Constraints korlátozásokat, konfigurációs felülbírálások, és a bennük foglalt szolgáltatástípusok stabil nevét. A terheléselosztás tartományok, amelybe helyezkedik el az alkalmazást is ismerteti.

Emiatt alkalmazásjegyzéket az alkalmazás szintjén elemeket ismerteti, és egy vagy több szolgáltatás jegyzékfájlokat alkalmazástípust írása hivatkozik. Ez az alkalmazás jegyzékfájljának a [mintaalkalmazás szavazás](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart):

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

Szolgáltatás jegyzékfájlban, például **verzió** attribútumok strukturálatlan karakterláncok, és a rendszer nem elemzi a rendszer. Attribútumok verziója is használt verziója minden egyes összetevő frissítésre.

**Paraméterek** határozza meg az alkalmazás jegyzékében során használt paraméterek. Ezek a paraméterek értékeit is megadni, ha az alkalmazás instatiated, és szeretné felülbírálni az alkalmazás vagy szolgáltatás konfigurációs beállításait.  A paraméter alapértelmezett értéke akkor használatos, ha az érték nem módosul alkalmazás történt. Különböző alkalmazás-és egyedi környezetek paramétereket, lásd: [alkalmazás paramétereinek több környezet kezelése](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** szolgáltatás jegyzékfájlokban, ez az alkalmazástípus alkotó mutató hivatkozásokat tartalmaz. Az alkalmazás jegyzékében több service manifest importálja, a egymástól függetlenül minden egyes lehetnek rendszerverzióval ellátott. Importált service jegyzékfájlokban meghatározására, hogy milyen típusú szolgáltatásokat érvényes ez az alkalmazástípus belül. A ServiceManifestImport belül bírálja felül konfigurációs ServiceManifest.xml fájlok Settings.xml és környezeti változók értékeit. **Házirendek** (nincs beállítva az előző példában) végpont kötése, a biztonsági és a hozzáférés és a csomag megosztási állítható be importált service jegyzékfájljai.  További információkért lásd: [konfigurálhat biztonsági házirendeket az alkalmazás](service-fabric-application-runas-security.md).

**DefaultServices** szolgáltatáspéldány, amikor egy alkalmazás létrejön az alkalmazás típusa alapján automatikusan létrehozott deklarál. Alapértelmezett szolgáltatások csupán a könnyebb elérhetőség érdekében, és minden tekintetben normál szolgáltatások viselkednek, azok létrehozását követően. Azok az alkalmazáspéldány bármely más szolgáltatásokkal együtt verzióra frissíti, és is távolíthatók el. Az alkalmazás jegyzékében több alapértelmezett szolgáltatás is tartalmazhat.

**Tanúsítványok** (nincs beállítva az előző példában) deklarálja a tanúsítványok [beállítása HTTPS-végpontnak](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) vagy [az alkalmazásjegyzékben szereplő titkos kulcsok titkosítására](service-fabric-application-secret-management.md).

**Házirendek** (nincs beállítva az előző példában), írja le az adatgyűjtést, [futtató alapértelmezett](service-fabric-application-runas-security.md), [állapotfigyelő](service-fabric-health-introduction.md#health-policies), és [biztonsági hozzáférési](service-fabric-application-runas-security.md) házirendek beállítása a alkalmazás szintjét.

**Rendszerbiztonsági tagok** (nincs beállítva az előző példában) ismertetik a rendszerbiztonsági tag (felhasználók vagy csoportok) szükséges [futtatási szolgáltatások és erőforrások biztonságos szolgáltatás](service-fabric-application-runas-security.md).  A hivatkozott rendszerbiztonsági tagok a **házirendek** szakaszok.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>További lépések
- [Egy alkalmazás becsomagolása](service-fabric-package-apps.md) és üzembe helyezésére teszi.
- [Központi telepítése és távolíthat el alkalmazásokat](service-fabric-deploy-remove-applications.md).
- [Paraméterek és a környezeti változók különböző alkalmazás-példányok konfigurálása](service-fabric-manage-multiple-environment-app-configuration.md).
- [Az alkalmazás biztonsági szabályzatainak konfigurálásához](service-fabric-application-runas-security.md).
- [A telepítő HTTPS-végpontnak](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Az alkalmazásjegyzékben szereplő titkos kulcsok titkosítása](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



