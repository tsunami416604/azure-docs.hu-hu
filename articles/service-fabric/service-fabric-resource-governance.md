---
title: Tárolók és szolgáltatások erőforrás-szabályozása
description: Az Azure Service Fabric lehetővé teszi erőforrás-korlátok megadását a tárolón belüli vagy kívüli szolgáltatásokhoz.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 11ca6e29829d911717a829b3e4dee0a190856a52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81115148"
---
# <a name="resource-governance"></a>Erőforrások szabályozása

Ha több szolgáltatást futtat ugyanazon a csomóponton vagy fürtön, lehetséges, hogy az egyik szolgáltatás több erőforrást is felhasználhat, és a folyamat más szolgáltatásait is megéhezik. Ezt a problémát a "zajos szomszéd" problémának nevezzük. Az Azure Service Fabric lehetővé teszi a fejlesztőknek, hogy a szolgáltatásokban foglalásokat és korlátozásokat határozzanak meg az erőforrások biztosítása és az erőforrások használatának korlátozása érdekében.

> A cikk folytatása előtt javasoljuk, hogy ismerkedjen meg a [Service Fabric alkalmazás modelljével](service-fabric-application-model.md) és a [Service Fabric üzemeltetési modellel](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Erőforrás-irányítási mérőszámok

Az erőforrás-szabályozás Service Fabric a [szervizcsomaggal](service-fabric-application-model.md)összhangban támogatott. A szervizcsomaghoz rendelt erőforrások tovább oszthatók a csomagok között. A megadott erőforrás-korlátok az erőforrások lefoglalását is jelentik. Service Fabric támogatja a CPU és a memória megadását a szervizcsomagok esetében, két beépített [metrikával](service-fabric-cluster-resource-manager-metrics.md):

* *CPU* (metrika neve `servicefabric:/_CpuCores` ): a gazdagépen elérhető logikai mag. Az összes csomóponton lévő összes mag súlyozása azonos.

* *Memória* (metrika neve `servicefabric:/_MemoryInMB` ): a memória megabájtban van kifejezve, és a számítógépen elérhető fizikai memóriára van leképezve.

Ezen két metrika esetében a [fürterőforrás-kezelő](service-fabric-cluster-resource-manager-cluster-description.md) nyomon követi a fürt teljes kapacitását, a fürt egyes csomópontjainak terhelését, valamint a fürt többi erőforrását. Ez a két metrika egyenértékű a többi felhasználóval vagy egyéni metrikával. Az összes meglévő funkció használható együtt:

* A fürt a két metrika (alapértelmezett viselkedés) alapján [kiegyensúlyozott](service-fabric-cluster-resource-manager-balancing.md) lehet.
* A fürt a két metrika alapján is [feldarabolható](service-fabric-cluster-resource-manager-defragmentation-metrics.md) .
* [Fürt leírásakor](service-fabric-cluster-resource-manager-cluster-description.md)a rendszer pufferelt kapacitást állíthat be ehhez a két metrikához.

> [!NOTE]
> Ezeknél a metrikákban nem támogatott a [dinamikus betöltési jelentéskészítés](service-fabric-cluster-resource-manager-metrics.md) . a metrikák terhelése a létrehozáskor van meghatározva.

## <a name="resource-governance-mechanism"></a>Erőforrás-irányítási mechanizmus

A Service Fabric futtatókörnyezet jelenleg nem biztosít foglalást az erőforrásokhoz. Egy folyamat vagy tároló megnyitásakor a futtatókörnyezet beállítja az erőforrás-korlátokat a létrehozáskor definiált terhelésekre. Emellett a futtatókörnyezet elutasítja az erőforrások túllépése esetén elérhető új szervizcsomagok megnyitását. A folyamat működésének jobb megismeréséhez vessünk egy példát a két CPU-magot tartalmazó csomópontra (a memória szabályozásának mechanizmusa egyenértékű):

1. Először a rendszer egy tárolót helyez el a csomóponton, amely egy CPU-mag igénylését kéri. A futtatókörnyezet megnyitja a tárolót, és a CPU-korlátot egy mag értékre állítja. A tároló nem tud egynél több magját használni.

2. Ezután egy szolgáltatás replikája kerül a csomópontra, és a hozzá tartozó szolgáltatáscsomag egy CPU-mag korlátját határozza meg. A futtatókörnyezet megnyitja a kód csomagot, és beállítja a CPU-korlátot egy mag értékre.

Ezen a ponton a határértékek összege egyenlő a csomópont kapacitásával. Egy folyamat és egy tároló egyetlen alapszintű, és nem zavarja egymást. Service Fabric nem tartalmaz több tárolót vagy replikát a CPU-korlát megadásakor.

Van azonban két olyan eset, amikor más folyamatok is megtarthatják a CPU-t. Ilyen helyzetekben előfordulhat, hogy egy folyamat és egy tároló a példánkban a zajos szomszéd problémát tapasztalja:

* *Irányított és nem szabályozott szolgáltatások és tárolók összekeverése*: Ha egy felhasználó a megadott erőforrás-szabályozás nélkül hoz létre szolgáltatást, a futtatókörnyezet nem igényel erőforrást, és elhelyezheti a példában szereplő csomóponton. Ebben az esetben ez az új folyamat hatékonyan felhasznál bizonyos CPU-t a csomóponton már futó szolgáltatások rovására. Ennek a problémának két megoldása van. Vagy ne keverje a szabályozott és nem szabályozott szolgáltatásokat ugyanazon a fürtön, vagy használjon [elhelyezési korlátozásokat](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) úgy, hogy ez a két típusú szolgáltatás ne legyen ugyanazon a csomópontokon.

* *Ha egy másik folyamat indul el a csomóponton, Service Fabricon kívül (például egy operációsrendszer-szolgáltatáson)*: ebben az esetben a folyamaton Service Fabric kívüli folyamat a meglévő szolgáltatásokkal rendelkező CPU-ra is érvényes. Ennek a problémának a megoldása a csomópont-kapacitások megfelelő beállítása az operációs rendszer terhelésének megfelelően, a következő szakaszban látható módon.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Fürt beállítása az erőforrás-szabályozás engedélyezéséhez

Amikor egy csomópont elindul és csatlakozik a fürthöz, Service Fabric észleli a rendelkezésre álló memória mennyiségét és a magok rendelkezésre álló számát, majd beállítja a csomópontok kapacitását a két erőforrás számára.

Ha meg szeretné hagyni az operációs rendszer pufferét, és más folyamatok is futnak a csomóponton, akkor Service Fabric a csomóponton rendelkezésre álló erőforrások 80%-át használja. Ez a százalék konfigurálható, és módosítható a fürt jegyzékfájljában.

Íme egy példa arra, hogyan utasíthatja a Service Fabric a rendelkezésre álló CPU 50%-ában és a rendelkezésre álló memória 70%-ában:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

A legtöbb ügyfél és forgatókönyv esetében a processzor és a memória csomópont-kapacitásának automatikus észlelése az ajánlott konfiguráció (alapértelmezés szerint az automatikus észlelés be van kapcsolva). Ha azonban a csomópont-kapacitások teljes manuális beállítására van szüksége, a csomópontok típusait a fürt csomópontjainak leírására szolgáló mechanizmus használatával konfigurálhatja. Íme egy példa arra, hogyan állíthatja be a csomópont típusát négy maggal és 2 GB memóriával:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Ha a rendelkezésre álló erőforrások automatikus észlelése engedélyezve van, és a csomóponti kapacitások manuálisan vannak definiálva a fürt jegyzékfájljában, Service Fabric ellenőrzi, hogy a csomópont rendelkezik-e elegendő erőforrással a felhasználó által definiált kapacitás támogatásához:

* Ha a jegyzékfájlban definiált csomópont-kapacitások kisebbek vagy egyenlőek a csomópont rendelkezésre álló erőforrásaival, akkor Service Fabric a jegyzékfájlban megadott kapacitásokat használja.

* Ha a jegyzékfájlban definiált csomópont-kapacitások nagyobbak, mint a rendelkezésre álló erőforrások, Service Fabric a rendelkezésre álló erőforrásokat használja csomópont-kapacitásként.

A rendelkezésre álló erőforrások automatikus észlelése kikapcsolható, ha nincs rá szükség. A kikapcsolásához módosítsa a következő beállítást:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Az optimális teljesítmény érdekében a következő beállítást is be kell kapcsolni a fürt jegyzékfájljában:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Az 7,0-Service Fabric es verziótól kezdődően a rendszer frissítette a csomópont-erőforrás-kapacitások kiszámításának szabályát azokban az esetekben, amikor a felhasználó manuálisan megadja a csomóponti erőforrás-kapacitások értékeit. Tekintsük át a következő forgatókönyvet:
>
> * A csomóponton 10 CPU-mag van összesen.
> * Az SF úgy van konfigurálva, hogy a felhasználói szolgáltatások teljes erőforrásainak 80%-át használja (alapértelmezett beállítás), amely 20%-os puffert hagy a csomóponton futó többi szolgáltatáshoz (beleértve Service Fabric rendszerszolgáltatásokat).
> * A felhasználó úgy dönt, hogy manuálisan felülbírálja a CPU-magok metrikai csomópontjának erőforrás-kapacitását, és beállítja 5 maggal
>
> Módosítottuk azt a szabályt, hogy a Service Fabric felhasználói szolgáltatások rendelkezésre álló kapacitása a következő módon legyen kiszámítva:
>
> * A 7,0 Service Fabric előtt a felhasználói szolgáltatások rendelkezésre álló kapacitása **5 maggal** lesz kiszámítva (a 20%-os kapacitási puffer figyelmen kívül lesz hagyva)
> * A Service Fabric 7,0-től kezdődően a felhasználói szolgáltatások rendelkezésre álló kapacitása **4 mag** (a 20%-os kapacitási puffer figyelmen kívül hagyása) lesz kiszámítva.

## <a name="specify-resource-governance"></a>Erőforrás-szabályozás meghatározása

Az erőforrás-irányítási korlátok az Application manifest (ServiceManifestImport) szakaszban vannak megadva, az alábbi példában látható módon:

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

Ebben a példában a **ServicePackageA** nevű szervizcsomag egy mag-t kap a csomópontokon, ahol elhelyezték. Ez a szolgáltatáscsomag két kódot tartalmaz (**CodeA1** és **CodeA2**), és mindkét `CpuShares` paramétert megadja. A CpuShares 512:256 aránya a két kód csomagjai között osztja el a magját.

Így ebben a példában a CodeA1 egy mag kétharmadát kapja meg, és a CodeA2 egy-harmada bekerül a mag (és az azonos) Ha a CpuShares nincsenek megadva a kódokhoz, Service Fabric egyenlően osztja el a magokat egymás között.

A memória korlátai abszolútak, ezért a csomagok 1024 MB memóriára korlátozódnak (és az azonos szintű, nem garantált foglalás). A kódok (tárolók vagy folyamatok) nem foglalhatnak le több memóriát ennél a korlátnál, és a kísérlet során a memóriában lévő kivételek is megtalálhatók. Az erőforráskorlát érvényesítéséhez a szolgáltatáscsomagokban lévő minden kódcsomaghoz memóriakorlátokat kell meghatároznia.

### <a name="using-application-parameters"></a>Alkalmazás paramétereinek használata

Az erőforrás-irányítási beállítások megadásakor az [alkalmazás paramétereinek](service-fabric-manage-multiple-environment-app-configuration.md) használatával több alkalmazás-konfiguráció is kezelhető. Az alábbi példa az alkalmazás paramétereinek használatát mutatja be:

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

Ebben a példában az alapértelmezett paraméterérték az éles környezethez van beállítva, ahol az egyes szervizcsomagok 4 magot és 2 GB memóriát kapnak. Az alapértelmezett értékek módosíthatók az Application paraméter fájljaival. Ebben a példában egy paraméteres fájl használható az alkalmazás helyi tesztelésére, ha kevesebb erőforrást fog kapni, mint az éles környezetben:

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
> Az erőforrás-szabályozás és az alkalmazás paramétereinek megadása Service Fabric 6,1-es verziótól kezdődően érhető el.<br>
>
> Ha az alkalmazás paramétereinek használatával határozza meg az erőforrás-szabályozást, Service Fabric nem lehet visszaminősíteni az 6,1-es verzió előtti verzióra.

## <a name="enforcing-the-resource-limits-for-user-services"></a>A felhasználói szolgáltatások erőforrás-korlátainak érvényesítése

Az erőforrás-szabályozásnak a Service Fabric-szolgáltatásokra való alkalmazása során garantálható, hogy az erőforrás-szabályozott szolgáltatások nem haladhatják meg az erőforrás-kvótát, sok felhasználónak továbbra is le kell futtatnia a Service Fabric szolgáltatásait a nem szabályozott módban. A nem szabályozott Service Fabric szolgáltatások használatakor lehetséges, hogy olyan helyzetekben is futtathatók, ahol a "szökevény" nem szabályozott szolgáltatások a Service Fabric-csomópontokon elérhető összes erőforrást használják, ami a következő súlyos problémákhoz vezethet:

* A csomópontokon futó egyéb szolgáltatások (köztük a Service Fabric rendszerszolgáltatások) erőforrásainak éhezése
* Sérült állapotban végződő csomópontok
* Nem válaszol Service Fabric fürt felügyeleti API-jai

Az ilyen helyzetek elkerülése érdekében Service Fabric lehetővé teszi, hogy a *csomóponton futó összes Service Fabric felhasználói szolgáltatáshoz érvényesítse az erőforrás-korlátozásokat* (mind a szabályozott, mind a nem szabályozott), hogy a felhasználói szolgáltatások soha ne használják a megadott mennyiségű erőforrást. Ezt úgy érheti el, ha a ClusterManifest PlacementAndLoadBalancing szakaszában a EnforceUserServiceMetricCapacities konfiguráció értékét True értékre állítja. Ez a beállítás alapértelmezés szerint ki van kapcsolva.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

További megjegyzések:

* Az erőforrás-korlátozás kényszerítése csak a `servicefabric:/_CpuCores` és az `servicefabric:/_MemoryInMB` erőforrás-metrikára vonatkozik
* Az erőforrás-korlátozás kényszerítése csak akkor működik, ha az erőforrás-metrikák csomópont-kapacitása Service Fabric elérhető az automatikus észlelési mechanizmuson keresztül, vagy a felhasználók manuálisan, a csomópontok kapacitásának megadásával (ahogy azt az [erőforrás-irányítás engedélyezése](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) című szakasz ismerteti).Ha a csomópont kapacitása nincs konfigurálva, az erőforrás-korlát kényszerítési funkciója nem használható, mert Service Fabric nem tudja, hogy mennyi erőforrást kell lefoglalni a felhasználói szolgáltatásokhoz.Ha a "EnforceUserServiceMetricCapacities" érték igaz, de a csomópont kapacitása nincs konfigurálva, a Service Fabric állapot figyelmeztetést ad ki.

## <a name="other-resources-for-containers"></a>A tárolók egyéb erőforrásai

A processzor és a memória mellett más erőforrás-korlátok is megadhatók a tárolók számára. Ezek a korlátok a kód csomag szintjén vannak megadva, és a rendszer a tároló indításakor alkalmazza őket. A CPU-val és a memóriával ellentétben a fürterőforrás-kezelő nem ismeri ezeket az erőforrásokat, és nem hajtja végre a kapacitás-ellenőrzéseket és a terheléselosztást.

* *MemorySwapInMB*: a tároló által használható swap memória mennyisége.
* *MemoryReservationInMB*: a memória-szabályozáshoz szükséges, csak akkor kényszerített, ha a csomóponton a memória-tartalom észlelhető.
* *CpuPercent*: a tároló által használható CPU százalékaránya. Ha a szolgáltatási csomaghoz CPU-korlátok vannak megadva, a paramétert a rendszer hatékonyan figyelmen kívül hagyja.
* *MaximumIOps*: a tároló által használható maximális IOPS (olvasás és írás).
* *MaximumIOBytesps*: a tároló által használható maximális i/o-érték (bájt/s) (olvasási és írási).
* *BlockIOWeight*: az i/o súlyozásának letiltása a többi tárolóhoz viszonyítva.

Ezek az erőforrások kombinálhatók a PROCESSZORral és a memóriával. Az alábbi példa bemutatja, hogyan határozhat meg további erőforrásokat a tárolók számára:

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

* A fürterőforrás-kezelővel kapcsolatos további információkért olvassa el [a Service Fabric fürterőforrás-kezelő bemutatása](service-fabric-cluster-resource-manager-introduction.md)című témakört.
* Ha többet szeretne megtudni az alkalmazás modelljéről, a szervizcsomagokról és a kódokról, valamint arról, hogy miként képezhetők le a replikák – olvassa el a [modell alkalmazást Service Fabricban](service-fabric-application-model.md).
