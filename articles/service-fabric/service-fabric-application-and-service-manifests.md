---
title: Az Azure Service Fabric-alkalmazások és -szolgáltatások leírása
description: Leírja, hogyan jegyzékek a Service Fabric-alkalmazások és -szolgáltatások leírására használt.
ms.topic: conceptual
ms.date: 8/12/2019
ms.openlocfilehash: 6014ef6a9b6ec810aafd5e5be96223b8ed92d576
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349962"
---
# <a name="service-fabric-application-and-service-manifests"></a>A Service Fabric alkalmazás- és szolgáltatásjegyzékei
Ez a cikk azt ismerteti, hogy a Service Fabric-alkalmazások és -szolgáltatások hogyan vannak definiálva és verziózva az ApplicationManifest.xml és serviceManifest.xml fájlokkal.  További részletes példákat az [alkalmazás- és szolgáltatásjegyzék-példákban talál.](service-fabric-manifest-examples.md)  A jegyzékfájlok XML-sémáját a [ServiceFabricServiceModel.xsd séma dokumentációja dokumentálja.](service-fabric-service-model-schema.md)

> [!WARNING]
> A jegyzékfájl-XML-fájlséma kényszeríti a gyermekelemek helyes rendezését.  Részleges megoldásként nyissa meg a "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricModel.xsd" fájlt a Visual Studio programban, miközben a Service Fabric-jegyzékek bármelyikét megírja vagy módosítja. Ez lehetővé teszi, hogy ellenőrizze a gyermek elemek sorrendjét, és intelli-értelmet biztosít.

