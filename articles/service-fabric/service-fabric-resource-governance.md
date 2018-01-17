---
title: "Az Azure Service Fabric-erőforrás szabályozás megvalósításához a tárolók és a szolgáltatások |} Microsoft Docs"
description: "Az Azure Service Fabric teszi erőforrás határértékeken belül vagy kívül tárolók futó szolgáltatásokhoz."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 974fb5bfa8b10cb5497220825b2a83ca96161b0c
ms.sourcegitcommit: a0d2423f1f277516ab2a15fe26afbc3db2f66e33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="resource-governance"></a>Erőforrás-irányítás 

Amikor több szolgáltatás ugyanazon a csomópont vagy fürt futtatja, nem lehetséges, hogy egy szolgáltatás előfordulhat, hogy több erőforrást, a folyamat során más szolgáltatások starving. Ez a probléma a "zajos szomszédos" probléma nevezzük. Az Azure Service Fabric lehetővé teszi, hogy a fejlesztő adhatja meg a fenntartásokat és határon belül az egyes erőforrások biztosítása és erőforrás-használatát korlátozása.

> Mielőtt folytatná ezt a cikket, azt javasoljuk, hogy Ismerkedjen meg a [Service Fabric-alkalmazás modell](service-fabric-application-model.md) és a [Service Fabric üzemeltetési modell](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Erőforrás-irányítás metrikák 

Erőforrás-irányítás támogatott a Service Fabric összhangban a [szolgáltatáscsomag](service-fabric-application-model.md). A service-csomag hozzárendelt erőforrások tovább oszthatók kód csomagok között. A megadott erőforrás-korlátozások is jelentheti a Foglalás erőforrást. A Service Fabric támogatja a Processzor és memória mennyisége szolgáltatáscsomagot, adja meg a két beépített [metrikák](service-fabric-cluster-resource-manager-metrics.md):

* *CPU* (metrika neve `servicefabric:/_CpuCores`): a gazdagépen rendelkezésre álló logikai alapszintű. Összes csomópont összes mag van súlyozott azonos.

* *Memória* (metrika neve `servicefabric:/_MemoryInMB`): memória megabájtban van kifejezve, és hozzárendeli őket a gépen rendelkezésre álló fizikai memória.

E két metrikáihoz [fürt erőforrás-kezelő](service-fabric-cluster-resource-manager-cluster-description.md) követi nyomon a fürt teljes kapacitás, a fürt minden csomópontja, és a fürt többi erőforrások terhelése. A két metrikák felhasználói vagy egyéni metrika egyenértékűek. Az összes meglévő szolgáltatása velük használható:
* A fürt lehet [elosztott terhelésű](service-fabric-cluster-resource-manager-balancing.md) megfelelően a két metrikák (alapértelmezés).
* A fürt lehet [töredezettségmentesíteni](service-fabric-cluster-resource-manager-defragmentation-metrics.md) megfelelően két metrikákat.
* Ha [fürt leíró](service-fabric-cluster-resource-manager-cluster-description.md), pufferelt kapacitás állíthat be két metrikákat.

[Dinamikus terheléselosztó jelentéskészítési](service-fabric-cluster-resource-manager-metrics.md) nem támogatott a következő metrikák tekintetében, és betölti a fenti metrikák létrehozáskor vannak meghatározva.

## <a name="resource-governance-mechanism"></a>Erőforrás-irányítás mechanizmus

A Service Fabric-futtatókörnyezet jelenleg nem biztosít tartozó erőforrásokat. A folyamat vagy egy tároló már meg van nyitva, a futtatókörnyezet az erőforrás-korlátozások a létrehozáskor meghatározott terhelések állítja. Továbbá a futtatókörnyezet elutasítja a rendelkezésre álló erőforrások túllépése esetén új service-csomagok megnyitása. Jobb megértése érdekében a folyamat működéséről, vegyünk egy példát a csomópont két Processzormagok (memória irányításhoz mechanizmus az egyenértékű):

1. Először egy tárolót a csomópont, a kért processzormag helyezkedik el. A futtatókörnyezet nyitja meg a tároló, és egy alapvető értékűre állítja be a CPU-korlátot. A tároló nem fogja tudni használni a több mag.

2. Ezután egy replikát készít a szolgáltatás el van helyezve a csomópont, és a megfelelő service-csomag határozza meg a maximális egy Processzormagok száma. A futtatókörnyezet megnyílik a kódcsomag, és egy alapvető értékűre állítja be a CPU-korlátot.

Ezen a ponton korlátok összege a kapacitás, a csomópont értéke. A folyamat és a tároló egy alapvető futtatását és nem ütköznének egymással. A Service Fabric nem helyezhető el, több tárolók vagy replikák a CPU-korlátot megadásakor.

Van azonban két olyan helyzet, amelyben más folyamatok előfordulhat, hogy a Processzor ugyanazért. Ezekben a helyzetekben a folyamat és a fenti példában egy tárolót is szembesülhet a zajos szomszédos problémát:

* *Az irányadó, és nem szabályozott szolgáltatások és a tárolók*: Ha egy felhasználó létrehoz egy szolgáltatást a megadott erőforrás-irányítás nélkül, a futtatókörnyezet, erőforrások a felhasználó megkeresheti azt, és ebben a példában a csomóponton helyről. Ebben az esetben az új folyamat hatékonyan igényel, néhány CPU csökkenti a csomóponton már futó szolgáltatásokat. Nincsenek két a probléma megoldása. Nem keverhetők szabályoz, és nem szabályozott szolgáltatások ugyanazon a fürtön, vagy használjon [placement Constraints korlátozásokat](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) , hogy a két típusú szolgáltatások ugyanazokat a csomópontok a nem végződhet.

* *Ha egy másik folyamat elindult a csomópont, a Service Fabric (például az operációs rendszer szolgáltatás) kívül*: Ebben a helyzetben a Service Fabric kívüli folyamat is állítja processzor meglévő szolgáltatásokkal. Ez a probléma megoldása csomópont kapacitások megfelelően a fiókot az operációs rendszer terhelés a beállítása, a következő szakaszban ismertetett módon.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Erőforrás-irányítás engedélyezéséhez a fürt beállítása

Egy csomópont indításakor, és csatlakozik a fürthöz, a Service Fabric észleli a rendelkezésre álló memória mennyisége és az elérhető magok száma, és ezután beállítja a csomópont-kapacitás az adott két erőforrás. 

Az operációs rendszerhez és más folyamatokkal pufferterületet, hogy előfordulhat, hogy fut a csomóponton, a Service Fabric csak 80 %-a rendelkezésre álló erőforrások használja a csomóponton. A konfigurálható, és módosíthatja a fürtjegyzékben. 

Íme egy példát utasíthatja a Service Fabric 50 %-a rendelkezésre álló Processzorkapacitást és 70 %-a rendelkezésre álló memória használatára: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Csomópont kapacitások teljes manuális telepítési módra kell, ha a rendszeres mechanizmus használata a fürt csomópontjaihoz leíró. Íme egy példa bemutatja, hogyan állíthatja be a négy magok csomópont és a 2 GB memória: 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Ha engedélyezve van a rendelkezésre álló erőforrások automatikus észlelést, és a csomópont-kapacitás manuálisan meghatározása a fürtjegyzékben, a Service Fabric ellenőrzi, hogy a csomópont rendelkezik-e elegendő kapacitással a felhasználó által megadott támogatásához szükséges erőforrások:
* Ha a jegyzékben meghatározott csomópont-kapacitás nagyobb, mint a rendelkezésre álló erőforrások a csomóponton, a Service Fabric a kapacitás, a jegyzékben megadott használja.

* Ha a jegyzékben meghatározott csomópont-kapacitás nagyobb, mint a rendelkezésre álló erőforrások, a Service Fabric használja a rendelkezésre álló erőforrások csomópont-kapacitás.

Automatikus észlelés rendelkezésre álló erőforrások is lehet kikapcsolni, ha nincs szükség. Kapcsolja ki, módosítsa a következő beállítást:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Az optimális teljesítmény érdekében az alábbi beállítást is be kell kapcsolni a fürtjegyzékben: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specify-resource-governance"></a>Adja meg az erőforrás-irányítás 

Erőforrás-irányítás határértékeken vannak megadva az alkalmazásjegyzékben (ServiceManifestImport szakaszát), a következő példában látható módon:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```
  
Ebben a példában a szolgáltatáscsomag nevű **ServicePackageA** egy alapvető lekérdezi a csomópontokon, ahol el van helyezve. A szolgáltatás csomagban két kód csomagok (**CodeA1** és **CodeA2**), és adja meg, mindkét a `CpuShares` paraméter. CpuShares 512:256 aránya a core osztja a két kód csomagok között. 

Így ebben a példában CodeA1 beolvasása, amely alapszintű, és CodeA2 lekérdezi egyharmad részére alapszintű (és ugyanazt a soft-garancia lefoglalása). Ha CpuShares kód csomagok nincsenek megadva, a Service Fabric osztja egyaránt közöttük magok.

Memóriakorlátokat olyan abszolút, úgy, hogy mindkét kód csomag legfeljebb 1024 MB memória (és ugyanazt a soft-garancia lefoglalása). Kód csomagok (tárolók és folyamatok) nem foglalható le ezt a határt, és hajtsa végre a kevés memória kivétel Igen eredményez próbál-nál több memóriát. Az erőforráskorlát érvényesítéséhez a szolgáltatáscsomagokban lévő minden kódcsomaghoz memóriakorlátokat kell meghatároznia.

### <a name="using-application-parameters"></a>Alkalmazás paraméterek használata

Erőforrás-irányítás megadása esetén használható [alkalmazás paraméterei](service-fabric-manage-multiple-environment-app-configuration.md) több alkalmazás konfiguráció kezeléséhez. A következő példa bemutatja, az alkalmazás paraméterek használatát:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

Ebben a példában alapértelmezett paraméter értéke éles környezetben, ahol visszajelzést kap minden szolgáltatáscsomagot, 4 magos és 2 GB memóriát. Alapértelmezett értékeket módosíthatja a alkalmazásparaméter-fájlokat is. Ebben a példában egy paraméterfájl használható az alkalmazás helyi teszteléséhez ahol azt visszajelzést kap kevesebb erőforrást éles környezetben: 

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT] 
> Alkalmazás paraméterek megadásával erőforrás eszközhitelesítéssel érhető el a Service Fabric 6.1-es verziójával kezdve.<br> 
>
> Alkalmazás paraméterek megadásához erőforrás irányítás használata esetén a Service Fabric 6.1-es vagy korábbi verzióra nem léptethető vissza. 


## <a name="other-resources-for-containers"></a>Egyéb erőforrások a tárolók
Processzor és memória mellett is lehet más tárolók erőforrás korlátairól adja meg. Ezek a korlátozások vannak megadva a kódcsomag szinten, és vonatkoznak, a tároló indításakor. Ellentétben a CPU és memória, fürt erőforrás-kezelő nincs tisztában legyen ezekkel az erőforrásokkal és nem ne bármely kapacitás ellenőrzések vagy terheléselosztás számukra. 

* *MemorySwapInMB*: egy tárolót használó swap memória mennyisége.
* *MemoryReservationInMB*: csak akkor, amikor memória versengés észleli a csomópont kényszerített memória irányításhoz enyhe korlátot.
* *CpuPercent*: A CPU, a tárolót használó aránya. Ha a szolgáltatáscsomag CPU-korlátok vannak megadva, ez a paraméter hatékonyan figyelmen kívül.
* *MaximumIOps*: A maximális iops-érték, amellyel a tároló (olvasási és írási).
* *MaximumIOBytesps*: A maximális IO (bájt / mp), amellyel a tároló (olvasási és írási).
* *BlockIOWeight*: A blokk IO-súlyozási a más tárolók viszonyítva.

Ezeket az erőforrásokat CPU és memória kombinálva. Íme egy példa bemutatja, hogyan adhatja meg a további erőforrásokat a tárolók:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>További lépések
* További tudnivalók fürt erőforrás-kezelő, olvassa el [vezet be, a Service Fabric fürt erőforrás-kezelő](service-fabric-cluster-resource-manager-introduction.md).
* További információ a alkalmazásmodell, szervizcsomagok és kód csomagok--, és hogyan replikák leképezi őket--olvasásához [minta egy alkalmazás a Service Fabric](service-fabric-application-model.md).
