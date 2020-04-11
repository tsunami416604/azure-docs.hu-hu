---
title: Tárolók és szolgáltatások erőforrás-szabályozása
description: Az Azure Service Fabric lehetővé teszi, hogy erőforráskorlátokat adjon meg a tárolókon belül vagy kívül futó szolgáltatásokhoz.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 11ca6e29829d911717a829b3e4dee0a190856a52
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115148"
---
# <a name="resource-governance"></a>Erőforrások szabályozása

Ha ugyanazon a csomóponton vagy fürtön több szolgáltatást futtat, lehetséges, hogy egy szolgáltatás több erőforrást használ fel, és a folyamat más szolgáltatásait is kiéhezteti. Ezt a problémát "zajos szomszéd" problémának nevezzük. Az Azure Service Fabric lehetővé teszi a fejlesztő számára, hogy szolgáltatásonként foglalásokat és korlátozásokat adjon meg az erőforrások garantálása és az erőforrás-használat korlátozása érdekében.

> Mielőtt folytatná ezt a cikket, azt javasoljuk, hogy ismerkedjen meg a [Service Fabric alkalmazásmodell](service-fabric-application-model.md) és a [Service Fabric üzemeltetési modell.](service-fabric-hosting-model.md)
>

## <a name="resource-governance-metrics"></a>Erőforrás-irányítási mutatók

Az erőforrás-szabályozás a Service Fabric a [szolgáltatáscsomagnak](service-fabric-application-model.md)megfelelően támogatott. A szolgáltatáscsomaghoz rendelt erőforrások tovább oszthatók a kódcsomagok között. A megadott erőforráskorlátok az erőforrások foglalását is jelentik. A Service Fabric támogatja a processzor és a memória szolgáltatáscsomagonkénti megadását két beépített [metrikával:](service-fabric-cluster-resource-manager-metrics.md)

* *CPU* (metrika név): `servicefabric:/_CpuCores`A gazdagépen elérhető logikai mag. Az összes csomópont összes magjának súlyozása megegyezik.

* *Memória* (metrikanév): `servicefabric:/_MemoryInMB`A memória megabájtban van kifejezve, és a számítógépen elérhető fizikai memóriára van leképezve.

A [fürterőforrás-kezelő](service-fabric-cluster-resource-manager-cluster-description.md) e két metrika esetében nyomon követi a fürt teljes kapacitását, a fürt egyes csomópontjainak terhelését és a fürt fennmaradó erőforrásait. Ez a két mutató egyenértékű bármely más felhasználói vagy egyéni metrikával. Az összes meglévő funkció használható velük:

