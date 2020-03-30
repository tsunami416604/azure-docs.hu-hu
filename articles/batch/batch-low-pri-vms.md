---
title: Számítási feladatok futtatása költséghatékony, alacsony prioritású virtuális gépeken – Azure Batch | Microsoft dokumentumok
description: Ismerje meg, hogyan építhet idv-es virtuális gépeket az Azure Batch-munkaterhelések költségeinek csökkentése érdekében.
services: batch
author: mscurrell
manager: evansma
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 03/19/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9f4b9ed9254eaf950311dd27d5716c4681707614
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053913"
---
# <a name="use-low-priority-vms-with-batch"></a>Alacsony prioritású virtuális gépek használata a Batch szolgáltatással

Az Azure Batch alacsony prioritású virtuális gépeket (VM-eket) kínál a Batch-számítási feladatok költségeinek csökkentése érdekében. Az alacsony prioritású virtuális gépek lehetővé teszik a batch-munkaterhelések új típusait azáltal, hogy lehetővé teszik a nagy számítási teljesítmény nagyon alacsony költségű használható.
 
Az alacsony prioritású virtuális gépek kihasználják az Azure többletkapacitását. Ha alacsony prioritású virtuális gépeket ad meg a készletekben, az Azure Batch használhatja ezt a többletet, ha rendelkezésre áll.
 
Az alacsony prioritású virtuális gépek használatának kompromisszumos használata az, hogy ezek a virtuális gépek nem lehet lefoglalni, vagy lehet, hogy a rendelkezésre álló kapacitás. Emiatt az alacsony prioritású virtuális gépek a legalkalmasabbak bizonyos típusú számítási feladatokhoz. Alacsony prioritású virtuális gépek használata kötegelt és aszinkron feldolgozási számítási feladatokhoz, ahol a feladat befejezési ideje rugalmas, és a munka számos virtuális gép között oszlik meg.
 
