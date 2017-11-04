---
title: "Az Azure Service Fabric-alkalmazás modell |} Microsoft Docs"
description: "A modell, és ismerteti az alkalmazások és szolgáltatások a Service Fabric módjáról."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: ryanwi
ms.openlocfilehash: e30482427b88eb3e58d39075c7f0734664b79aa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="model-an-application-in-service-fabric"></a>A Service Fabric alkalmazás minta
Ez a cikk áttekintést nyújt az Azure Service Fabric alkalmazásmodell és hogyan adhat meg a kérelem és a service manifest-fájlok segítségével.

## <a name="understand-the-application-model"></a>Az alkalmazásmodell ismertetése
Egy alkalmazás egy bizonyos funkciója vagy funkciói végző alkotó szolgáltatások gyűjteménye. Egy szolgáltatás teljes és a különálló funkciót hajt végre és elindíthatja és egyéb szolgáltatások függetlenül futtatni.  A szolgáltatás kódot, a konfiguráció és az adatok tevődik össze. Minden egyes szolgáltatás kód a végrehajtható bináris áll, konfigurálása a futási időben betölthető Szolgáltatásbeállítások áll és a szolgáltatás által felhasznált tetszőleges statikus adatokat tartalmaz. A hierarchikus alkalmazás modellben minden egyes összetevő csak rendszerverzióval ellátott és frissített egymástól függetlenül.

![A Service Fabric alkalmazásmodellt.][appmodel-diagram]

Alkalmazástípust egy kategorizálási kérelem és a csomag egyik gyermekszoftver található szolgáltatástípusok áll. A szolgáltatás típus egy kategorizálási szolgáltatás. A kategorizálási különböző beállítások és konfigurációk rendelkezhet, de alapfunkciókat változatlan marad. A szolgáltatás példányainak a különböző nagyon sokféle konfiguráció azonos típusú szolgáltatás.  

Osztályok (vagy "típusú") az alkalmazások és szolgáltatások leírása XML-fájlok (alkalmazás és szolgáltatás jegyzékfájlokban) keresztül.  A jegyzékfájlokban a sablonokat, amely alkalmazások lehet létrehozni a fürt kép store-ból. A séma meghatározása a ServiceManifest.xml és ApplicationManifest.xml fájl telepítve van a Service Fabric SDK-val, és az eszközök *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

A különböző alkalmazás-példányok kódot futtató különálló folyamat akkor is, ha a azonos a Service Fabric-csomópont. Ezenkívül az életciklus az összes alkalmazás-példány (például frissítése) kezelhetők egymástól függetlenül. Az alábbi ábrán látható, hogyan alkalmazástípusok szolgáltatástípusok, viszont álló kódot, a konfiguráció és adatok csomagok állnak. Egyszerűbbé teheti a diagramot, csak a kód/config/adatokat a csomagok `ServiceType4` láthatók, bár egyes szolgáltatástípusokról tartalmazhat néhány vagy minden csomagtípus.

![A Service Fabric alkalmazás és szolgáltatás típusainak][cluster-imagestore-apptypes]

Két különböző fájlok használt alkalmazások és szolgáltatások: a szolgáltatás jegyzékfájl és az alkalmazásjegyzék. Jegyzékfájlokban a következő szakaszokban részletesen ismertetett.

Egy vagy több példányát egy típusú szolgáltatás lehet aktív a fürtben. Például állapot-nyilvántartó szolgáltatáspéldány, vagy a replikák elérése magas megbízhatóság állapot replikálja a replikák található a fürt csomópontjai között. Replikációs alapvetően a szolgáltatás számára elérhetők, még akkor is, ha a fürt egyik csomópontjáról sikertelen redundanciát biztosít. A [szolgáltatás particionálva](service-fabric-concepts-partitioning.md) további osztja a állapota (és a hozzáférési minták arra az állapotra) a fürt csomópontjai között.

Az alábbi ábrán látható, az alkalmazások és a szolgáltatáspéldány, a partíciók és a replikák közötti kapcsolat.

![Partíciók és replikáit a szolgáltatáson belül][cluster-application-instances]