* A fürt [e](service-fabric-cluster-resource-manager-balancing.md) két metrika (alapértelmezett viselkedés) szerint kiegyenlíthető.
* A fürt e két metrika szerint [töredezettségmentesíthető.](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* [A fürt leírásakor](service-fabric-cluster-resource-manager-cluster-description.md)pufferelt kapacitás állítható be erre a két mutatóra.

> [!NOTE]
> [A dinamikus terhelésjelentés](service-fabric-cluster-resource-manager-metrics.md) nem támogatott ezeknél a mutatóknál; ezekhez a metrikákhoz a terhelések a létrehozás időpontjában vannak meghatározva.

## <a name="resource-governance-mechanism"></a>Erőforrás-irányítási mechanizmus

A Service Fabric futásidejű jelenleg nem biztosít erőforrások foglalását. Folyamat vagy tároló megnyitásakor a futásidejű beállítja az erőforrás-korlátokat a létrehozáskor meghatározott terhelésekre. Továbbá a futásidejű elutasítja az erőforrások túllépése esetén elérhető új szolgáltatáscsomagok megnyitását. A folyamat működésének jobb megértéséhez vegyünk egy példát egy két processzormagot ú csomópontra (a memóriairányítási mechanizmus egyenértékű):

1. Először egy tároló kerül a csomópontra, és egy PROCESSZOR-magot kér. A futásidejű megnyitja a tárolót, és beállítja a CPU-korlátot egy magra. A tároló nem fog tudni egynél több magot használni.

2. Ezután egy szolgáltatás replikája kerül a csomópontra, és a megfelelő szolgáltatáscsomag egy PROCESSZORmag korlátját határozza meg. A futásidejű megnyitja a kódcsomagot, és a CPU-korlátot egy magra állítja be.

Ezen a ponton a korlátok összege megegyezik a csomópont kapacitásával. Egy folyamat és egy tároló fut egy-egy maggal, és nem zavarja egymást. A Service Fabric nem helyez el több tárolót vagy replikát, amikor a CPU-korlátot adja meg.

Azonban két olyan helyzet van, amelyben más folyamatok is megküzdhetnek a CPU-val. Ezekben a helyzetekben egy folyamat és egy tároló a példánkból előfordulhat, hogy a zajos szomszéd probléma:

* *A szabályozott és nem szabályozott szolgáltatások és tárolók keverése:* Ha a felhasználó erőforrás-szabályozás nélkül hoz létre egy szolgáltatást, a futásidejű úgy látja, hogy nem fogyaszt erőforrásokat, és a példánkban a csomópontra helyezheti. Ebben az esetben ez az új folyamat hatékonyan fogyaszt néhány CPU rovására a szolgáltatások, amelyek már futnak a csomóponton. Erre a problémára két megoldás létezik. Vagy ne keverje a szabályozott és nem szabályozott szolgáltatásokat ugyanazon a fürtön, vagy használjon [elhelyezési korlátozásokat,](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) hogy ez a két szolgáltatástípus ne kerüljön ugyanazon a csomópontcsoportba.

* *Ha egy másik folyamat indul el a csomóponton, a Szolgáltatásfabric en kívül (például egy operációs rendszer szolgáltatás)*: Ebben a helyzetben a folyamat a Service Fabric-en kívül is verseng a PROCESS meglévő szolgáltatásokkal. A probléma megoldása az, hogy állítsa be a csomópont kapacitások megfelelően figyelembe os terhelés, ahogy az a következő szakaszban látható.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Fürtbeállítása az erőforrás-szabályozás engedélyezéséhez

Amikor egy csomópont elindul, és csatlakozik a fürthöz, a Service Fabric észleli a rendelkezésre álló memória mennyisége és a rendelkezésre álló magok száma, majd beállítja a csomópont kapacitása a két erőforrás.

Az operációs rendszer pufferterületének meghagyása, és más folyamatok számára a csomóponton futó Service Fabric csak a csomóponton rendelkezésre álló erőforrások 80%-át használja. Ez a százalék konfigurálható, és módosítható a fürtjegyzékben.

Íme egy példa arra, hogyan utasíthatja a Service Fabric-et a rendelkezésre álló PROCESSZOR 50%-ának és a rendelkezésre álló memória 70%-ának használatára:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

A legtöbb ügyfél és forgatókönyv esetében a processzor és a memória csomópontkapacitásának automatikus észlelése az ajánlott konfiguráció (az automatikus észlelés alapértelmezés szerint be van kapcsolva). Ha azonban a csomópontkapacitások teljes manuális beállítására van szüksége, konfigurálhatja azokat csomópontonkénti típusonként a fürt csomópontjainak leírására szolgáló mechanizmus használatával. Íme egy példa a csomóponttípus négy maggal és 2 GB memóriával történő beállítására:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Ha a rendelkezésre álló erőforrások automatikus észlelése engedélyezve van, és a csomópont kapacitásai manuálisan vannak definiálva a fürtjegyzékben, a Service Fabric ellenőrzi, hogy a csomópont rendelkezik-e elegendő erőforrással a felhasználó által definiált kapacitás támogatásához:

* Ha a jegyzékben definiált csomópontkapacitások kisebbek vagy egyenlőek a csomóponton rendelkezésre álló erőforrásokkal, akkor a Service Fabric a jegyzékben megadott kapacitásokat használja.

* Ha a jegyzékben definiált csomópontkapacitások nagyobbak a rendelkezésre álló erőforrásoknál, a Service Fabric a rendelkezésre álló erőforrásokat használja csomópontkapacitásként.

A rendelkezésre álló erőforrások automatikus észlelése kikapcsolható, ha nem szükséges. A kikapcsoláshoz módosítsa a következő beállítást:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Az optimális teljesítmény érdekében a fürtjegyzékben a következő beállítást is be kell kapcsolni:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> A Service Fabric 7.0-s verziójával kezdve frissítettük a csomópont erőforrás-kapacitásainak kiszámítására vonatkozó szabályt azokban az esetekben, amikor a felhasználó manuálisan adja meg a csomópont erőforrás-kapacitások értékeit. Vegyük figyelembe a következő forgatókönyvet:
>
> * Jelenleg 10 cpu magok összesen a csomóponton
> * Az SF úgy van beállítva, hogy a teljes erőforrás 80%-át használja a felhasználói szolgáltatásokhoz (alapértelmezett beállítás), így a csomóponton futó egyéb szolgáltatások (beleértve a Service Fabric rendszerszolgáltatásait is) 20%-os puffert hagynak.
> * A felhasználó úgy dönt, hogy manuálisan felülbírálja a csomópont erőforrás-kapacitása a cpu magok metrika, és beállítja, hogy 5 mag
>
> Módosítottuk a Service Fabric felhasználói szolgáltatásainak rendelkezésre álló kapacitásának kiszámítására vonatkozó szabályt a következő módon:
>
> * A Service Fabric 7.0 előtt a felhasználói szolgáltatások rendelkezésre álló kapacitása **5 magra** lesz számítva (a 20%-os kapacitáspuffert figyelmen kívül hagyja)
> * A Service Fabric 7.0-s verziótól kezdve a felhasználói szolgáltatások rendelkezésre álló kapacitása **4 magra** lesz számítva (a 20%-os kapacitáspuffert nem hagyja figyelmen kívül)

## <a name="specify-resource-governance"></a>Erőforrás-szabályozás megadása

Az erőforrás-irányítási korlátok az alkalmazásjegyzékben (ServiceManifestImport szakasz) vannak megadva, ahogy az a következő példában látható:

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

Ebben a példában a **ServicePackageA** nevű szolgáltatáscsomag lekéri az egyik mag a csomópontokon, ahol van elhelyezve. Ez a szolgáltatáscsomag két kódcsomagot tartalmaz (**CodeA1** és `CpuShares` **CodeA2**), és mindkettő megadja a paramétert. A CpuShares 512:256 aránya elosztja a magot a két kódcsomag között.

Így ebben a példában a CodeA1 egy mag kétharmadát kapja, a CodeA2 pedig egy mag egyharmadát (és egy puha garancia foglalását). Ha a CpuShares nincs megadva a kódcsomagokhoz, a Service Fabric egyenlően osztja el a magok közöttük.

A memóriakorlátok abszolútak, így mindkét kódcsomag 1024 MB memóriára korlátozódik (és egy ideiglenes garancia esetén). A kódcsomagok (tárolók vagy folyamatok) nem foglalhatnak le több memóriát ennél a korlátnál, és ennek megkísérlése memóriazavar-kivételt eredményez. Az erőforráskorlát érvényesítéséhez a szolgáltatáscsomagokban lévő minden kódcsomaghoz memóriakorlátokat kell meghatároznia.

### <a name="using-application-parameters"></a>Alkalmazásparaméterek használata

Erőforrás-irányítási beállítások megadásakor [alkalmazásparaméterek](service-fabric-manage-multiple-environment-app-configuration.md) használatával több alkalmazáskonfigurációk kezelésére. A következő példa az alkalmazásparaméterek használatát mutatja be:

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

Ebben a példában az alapértelmezett paraméterértékek az éles környezetben vannak beállítva, ahol minden egyes Service Package 4 magot és 2 GB memóriát kapna. Az alapértelmezett értékek et alkalmazásparaméter-fájlokkal lehet módosítani. Ebben a példában egy paraméterfájl használható az alkalmazás helyi tesztelésére, ahol kevesebb erőforrást kapna, mint az éles környezetben:

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
> Erőforrás-szabályozás megadása alkalmazásparaméterekkel érhető el a Service Fabric 6.1-es verziójától kezdve.<br>
>
> Ha alkalmazásparamétereket használ az erőforrás-szabályozás megadásához, a Service Fabric nem minősíthető vissza a 6.1-es verzió előtti verzióra.

## <a name="enforcing-the-resource-limits-for-user-services"></a>A felhasználói szolgáltatások erőforráskorlátainak érvényesítése

Erőforrás-szabályozás a Service Fabric-szolgáltatások alkalmazása garantálja, hogy ezek az erőforrások által szabályozott szolgáltatások nem haladhatja meg az erőforrás-kvótát, sok felhasználó továbbra is kell futtatni a Service Fabric-szolgáltatások egy részét szabályozatlan módban. Szabályozatlan Service Fabric-szolgáltatások használata esetén olyan helyzetekbe ütközhet, ahol a "runaway" nem szabályozott szolgáltatások a Service Fabric-csomópontokon rendelkezésre álló összes erőforrást igénybe veszik, ami olyan súlyos problémákhoz vezethet, mint például:

* A csomópontokon futó egyéb szolgáltatások (beleértve a Service Fabric rendszerszolgáltatásait) erőforrás-éhezése
* A csomópontok egészségtelen állapotban végződnek
* Nem válaszol a Service Fabric fürtfelügyeleti API-k

A helyzetek bekövetkezésének megakadályozása érdekében a Service Fabric lehetővé teszi, hogy a csomóponton futó (szabályozott és szabályozatlan) *összes Service Fabric felhasználói szolgáltatás erőforráskorlátait kényszerítse,* hogy garantálja, hogy a felhasználói szolgáltatások soha nem fognak többet használni, mint a megadott mennyiségű erőforrás. Ez úgy érhető el, hogy az EnforceUserServiceMetricCapacityes konfiguráció értékét a ClusterManifest PlacementAndLoadBalancing szakaszában true értékre állítja. Ez a beállítás alapértelmezés szerint ki van kapcsolva.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

További megjegyzések:

* Az erőforrás-korlátozás kényszerítése csak a és `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` az erőforrás-mérőszámokra vonatkozik.
* Az erőforrás-korlátozás kényszerítése csak akkor működik, ha az erőforrás-metrikák csomópontkapacitásaelérhető a Service Fabric számára, akár automatikus észlelési mechanizmuson keresztül, akár a felhasználókon keresztül, akik manuálisan határozzák meg a csomópontkapacitásokat (afürt beállításában az [erőforrás-irányítási szakasz engedélyezéséhez).](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance)Ha a csomópont kapacitása nincs konfigurálva, az erőforrás-korlátozás kényszerítési képesség nem használható, mert a Service Fabric nem tudja, hogy mennyi erőforrást kell lefoglalni a felhasználói szolgáltatások számára.A Service Fabric állapotfigyelmeztetést ad ki, ha az "EnforceUserServiceMetricCapacityes" igaz, de a csomópont kapacitásanincs konfigurálva.

## <a name="other-resources-for-containers"></a>Tárolók egyéb erőforrásai

A PROCESSZOR és a memória mellett más erőforráskorlátokat is megadhat a tárolókhoz. Ezek a korlátok a kódcsomag szintjén vannak megadva, és a tároló indításakor kerülnek alkalmazásra. A processzorral és a memóriával ellentétben a fürterőforrás-kezelő nem ismeri ezeket az erőforrásokat, és nem végez kapacitásellenőrzést vagy terheléselosztást.

* *MemorySwapInMB*: A tároló által használható felcserélő memória mennyisége.
* *MemoryReservationInMB*: A memória-szabályozás lágy korlátja, amely csak akkor van érvényben, ha a rendszer memóriaversengést észlel a csomóponton.
* *CpuPercent*: A tároló által használható processzor százaléka. Ha a szolgáltatáscsomaghoz processzorkorlátok vannak megadva, a paramétert a rendszer gyakorlatilag figyelmen kívül hagyja.
* *MaximumIOps:* A tároló által használható maximális IOPS (olvasási és írási).
* *MaximumIOBytesps*: A tároló által használható maximális IO (bájt/másodperc) (olvasási és írási).
* *BlockIOWeight*: A blokk I/O-súlya más tárolókhoz viszonyítva.

Ezek az erőforrások kombinálhatók a PROCESSZORRAL és a memóriával. Íme egy példa arra, hogyan adhat meg további erőforrásokat a tárolókhoz:

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

* Ha többet szeretne megtudni a fürterőforrás-kezelőről, olvassa el [a Bemutatkozás a Service Fabric-fürt erőforrás-kezelőjének bemutatása](service-fabric-cluster-resource-manager-introduction.md)című című.
* Ha többet szeretne megtudni az alkalmazásmodellről, a szolgáltatáscsomagokról és a kódcsomagokról – és arról, hogy a replikák hogyan lesznek leképezve rájuk –, olvassa el [az alkalmazás modellezése a Service Fabric alkalmazásban.](service-fabric-application-model.md)