Alacsony prioritású virtuális gépek kínálnak jelentősen csökkentett áron, mint a dedikált virtuális gépek. Az árképzésrészleteiről a [Kötegelt árak oldalon](https://azure.microsoft.com/pricing/details/batch/)talál.

> [!NOTE]
> [A direkt virtuális gépek](https://azure.microsoft.com/pricing/spot/) már elérhetők [az egypéldányos virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/linux/spot-vms) és [a virtuálisgép-méretezési csoportokhoz.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot) A azonnali virtuális gépek az alacsony prioritású virtuális gépek fejlődése, de különböznek, hogy az árképzés változhat, és opcionális maximális ár állítható be a direkt virtuális gépek lefoglalásakor.
>
> Az Azure Batch-készletek néhány hónapon belül megkezdik a direkt virtuális gépek támogatását, és a [Batch API-k és az eszközök](https://docs.microsoft.com/azure/batch/batch-apis-tools)új verzióival kezdik meg a virtuális gépek támogatását. Amint a virtuális gépek támogatása elérhetővé válik, az alacsony prioritású virtuális gépek elavulttá válik – legalább 12 hónapig továbbra is támogatni fogják őket a jelenlegi API-k és az eszközverziók használatával, így elegendő idő marad a virtuális gépekre való áttelepítéshez. 
>
> A virtuális gépek nem támogatottak a [felhőszolgáltatás konfigurációs](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) készletei számára. A direkt virtuális gépek használatához a Felhőszolgáltatás-készleteket át kell telepíteni [a virtuálisgép-konfigurációs](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration) készletekbe.


## <a name="use-cases-for-low-priority-vms"></a>Esetek használata alacsony prioritású virtuális gépekhez

Az alacsony prioritású virtuális gépek jellemzői miatt milyen számítási feladatok használhatják és nem használhatják őket? Általában a kötegelt feldolgozási számítási feladatok megfelelőek, mivel a feladatok számos párhuzamos feladatra vannak bontva, vagy sok olyan feladat van, amelyek horizontálisan ki vannak osztva és elosztva számos virtuális gép között.

-   A többletkapacitás azure-beli maximális kihasználása érdekében a megfelelő feladatok horizontálisan kiszélesedhetnek.

-   Előfordulhat, hogy a virtuális gépek nem érhetők el, vagy nem érhetők el, ami a feladatok csökkentett kapacitását eredményezi, és a feladatok megszakításához és ismétléséhez vezethet. A feladatoknak ezért rugalmasnak kell lenniük a futáshoz.

-   A hosszabb feladatokkal rendelkező feladatokra nagyobb hatással lehet, ha megszakad. Ha a hosszú ideig futó feladatok ellenőrzőpontokat valósítanak meg a folyamat végrehajtásának mentéséhez, akkor a megszakítás hatása csökken. A rövidebb végrehajtási idővel rendelkező feladatok általában az alacsony prioritású virtuális gépeken működnek a legjobban, mivel a megszakítás hatása sokkal kisebb.

-   A több virtuális gépet használó, hosszú ideig futó MPI-feladatok nem alkalmasak az alacsony prioritású virtuális gépek használatára, mivel egy preempted virtuális gép az egész feladat újra futtatásához vezethet.

Néhány példa a kötegelt feldolgozás használati esetekre, amelyek kiválóan alkalmasak az alacsony prioritású virtuális gépek használatára:

-   **Fejlesztés és tesztelés**: Különösen, ha nagyszabású megoldásokat fejlesztenek ki, jelentős megtakarítások at érhetnek el. Minden típusú tesztelés előnyös lehet, de a nagyléptékű terheléstesztelés és regressziós tesztelés nagyszerű felhasználási.

-   **Az igény szerinti kapacitás kiegészítése:** Az alacsony prioritású virtuális gépek rendszeres dedikált virtuális gépek kiegészítésére használhatók – ha rendelkezésre állnak, a feladatok skálázhatók, és így gyorsabban végezhetők el alacsonyabb költségek esetén; ha nem áll rendelkezésre, a dedikált virtuális gépek alapkonfigurációja továbbra is elérhető marad.

-   **Rugalmas feladat-végrehajtási idő:** Ha a feladatok befejezésének idejében rugalmas, akkor a kapacitás potenciális csökkenése tolerálható; azonban az alacsony prioritású virtuális gépek feladatainak hozzáadásával gyakran gyorsabban és alacsonyabb költségek mellett futnak.

A kötegkészletek beállíthatók úgy, hogy a feladat-végrehajtási idő rugalmasságától függően néhány módon alacsony prioritású virtuális gépeket használjanak:

-   Alacsony prioritású virtuális gépek kizárólag egy készletben használható. Ebben az esetben a Batch helyreállítja a preempted kapacitást, ha rendelkezésre áll. Ez a konfiguráció a legolcsóbb módja a feladatok végrehajtásának, mivel csak alacsony prioritású virtuális gépeket használ.

-   Alacsony prioritású virtuális gépek használható együtt egy rögzített alapkonfiguráció dedikált virtuális gépek. A dedikált virtuális gépek rögzített száma biztosítja, hogy mindig van némi kapacitás a feladat előrehaladásának fenntartásához.

-   A dedikált és alacsony prioritású virtuális gépek dinamikus vegyesen érhetők el, így az olcsóbb, alacsony prioritású virtuális gépek et csak akkor használja a gép, ha rendelkezésre állnak, de a teljes árú dedikált virtuális gépek szükség esetén felskálázhatók. Ez a konfiguráció minimális kapacitást biztosít a feladatok előrehaladásának fenntartásához.

## <a name="batch-support-for-low-priority-vms"></a>Kötegtámogatás alacsony prioritású virtuális gépekhez

Az Azure Batch számos olyan képességet kínál, amelyek megkönnyítik az alacsony prioritású virtuális gépek felhasználását és előnyeit:

-   A kötegkészletek dedikált virtuális gépeket és alacsony prioritású virtuális gépeket is tartalmazhatnak. A virtuális gép egyes típusainak száma megadható, amikor egy készletet létrehoznak, vagy egy meglévő készlethez bármikor módosítanak, az explicit átméretezési művelet vagy az automatikus méretezés használatával. A feladat és a feladat beküldése változatlan maradhat, függetlenül a készletben lévő virtuálisgép-típusoktól. Konfigurálhat egy készletet úgy is, hogy teljes mértékben alacsony prioritású virtuális gépeket használjon a feladatok lehető legolcsóbb futtatásához, de a dedikált virtuális gépeket, ha a kapacitás egy minimális küszöbérték alá csökken, hogy a feladatok futnak.

-   A kötegkészletek automatikusan keresik az alacsony prioritású virtuális gépek célszámát. Ha a virtuális gépek preempted, majd batch megpróbálja helyettesíteni az elveszett kapacitást, és visszatér a cél.

-   A feladatok megszakításakor a Batch észleli és automatikusan újravárólistára helyezi a feladatokat, hogy újra fussanak.

-   Alacsony prioritású virtuális gépek egy külön vCPU-kvótát, amely eltér a dedikált virtuális gépek. 
    Az alacsony prioritású virtuális gépek kvótája magasabb, mint a dedikált virtuális gépek kvótája, mivel az alacsony prioritású virtuális gépek olcsóbbak. További információt a [Batch szolgáltatási kvóták és korlátok című](batch-quota-limit.md#resource-quotas)témakörben talál.    

> [!NOTE]
> A [felhasználói előfizetési módban](batch-api-basics.md#account)létrehozott batch fiókok jelenleg nem támogatottak az alacsony prioritású virtuális gépek.
>

## <a name="create-and-update-pools"></a>Készletek létrehozása és frissítése

A batch készlet dedikált és alacsony prioritású virtuális gépeket (más néven számítási csomópontokat is tartalmazhat). Beállíthatja a számítási csomópontok célszámát a dedikált és az alacsony prioritású virtuális gépekhez. A csomópontok célszáma adja meg a készletben a virtuális gépek számát.

Például hozzon létre egy készletet az Azure felhőszolgáltatás virtuális gépei használatával, amelynek célja 5 dedikált virtuális gép és 20 alacsony prioritású virtuális gép:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Készlet létrehozása Azure virtuális gépek (ebben az esetben a Linux virtuális gépek) használatával, amelynek célja 5 dedikált virtuális gépek és 20 alacsony prioritású virtuális gépek:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

A dedikált és az alacsony prioritású virtuális gépek hez a csomópontok aktuális számát is lekaphatja:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

A készletcsomópontok rendelkeznek egy tulajdonsággal, amely jelzi, hogy a csomópont dedikált vagy alacsony prioritású virtuális gép-e:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Ha egy készlet egy vagy több csomópontja elődül, a készletlista-csomópontok művelete továbbra is visszaadja ezeket a csomópontokat. Az alacsony prioritású csomópontok aktuális száma változatlan marad, de ezek a csomópontok állapota **preempted** állapotban van állítva. Batch megpróbálja megtalálni a helyettesítő virtuális gépek, és ha sikeres, a csomópontok megy **keresztül létrehozása,** majd **indítása** állapotok előtt elérhetővé válik a feladat végrehajtására, mint az új csomópontok.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Alacsony prioritású virtuális gépeket tartalmazó készlet méretezése

A kizárólag dedikált virtuális gépekből álló készletekhez hasonlóan az átméretezési módszer vagy az automatikus skálázás használatával alacsony prioritású virtuális gépeket tartalmazó készlet skálázható.

A készlet átméretezési művelet egy második választható paramétert vesz igénybe, amely frissíti a **targetLowPriorityNodes**értékét:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

A készlet automatikus skálázási képlete az alábbiak szerint támogatja az alacsony prioritású virtuális gépeket:

-   Beszerezheti vagy beállíthatja a szolgáltatás által definiált **változó értékét $TargetLowPriorityNodes.**

-   A szolgáltatás által definiált változó értéke **$CurrentLowPriorityNodes.**

-   A szolgáltatás által definiált változó értékét **$PreemptedNodeCount.** 
    Ez a változó a preempted állapotban lévő csomópontok számát adja vissza, és lehetővé teszi a dedikált csomópontok számának fel- vagy leskálázását, a nem elérhető preempted csomópontok számától függően.

## <a name="jobs-and-tasks"></a>Feladatok és feladatok

A feladatok és a feladatok kevés további konfigurációt igényelnek az alacsony prioritású csomópontokhoz; az egyetlen támogatás a következő:

-   Egy feladat JobManagerTask tulajdonsága új tulajdonsággal ( **AllowLowPriorityNode**. 
    Ha ez a tulajdonság igaz, a feladatkezelő feladat ütemezhető dedikált vagy alacsony prioritású csomóponton. Ha ez a tulajdonság hamis, a feladatkezelő feladat csak egy dedikált csomópontra van ütemezve.

-   Egy [környezeti változó](batch-compute-node-environment-variables.md) elérhető egy feladatalkalmazás számára, így meg állapíthatja meg, hogy alacsony prioritású vagy dedikált csomóponton fut-e. A környezeti változó AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Elővásárlás kezelése

A virtuális gépek esetenként kísértésűek lehetnek; amikor elővásárlás történik, a Batch a következőket teszi:

-   A preempted virtuális gépek állapota **preempted**állapotra frissül.
-   Ha a feladatok a preempted csomópont virtuális gépeken futottak, majd ezeket a feladatokat a várólistára helyezi, és újra futtatja.
-   A virtuális gép hatékonyan törlődik, ami a virtuális géphelyileg tárolt adatok elvesztését.
-   A készlet folyamatosan megpróbálja elérni a rendelkezésre álló alacsony prioritású csomópontok célszámát. Ha cserekapacitás található, a csomópontok megtartják az azonosítóikat, de újrainicializálódnak, és végighaladnak **az Állapotok létrehozása** és **indítása** korán, mielőtt azok elérhetők lennének a feladatütemezéshez.
-   A megelőző adatok száma metrikaként érhető el az Azure Portalon.

## <a name="metrics"></a>Mérőszámok

Új metrikák érhetők el az [Azure](https://portal.azure.com) Portalon az alacsony prioritású csomópontok. Ezek a mutatók a következők:

- Alacsony prioritású csomópontszám
- Alacsony prioritású magok száma 
- Preempted csomópontszám

Metrikák megtekintése az Azure Portalon:

1. Nyissa meg a Batch-fiókot a portálon, és tekintse meg a Batch-fiók beállításait.
2. Válassza ki **a metrikák** a **figyelési** szakaszban.
3. Válassza ki a kívánt mérőszámokat az **Elérhető mutatók** listából.

![Metrikák alacsony prioritású csomópontok](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>További lépések

* Olvassa el [Az Azure Batch funkcióinak áttekintése](batch-api-basics.md) című témakört, amely hasznos információkkal szolgál a Batch használatára készülőknek. A cikk a Batch szolgáltatás erőforrásainak, például a készleteknek, csomópontoknak, feladatoknak, tevékenységeknek és sok olyan API funkciónak a részletesebb információit tartalmazza, amelyeket a Batch-alkalmazás kiépítésekor használhat.
* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
* Kezdje meg megtervezni az alacsony prioritású virtuális gépekről a direkt virtuális gépekre való áttérést. Ha alacsony prioritású virtuális gépeket használ **a Cloud Service konfigurációs** készleteivel, majd tervezze meg a **virtuális gép konfigurációs** készleteire való áttérést.