> [!TIP]
> Azzal, hogy az alkalmazások megtekintheti egy http:// érhető el a Service Fabric Explorer eszközt használó fürtben&lt;yourclusteraddress&gt;: 19080/Explorer. További információkért lásd: [a fürt megjelenítése a Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 

## <a name="describe-a-service"></a>A szolgáltatás leírása
A szolgáltatás deklarációval jegyzékfájl szolgáltatás típusától és verziójától. Azt adja meg a szolgáltatás metaadatokat, például szolgáltatás típusa, a rendszerállapot-tulajdonságai, a terheléselosztás metrikák, a bináris fájljait és a konfigurációs fájlok.  Másképp fogalmazva, a kódot, a konfiguráció és az adatok csomagok a service-csomag egy vagy több szolgáltatás típusainak használatát lehetővé alkotó ismerteti. Íme egy egyszerű példa service jegyzékfájl:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```

**Verzió** attribútumok strukturálatlan karakterláncok, és a rendszer nem elemzi. Verzió attribútumok használt verziója minden egyes összetevő frissítésre.

**ServiceTypes** kijelenti, hogy milyen típusú szolgáltatásokat által támogatott **CodePackages** a jegyzékfájlban. Amikor egy szolgáltatás létrejön az említett szolgáltatás ellen, a jegyzékfájlban deklarált összes kódot csomag aktívak a belépési pontok futtatásával. Az eredményül kapott folyamatok várhatóan a támogatott típusok regisztrálása a futási időben. A jegyzék szintjét és a nem a kód csomag szintjén deklarált típusú szolgáltatásokat. Ezért ha több kód csomagot, az összes aktiválás amikor a rendszer megkeresi a deklarált szolgáltatás típusok egyikét sem.

**SetupEntryPoint** egy jogosultsági szintű belépési pontja, amelynek ugyanazokat a hitelesítő adatokat, mint a Service Fabric (általában a *LocalSystem* fiók) más belépési pont előtt. A megadott végrehajtható fájl **EntryPoint** általában a hosszan futó szolgáltatás gazdagép legyen. Egy külön telepítő belépési pont jelenléte elkerülhető, hogy rendelkezik jogosultságokkal történő futtatása az adatszolgáltatás gazdájának magas huzamosabb ideig. A megadott végrehajtható fájl **EntryPoint** futtatása **SetupEntryPoint** sikeresen kilép. Ha valaha is a folyamat leáll, vagy összeomlik, az eredményül kapott folyamat ellenőrzött és újraindítása (újra kezdve **SetupEntryPoint**).  

A jellemző forgatókönyvek **SetupEntryPoint** Biztosan futtatni egy végrehajtható fájlt a szolgáltatás indítása előtt, vagy egy emelt szintű jogosultságokkal műveletét úgy végezheti el. Példa:

* Beállítását, valamint a környezeti változókat, amelyek a szolgáltatás futtatható fájlja kell inicializálása. Ez nem csak a Service Fabric programozási modell használatával írt végrehajtható korlátozódik. Például npm.exe kell néhány környezetiblokk-változót, egy node.js-alkalmazás telepítéséhez konfigurált.
* Hozzáférés-vezérlés beállítása biztonsági tanúsítványok telepítésével.

Konfigurálásával kapcsolatos további részletekért a **SetupEntryPoint** lásd [egy szolgáltatás-telepítés belépési pont vonatkozó házirend konfigurálása](service-fabric-application-runas-security.md)

**Változók** környezeti változókat, amelyek be vannak állítva a kódcsomag listáját tartalmazza. Environmentment változók felülbírálhatók a `ApplicationManifest.xml` különböző szolgáltatáspéldány különböző értékének megadására. 

**DataPackage** deklarál egy által nevű mappát a **neve** attribútum, amely tartalmazza a futási időben a folyamat által felhasznált tetszőleges statikus adatok.

**ConfigPackage** deklarál egy által nevű mappát a **neve** attribútum, amely tartalmaz egy *Settings.xml* fájlt. A fájl a felhasználó által definiált, a kulcs-érték pár beállítások a folyamat futási időben vissza olvasó részeket tartalmazza. Ha csak frissítés során a **ConfigPackage** **verzió** változott, és nem indítják újra a futó folyamattal. Ehelyett egy visszahívási értesíti, hogy a konfigurációs beállításai módosultak, így azok dinamikusan lehetett újra a folyamatot. Példa *Settings.xml* fájlt:

```xml
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