## <a name="describe-a-service-in-servicemanifestxml"></a>Szolgáltatás leírása a ServiceManifest.xml fájlban
A szolgáltatásjegyzék deklaratív módon határozza meg a szolgáltatás típusát és verzióját. A szolgáltatás metaadatait, például a szolgáltatás típusát, az állapottulajdonságokat, a terheléselosztási metrikákat, a szolgáltatás bináris fájljait és a konfigurációs fájlokat határozza meg.  Másképpen fogalmazva, leírja a kódot, a konfigurációt és az adatcsomagokat, amelyek egy vagy több szolgáltatástípus ttámogató szolgáltatáscsomagot állítanak össze. A szolgáltatásjegyzék több kódot, konfigurációt és adatcsomagokat is tartalmazhat, amelyek egymástól függetlenül verziószámmal is verziózhatók. Itt van egy szolgáltatás jegyzékfájl a ASP.NET Core web front-end szolgáltatás a [szavazásminta alkalmazás](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (és itt van néhány [részletesebb példa):](service-fabric-manifest-examples.md)

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

**A verzióattribútumok** strukturálatlan karakterláncok, és a rendszer nem elemzi őket. A verzióattribútumok az egyes összetevők frissítésére szolgálnak.

**A ServiceTypes** deklarálja, hogy a **codepackages** mely szolgáltatástípusokat támogatja ebben a jegyzékben. Ha egy szolgáltatást ezen szolgáltatástípusok egyikével hoznak meg, a jegyzékfájlban deklarált összes kódcsomag a belépési pontok futtatásával aktiválódik. Az eredményül kapott folyamatoknak futásidőben regisztrálniuk kell a támogatott szolgáltatástípusokat. A szolgáltatástípusok deklarálva a jegyzékfájl szintjén vannak deklarálva, nem pedig a kódcsomag szintjén. Így ha több kódcsomag van, akkor azok mind aktiválódnak, amikor a rendszer a deklarált szolgáltatástípusok bármelyikét keresi.

Az **EntryPoint** által megadott végrehajtható fájl általában a hosszú ideig futó szolgáltatásgazda. **A SetupEntryPoint** egy kiemelt belépési pont, amely a Service Fabric (általában a *LocalSystem* fiók) hitelesítő adataival fut bármely más belépési pont előtt.  Egy külön beállítási belépési pont jelenléte elkerüli, hogy a szolgáltatásgazda nagy jogosultságokkal fusson hosszabb ideig. Az **EntryPoint** által megadott végrehajtható fájl a **SetupEntryPoint** sikeres kilépése után fut. Ha a folyamat valaha is leáll vagy összeomlik, az eredményül kapott folyamatot figyeli és újraindítja (a **SetupEntryPoint**programmal kezdődik).  

A **SetupEntryPoint** használatának tipikus forgatókönyvei a végrehajtható fájl futtatása a szolgáltatás indítása előtt, vagy emelt szintű jogosultságokkal rendelkező művelet végrehajtása. Példa:

* A szolgáltatás végrehajtható rendszerének igényeinek megfelelő környezeti változók beállítása és inicializálása. Ez nem korlátozódik csak a Service Fabric programozási modelleken keresztül írt végrehajtható fájlokra. Az npm.exe például szüksége van néhány olyan környezeti változóra, amely et konfigurált egy node.js alkalmazás telepítéséhez.
* Hozzáférés-vezérlés beállítása biztonsági tanúsítványok telepítésével.

A SetupEntryPoint konfigurálásáról a [Szolgáltatás beállítási belépési pontjához szükséges házirend konfigurálása című](service-fabric-application-runas-security.md) témakörben talál további információt.

**EnvironmentVariables** (nem szerepel az előző példában) tartalmazza a környezeti változók, amelyek be vannak állítva ehhez a kódcsomaghoz. A környezeti változók felülbírálhatók `ApplicationManifest.xml` a különböző szolgáltatáspéldányok különböző értékeinek megadásához. 

**A DataPackage** (amely nincs az előző példában beállítva) deklarál **egy,** a Name attribútum mal elnevezett mappát, amely tetszőleges statikus adatokat tartalmaz, amelyeket a folyamat futásidőben használ fel.

**A ConfigPackage** deklarál egy Folder nevű mappát, amelyet a **Name** attribútum nevez el, és amely *Settings.xml* fájlt tartalmaz. A beállításfájl a felhasználó által definiált, kulcs-érték pár beállítások azon szakaszait tartalmazza, amelyeket a folyamat futásközben visszaolvas. A frissítés során, ha csak a **ConfigPackage** **verzió** változott, akkor a futó folyamat nem indul újra. Ehelyett a visszahívás értesíti a folyamatot, hogy a konfigurációs beállítások megváltoztak, így azok dinamikusan újratölthetők. Íme egy példa *Settings.xml* fájl:

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

A Service Fabric service **endpoint** egy szolgáltatásháló-erőforrás egy példa. A Service Fabric-erőforrás deklarálható/módosítható a lefordított kód módosítása nélkül. A service-jegyzékben megadott Service Fabric-erőforrásokhoz való hozzáférés az alkalmazásjegyzékben lévő **SecurityGroup-on** keresztül szabályozható. Ha egy végponti erőforrás definiálva van a szolgáltatásjegyzékben, a Service Fabric portokat rendel a fenntartott alkalmazásport-tartományból, ha egy port nincs megadva explicit módon. További információ a [végponti erőforrások megadásáról vagy felülbírálásáról.](service-fabric-service-manifest-resources.md)

 
> [!WARNING]
> A statikus portok a tervek szerint nem fedhetik át a ClusterManifest-ben megadott alkalmazásport-tartományt. Ha statikus portot ad meg, rendelje hozzá az alkalmazásport tartományán kívülre, ellenkező esetben portütközéseket fog eredményezni. A 6.5CU2 kiadással egészségügyi figyelmeztetést adunk **ki,** amikor ilyen ütközést észlelünk, de hagyjuk, hogy a telepítés szinkronban maradjon a szállított 6.5 viselkedéssel. Azonban megakadályozhatja, hogy az alkalmazás központi telepítése a következő fő kiadások.
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Alkalmazás leírása az ApplicationManifest.xml fájlban
Az alkalmazás jegyzékfájlja deklaratív módon leírja az alkalmazás típusát és verzióját. Meghatározza a szolgáltatásösszetétel metaadatait, például a stabil neveket, a particionálási sémát, a példányszám/replikációs tényezőt, a biztonsági/elkülönítési házirendet, az elhelyezési megkötéseket, a konfigurációfelülbírálásokat és az összetevő szolgáltatástípusokat. A terheléselosztási tartományok, amelyekbe az alkalmazás kerül, szintén le van írva.

Így az alkalmazásjegyzék az alkalmazás szintjén lévő elemeket írja le, és egy vagy több szolgáltatásjegyzékre hivatkozik egy alkalmazástípus összeállításához. Itt van a pályázati jegyzék a [szavazás minta alkalmazás](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (és itt van néhány [részletesebb példa):](service-fabric-manifest-examples.md)

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
         <PlacementConstraints>(NodeType==NodeType0)</PlacementConstraints
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

A szolgáltatásjegyzékekhez hasonlóan **a verzióattribútumok** is strukturálatlan karakterláncok, és a rendszer nem elemzi őket. A verzióattribútumok az egyes összetevők frissítéséhez is használhatók.

**A paraméterek** az alkalmazásjegyzékben használt paramétereket határozzák meg. Ezeknek a paramétereknek az értékei az alkalmazás példányossága esetén adhatók meg, és felülbírálhatják az alkalmazás- vagy szolgáltatáskonfigurációs beállításokat.  Az alapértelmezett paraméterértéket használja a rendszer, ha az érték nem változik az alkalmazás példányosítása során. Ha tudni szeretné, hogyan tarthatja fenn az egyes környezetek különböző alkalmazás- és szolgáltatásparamétereit, olvassa el [az Alkalmazásparaméterek kezelése több környezethez című témakört.](service-fabric-manage-multiple-environment-app-configuration.md)

**A ServiceManifestImport** az alkalmazástípust alkotó szolgáltatásjegyzékekhivatkozásait tartalmazza. Az alkalmazásjegyzék több szolgáltatásjegyzék-importálást is tartalmazhat, mindegyik egymástól függetlenül verziószámozott. Az importált szolgáltatásjegyzékek határozzák meg, hogy milyen szolgáltatástípusok érvényesek ezen az alkalmazástípuson belül. A ServiceManifestImport fájlon belül felülbírálhatja a Configuration.xml és a ServiceManifest.xml fájlok környezeti változóinak konfigurációs értékeit. A végpontkötésre, a biztonságra és a hozzáférésre vonatkozó **házirendek** (nincsenek beállítva) az importált szolgáltatásjegyzékekben.  További információt az [Alkalmazás biztonsági házirendjeinek konfigurálása](service-fabric-application-runas-security.md)című témakörben talál.

**A DefaultServices** deklarálja azokat a szolgáltatáspéldányokat, amelyek automatikusan létrejönnek, amikor egy alkalmazás példányosan erre az alkalmazástípusra kerül létrehozva. Az alapértelmezett szolgáltatások csak kényelmi ek, és úgy viselkednek, mint a normál szolgáltatások minden tekintetben, miután létrehozták őket. Ezek az alkalmazáspéldány bármely más szolgáltatásával együtt frissülnek, és eltávolíthatók is. Az alkalmazásjegyzék több alapértelmezett szolgáltatást is tartalmazhat.

**A tanúsítványok** (amelyek nincsenek beállítva az előző példában) deklarálják a [HTTPS-végpontok beállításához](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) vagy [az alkalmazásjegyzék titkos titkainak titkosításához](service-fabric-application-secret-management.md)használt tanúsítványokat.

**Az elhelyezési korlátok** azok az utasítások, amelyek meghatározzák, hogy a szolgáltatások hol fussanak. Ezek az utasítások egy vagy több csomóponttulajdonsághoz kiválasztott egyes szolgáltatásokhoz vannak csatolva. További információ: [Elhelyezési megkötések és csomóponttulajdonság szintaxisa](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#placement-constraints-and-node-property-syntax)

**A házirendek** (amelyek nincsenek beállítva az előző példában) ismertetik az alkalmazás szintjén beállított naplógyűjteményt, [alapértelmezett futtatási](service-fabric-application-runas-security.md), [állapot-](service-fabric-health-introduction.md#health-policies)és [biztonsági hozzáférési](service-fabric-application-runas-security.md) házirendeket, beleértve azt is, hogy a szolgáltatás(ok) hozzáférhetnek-e a Service Fabric futásidejéhez.

> [!NOTE] 
> Alapértelmezés szerint a Service Fabric-alkalmazások hozzáférhetnek a Service Fabric futásidejű, egy végpont alkalmazásspecifikus kéréseket fogad el, és a környezeti változók mutató fájl elérési utak a gazdagépen, amely tartalmazza a Fabric és az alkalmazás-specifikus fájlok . Fontolja meg a hozzáférés letiltását, ha az alkalmazás nem megbízható kódot üzemeltet (azaz olyan kódot, amelynek eredete ismeretlen, vagy amelyről az alkalmazás tulajdonosa tudja, hogy nem biztonságos végrehajtani). További információkért tekintse meg a [Service Fabric biztonsági gyakorlati tanácsait.](service-fabric-best-practices-security.md#platform-isolation) 
>

**A rendszerbiztonsági tagok** (amelyek nincsenek beállítva az előző példában) a szolgáltatások futtatásához és a [szolgáltatáserőforrások biztosításához](service-fabric-application-runas-security.md)szükséges rendszerbiztonsági tagokat (felhasználók vagy csoportok) írják le.  A rendszerbiztonsági tagokra a **Házirendek** szakasz hivatkozik.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>További lépések
- [Csomagoljon be egy alkalmazást,](service-fabric-package-apps.md) és tegye készen az üzembe helyezésre.
- [Alkalmazások telepítése és eltávolítása](service-fabric-deploy-remove-applications.md).
- [Konfigurálja a paramétereket és a környezeti változókat a különböző alkalmazáspéldányokhoz.](service-fabric-manage-multiple-environment-app-configuration.md)
- [Konfigurálja az alkalmazás biztonsági házirendjeit.](service-fabric-application-runas-security.md)
- [HTTPS-végpontok beállítása](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Titkos kulcsok titkosítása az alkalmazásjegyzékben](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



