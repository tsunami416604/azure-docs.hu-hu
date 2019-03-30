---
title: Az Azure Service Fabric erőforrás-szabályozása tárolók és szolgáltatások |} A Microsoft Docs
description: Az Azure Service Fabric lehetővé teszi, hogy a belső vagy külső tárolók futó szolgáltatásokhoz erőforráskorlátok adja meg.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: aljo, subramar
ms.openlocfilehash: e011554e61411fddca034f024c30c2270593e07b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669248"
---
# <a name="resource-governance"></a>Erőforrások szabályozása

Ha több szolgáltatást ugyanarra a csomópont vagy fürtön futtatja, lehetséges, hogy egy szolgáltatás előfordulhat, hogy több erőforrást, a folyamat során más szolgáltatások starving. Ez a probléma a "zajos szomszédok" okozta nevezzük. Az Azure Service Fabric lehetővé teszi, hogy a fejlesztő adja meg a foglalások és a szolgáltatási erőforrások biztosítása és erőforrás-használat korlátozása vonatkozó korlátok.

> Ez a cikk a folytatás előtt javasoljuk, hogy Ön ismerkedhet meg vele a [Service Fabric-alkalmazásmodell](service-fabric-application-model.md) és a [Service Fabric üzemeltetési modell](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Erőforrás-szabályozási mérőszámok

A Service Fabric-szolgáltatást a támogatott erőforrás-szabályozása a [szolgáltatáscsomag](service-fabric-application-model.md). A csomag hozzárendelt erőforrások közötti kódcsomagok tovább oszthatók. A megadott erőforráskorlátok is jelentheti a foglalást az erőforrások. A Service Fabric támogatja a Processzor- és szolgáltatás csomagonként megadása két beépített [metrikák](service-fabric-cluster-resource-manager-metrics.md):

* *CPU* (metrika neve `servicefabric:/_CpuCores`): A gazdagépen rendelkezésre álló logikai alapszintű. Az összes mag csomópontokon vannak súlyozott azonos.

* *Memória* (metrika neve `servicefabric:/_MemoryInMB`): Memória (MB) fejezzük ki, és a gépen rendelkezésre álló fizikai memória vannak leképezve.

A következő két metrikákhoz [fürterőforrás-kezelő](service-fabric-cluster-resource-manager-cluster-description.md) nyomon követi a fürt teljes kapacitása, a fürt egyes csomópontjaihoz, és a fürtben lévő további erőforrásokat érő terhelést. E két mérőszám semmilyen más felhasználó- vagy egyéni metrika egyenértékűek. Az összes meglévő funkciók velük használhatók:

* A fürt lehet [elosztott terhelésű](service-fabric-cluster-resource-manager-balancing.md) megfelelően két metrikák (alapértelmezés).
* A fürt lehet [töredezettségmentesíteni](service-fabric-cluster-resource-manager-defragmentation-metrics.md) két metrikák alapján.
* Amikor [fürt leírása](service-fabric-cluster-resource-manager-cluster-description.md), pufferelt kapacitás állítható be az alábbi két mérőszám.

[Dinamikus terhelésjelentés-készítés](service-fabric-cluster-resource-manager-metrics.md) nem támogatott a következő metrikákhoz, és betölti a metrikák létrehozáskor vannak definiálva.

## <a name="resource-governance-mechanism"></a>Erőforrás-szabályozási mechanizmus

A Service Fabric-futtatókörnyezet jelenleg nem biztosít erőforrásokat foglalás. Ha egy folyamat vagy egy tároló meg van nyitva, a futtatókörnyezet a erőforráskorlátok a terheléseket, amelyek a rendszer a létrehozáskor kell meghatározni állítja be. Továbbá a futásidejű elutasítja nyithatja meg az új szolgáltatáscsomagok a rendelkezésre álló erőforrások túllépése esetén. Segít jobban megérteni, hogyan működik, a folyamat, vegyünk egy példát a két processzormag-csomópont (memória cégirányítási mechanizmus az ezzel egyenértékű):

1. Először egy tárolót a csomóponton, egy processzormagot kérő kerül. A futtatókörnyezet megnyitja a tároló és a CPU-korlát beállítása egy magot. A tároló nem tudják használni, több processzormag.

2. Ezután egy replikát, a szolgáltatás el van helyezve a csomópontot, és a megfelelő csomag megadja egy processzormagot a korlát. A futtatókörnyezet a kódcsomag megnyílik, és a egy magot állítja be a CPU-korlátot.

A korlátok összege ezen a ponton megegyezik a csomópont kapacitását. A folyamat és a egy tároló és a egy magot fut és nem ütközik egymással. A CPU-korlát megadásakor a Service Fabric több tárolók vagy replikák nem helyez.

Azonban két olyan helyzet van, amelyben más folyamatok CPU előfordulhat, hogy ugyanazért. Ebben az esetben egy folyamat és a egy tárolót a jelen példában tapasztalhat a zajos szomszédok problémát:

* *Szabályozott, és nem szabályozott szolgáltatások és a tárolók*: Ha egy felhasználó létrehoz egy szolgáltatást bármely megadott erőforrás-szabályozása nélkül, a futtatókörnyezet, nincsenek erőforrások felhasználó látja azokat, és helyezheti ebben a példában a csomóponton. Ebben az esetben ez az új folyamat hatékonyan néhány CPU, a szolgáltatások, a csomóponton már futó rovására használ. Nincsenek erre a problémára két megoldás. Nem használhatók vegyesen szabályozott, és nem szabályozott szolgáltatások ugyanazon a fürtön, vagy használja [elhelyezési korlátozások](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) úgy, hogy a két típusú szolgáltatások végül nem a csomópontok ugyanazon az adatkészleten.

* *Ha egy másik folyamat elindul a csomóponton kívülre (például az operációs rendszer szolgáltatás). a Service Fabric*: Ebben a helyzetben a folyamat a Service Fabric kívül is állítja processzor meglévő szolgáltatásokkal. A megoldás erre a problémára, hogy fürt(ök) megfelelően a fiókot az operációs rendszer terhelés a beállítása, a következő szakaszban látható módon.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Fürt beállítása az erőforrás-szabályozás engedélyezése

Ha egy csomópont elindul, és csatlakozik a fürthöz, Service Fabric észleli a rendelkezésre álló memória mennyiségét és a rendelkezésre álló magok számát, és ezután beállítja az ezen két erőforrás következő fürt(ök).

Az operációs rendszerhez és más folyamatokkal pufferterületének, hogy előfordulhat, hogy fut a csomóponton, a Service Fabric csak 80 %-a rendelkezésre álló erőforrások használ a csomóponton. Ezt a százalékos arányt konfigurálható, és a fürtjegyzékben is módosítható.

A következő példa bemutatja, hogyan arra utasítani a Service Fabric 50 %-a rendelkezésre álló Processzor- és 70 %-a rendelkezésre álló memória használatára:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

A csomópont kapacitások teljes manuális telepítésre van szüksége, ha a rendszeres mechanizmus a fürt csomópontjainak leíró is használhat. A következő példa bemutatja, hogyan állíthatja be a négy magot csomópont és a 2 GB memória:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Amikor engedélyezve van a rendelkezésre álló erőforrások automatikus észlelését, és fürt(ök) manuálisan a fürtjegyzékben definiált, a Service Fabric ellenőrzi, hogy a csomópont rendelkezik-e elegendő erőforrás a kapacitást, amelyhez a felhasználó által megadott támogatásához:

* Ha a jegyzékfájlban definiált fürt(ök) kisebb vagy egyenlő a rendelkezésre álló erőforrások a csomóponton, Service Fabric a kapacitásokat a jegyzékfájlban megadott használ.

* Ha a jegyzékfájlban definiált csomópont-kapacitás nagyobb, mint a rendelkezésre álló erőforrások, a Service Fabric használja a rendelkezésre álló erőforrások fürt(ök).

Automatikus észlelés rendelkezésre álló erőforrások is kapcsolható ki, ha nem szükséges. Kapcsolja ki, módosítsa a következő beállítást:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Az optimális teljesítmény érdekében az alábbi beállítást is be kell kapcsolni a fürtjegyzékben:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

## <a name="specify-resource-governance"></a>Adja meg az erőforrás-szabályozása

Erőforrás-szabályozási korlátait az alkalmazásjegyzékben (ServiceManifestImport szakasz) vannak megadva, az alábbi példában látható módon:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

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

Ebben a példában a csomag nevű **ServicePackageA** egy magot kap a csomópontokon, amelyekre el van helyezve. A service-csomagban található két kódcsomagok (**CodeA1** és **CodeA2**), és mindkettő adja meg a `CpuShares` paraméter. Az időarány, amíg CpuShares 512:256 alapvető fontosságú a két kód csomagok között osztja fel.

Így ebben a példában CodeA1 lekérdezi a core kétharmada, és CodeA2 lekér egy core (és a egy garanciás foglalás) egyharmada. Ha CpuShares kód csomagok nincsenek megadva, a Service Fabric egyaránt ezek közül a Processzormagok száma osztja fel.

Memóriakorlátok abszolútak, ezért a mindkét kódcsomagok legfeljebb 1024 MB memória (és a egy garanciás foglalás). A kódcsomagok (tárolók vagy folyamatok) nem foglalható le több memóriát ezt a korlátot, és próbálja meg memóriahiány kivételt eredményez. Az erőforráskorlát érvényesítéséhez a szolgáltatáscsomagokban lévő minden kódcsomaghoz memóriakorlátokat kell meghatároznia.

### <a name="using-application-parameters"></a>Alkalmazás-paraméterek használata

Adja meg az erőforrás-szabályozás esetén használható [alkalmazásparamétereket](service-fabric-manage-multiple-environment-app-configuration.md) több alkalmazás konfiguráció kezeléséhez. Az alábbi példa bemutatja a alkalmazás paraméterek használatát:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

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

Ebben a példában az alapértelmezett paraméterértékeket vannak beállítva a éles környezetben, ahol minden szolgáltatás csomag kap 4 maggal és 2 GB memória. Alapértelmezett értékek módosítani alkalmazásparaméter-fájlok használatával lehetőség. Ebben a példában egy paraméterfájlt használható az alkalmazás helyi tesztelése amennyiben azt kap kevesebb erőforrást éles környezetben:

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
> Alkalmazás paraméterekkel megadásával erőforrás-szabályozás érhető el a Service Fabric 6.1-es verzió indítása.<br>
>
> Ha alkalmazás paraméterek használatával adja meg az erőforrás-szabályozása el, a Service Fabric 6.1-es verziónál régebbi verzióra nem lehet visszaminősíteni.

## <a name="other-resources-for-containers"></a>Egyéb erőforrások a tárolók

CPU és memória mellett a tárolók egyéb erőforráskorlátok adja meg. Ezeket a korlátokat a kódcsomag szintjén vannak megadva, és a rendszer alkalmazza a tároló elindult. Ellentétben a CPU és memória, a fürterőforrás-kezelő nem ezeket az erőforrásokat, és nem bármelyik kapacitásának ellenőrzéséhez tegye vagy terheléselosztás, a számukra.

* *MemorySwapInMB*: Egy tárolót használó lapozómemória mennyisége.
* *MemoryReservationInMB*: A memória cégirányítási csak akkor, ha a csomópontot a memória a versengés észlelt a rendszer érvényes enyhe korlát.
* *CpuPercent*: A Processzor, a tárolót használó százalékos értéke. Ha a CPU-korlátok a szolgáltatási csomag számára meg van adva, ezt a paramétert hatékonyan figyelmen kívül.
* *MaximumIOps*: A maximális iops-érték tároló használható (olvasási és írási).
* *MaximumIOBytesps*: A maximális i/o (bájt / másodperc), amellyel egy tároló (olvasási és írási).
* *BlockIOWeight*: A blokk más tárolók képest relatív súlya IO.

Ezeket az erőforrásokat a CPU és memória kombinálható is. Íme egy példa, hogyan adhat meg további források a tárolók:

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

* A fürt Resource Managerrel kapcsolatos további tudnivalókért olvassa el [a Service Fabric fürterőforrás-kezelő bemutatása](service-fabric-cluster-resource-manager-introduction.md).
* További információ az alkalmazásmodell szolgáltatáscsomagok és kódcsomagok –, és hogyan replikák leképezése őket – beolvashatja [minta Service Fabric-alkalmazásokhoz](service-fabric-application-model.md).