> [!NOTE]
> A szolgáltatás jegyzék több kódot, konfiguráció és adatok csomagok tartalmazhatnak. Egyes lehetnek rendszerverzióval ellátott egymástól függetlenül.
> 
> 

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>Az alkalmazás leírása
Az alkalmazás jegyzékében deklarációval ismerteti az alkalmazástípus és -verzió. Azt adja meg a szolgáltatás összeállítás metaadatai köztük, példányok száma vagy replikációs tényező, biztonsági/elkülönítési házirenddel, placement Constraints korlátozásokat, konfigurációs felülbírálások, és a bennük foglalt szolgáltatástípusok stabil nevét. A terheléselosztás tartományok, amelybe helyezkedik el az alkalmazást is ismerteti.

Emiatt alkalmazásjegyzéket az alkalmazás szintjén elemeket ismerteti, és egy vagy több szolgáltatás jegyzékfájlokat alkalmazástípust írása hivatkozik. Íme egy egyszerű példa alkalmazásjegyzék:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
    <ConfigOverrides/>
    <EnvironmentOverrides CodePackageRef="MyCode"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
```

Szolgáltatás jegyzékfájlban, például **verzió** attribútumok strukturálatlan karakterláncok, és a rendszer nem elemzi a rendszer. Attribútumok verziója is használt verziója minden egyes összetevő frissítésre.

**ServiceManifestImport** szolgáltatás jegyzékfájlokban, ez az alkalmazástípus alkotó mutató hivatkozásokat tartalmaz. Importált service jegyzékfájlokban meghatározására, hogy milyen típusú szolgáltatásokat érvényes ez az alkalmazástípus belül. A ServiceManifestImport belül bírálja felül konfigurációs ServiceManifest.xml fájlok Settings.xml és környezeti változók értékeit. 


**DefaultServices** szolgáltatáspéldány, amikor egy alkalmazás létrejön az alkalmazás típusa alapján automatikusan létrehozott deklarál. Alapértelmezett szolgáltatások csupán a könnyebb elérhetőség érdekében, és minden tekintetben normál szolgáltatások viselkednek, azok létrehozását követően. Azok az alkalmazáspéldány bármely más szolgáltatásokkal együtt verzióra frissíti, és is távolíthatók el.

> [!NOTE]
> Az alkalmazás jegyzékében több szolgáltatás nyilvánvaló imports és alapértelmezett szolgáltatások tartalmazhat. Minden egyes importált jegyzékből szolgáltatás egymástól függetlenül lehet rendszerverzióval ellátott.
> 
> 

Különböző alkalmazás-és egyedi környezetek paramétereket, lásd: [alkalmazás paramétereinek több környezet kezelése](service-fabric-manage-multiple-environment-app-configuration.md).

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Következő lépések
[Egy alkalmazás becsomagolása](service-fabric-package-apps.md) és üzembe helyezésére teszi.

[Központi telepítése és távolíthat el alkalmazásokat] [ 10] ismerteti, hogyan lehet alkalmazáspéldányok kezelése a PowerShell használatával.

[Alkalmazás paramétereinek több környezet kezelése] [ 11] paramétereket és változókat környezet különböző alkalmazáspéldányok beállításának módját ismerteti.

[Az alkalmazás biztonsági szabályzatainak konfigurálásához] [ 12] ismerteti, hogyan korlátozhatja a hozzáférést a biztonsági házirendek szolgáltatások futtatásához.

[Modellek futtató alkalmazás] [ 13] írják le a replikák (vagy példányok) telepített szolgáltatások és folyamata közötti kapcsolat.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
[13]: service-fabric-hosting-model.md
