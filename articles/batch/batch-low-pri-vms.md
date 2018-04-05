---
title: A költséghatékony, alacsony prioritású virtuális gépek Azure Batch feladatokat futtató |} Microsoft Docs
description: Ismerje meg, alacsony prioritású virtuális gépeket az Azure Batch-munkaterhelések költségek csökkentése a kiépítése.
services: batch
author: mscurrell
manager: jeconnoc
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 03/19/2018
ms.author: markscu
ms.openlocfilehash: a05c816bd876f7c66a1e62515dea44c6fd969c8c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="use-low-priority-vms-with-batch"></a>Kis prioritású virtuális gépek használata a kötegelt

Az Azure Batch biztosít alacsony prioritású virtuális gépek (VM) kötegelt munkaterhelések költségeinek csökkentése érdekében. Alacsony prioritású virtuális gépek új munkaterhelések lehetséges, mivel lehetővé teszi nagy mennyiségű számítási teljesítményt, nagyon alacsony költséggel használt kötegelt teszik.
 
Kis prioritású virtuális gépek előnyeit felesleges kapacitás az Azure-ban. Kis prioritású virtuális gépek a készletek megadásakor Azure Batch használhatja az eredmény, ha elérhető.
 
Mi a fontosabb: az alacsony prioritású virtuális gépeket, hogy a virtuális gépek nem állnak rendelkezésre kiosztani, vagy előfordulhat, hogy tetszőleges időpontban, attól függően, hogy a rendelkezésre álló kapacitásból foglalható. Emiatt alacsony prioritású virtuális gépek legmegfelelőbb bizonyos munkaterhelések esetében. Alacsony prioritású virtuális gépek használata kötegelt és aszinkron feldolgozási terheléshez, ahol a feladat befejezési idő rugalmas, és a munka sok virtuális gép között van elosztva.
 
Kis prioritású virtuális gépek dedikált virtuális gépek képest jelentősen csökkentett áron érhető el. Díjszabása, lásd: [kötegelt árképzési](https://azure.microsoft.com/pricing/details/batch/).

## <a name="use-cases-for-low-priority-vms"></a>Kis prioritású virtuális gépek alkalmazási helyzetei

Alacsony prioritású virtuális gépeket, mi munkaterhelések is, és nem használja őket figyelembe véve? Általában kötegelt feldolgozási terheléshez amelyeket remekül beválik, feladatok számos párhuzamos feladat van felosztva, vagy sok feladat horizontálisan és sok virtuális gép pontjain.

-   Használja az Azure-ban felesleges kapacitás maximalizálása érdekében megfelelő feladatok lehet terjeszteni.

-   Alkalmanként virtuális gépek nem érhető el vagy leállított, ennek eredményeképpen a feladatok korlátozott kapacitás és a feladat megszakítása és ismétlések vezethet. Feladatok ezért rugalmas a futtatásához érvénybe belül kell lennie.

-   Feladatok hosszabb ideig feladatok több érintheti, ha megszakad. Ha hosszú ideig futó feladatok végrehajtása ellenőrzőpontok mentése folyamatban van, akkor hajtható végre, a megszakítás hatását csökken. Rövidebb végrehajtási idővel rendelkező tevékenységek általában a legmegfelelőbb alacsony prioritású virtuális gépeket, mert a megszakítás hatását sokkal kevesebb.

-   Hosszan futó MPI-feladatok, amelyek ténylegesen használják több virtuális gép nem nincsenek kiválóan alkalmas a alacsony prioritású virtuális gép használja, mert egy leállított virtuális gép vezethet a teljes feladat újrafuttatása kellene.

Néhány példa a kötegelt feldolgozásra használati esetek jól, alacsony prioritású virtuális gépek használatára alkalmas a következők:

-   **Fejlesztéshez és teszteléshez**: különösen akkor, ha a felügyeleti teendők központjaként megoldások fejlesztenek, jelentős megtakarítást is kell megvalósítani. Tesztelési minden típusú kihasználhassa, de nagy terhelés tesztelése és regressziós tesztelés kiváló használ.

-   **Igény szerinti kapacitás kiegészítése**: alacsony prioritású virtuális gépek regular dedikált virtuális gépek - kiegészítésére használható, ha elérhető, a feladatok méretezése, és ezért alacsonyabb költségek gyorsabb befejezéséhez; nem érhető el, ha az alapkonfiguráció dedikált virtuális gépek továbbra is elérhető .

-   **Rugalmas feladat végrehajtási ideje**: esetén az idő-feladatok rugalmasságot kell végrehajtania, akkor lehetséges elhagyta a kapacitás is elfogadhatók, azonban alacsony prioritású virtuális gépek hozzáadásával feladatok gyakran futtatását gyorsabb és az alacsonyabb költségek.

Kötegelt készletek beállítható úgy, hogy számos módja, attól függően, hogy a feladat végrehajtási ideje rugalmasságot alacsony prioritású virtuális gépek használata:

-   Kis prioritású virtuális gépek kizárólag a készletben használható. Kötegelt ebben az esetben, ha elérhető bármely preempted kapacitás állítja helyre. Ez a konfiguráció módja a legolcsóbb feladatok végrehajtásához csak alacsony prioritású virtuális gép használja.

-   Kis prioritású virtuális gépek dedikált virtuális gépek rögzített alapterv együtt használható. A dedikált virtuális gépek rögzített száma biztosítja, hogy mindig van néhány kapacitás tartani a feladat halad.

-   Lehet dedikált és alacsony prioritású virtuális gépeket, a dinamikus kombinációját, hogy az olcsóbb alacsony prioritású virtuális gépek kizárólag akkor használatosak, ha elérhető, de a teljes árú dedikált virtuális gépek méretezhető, szükség esetén. Ez a konfiguráció tartja a minimális kapacitás elérhető, a feladatok halad.

## <a name="batch-support-for-low-priority-vms"></a>Kis prioritású virtuális gépek kötegelt támogatása

Az Azure Batch szolgáltatás különböző képességeket, amelyek megkönnyítik az használnak, és igénybe vehesse az alacsony prioritású virtuális gépek:

-   Kötegelt készletek dedikált virtuális gépek és az alacsonyabb prioritású virtuális gépek is tartalmazhat. A virtuális gép típusonkénti számát a készlet létrehozásakor, vagy megváltozott a meglévő készletek, az explicit átméretezés vagy automatikus skálázása használatával bármikor adható meg. Feladat- és küldésének változatlan marad, függetlenül a tárolókészlet a Virtuálisgép-típusokon maradjanak. Beállíthatja úgy is egy alkalmazáskészlet a teljesen alacsony prioritású virtuális gépek is, de dedikált virtuális gépeinek léptetéses olcsón futtathat feladatokat, ha a kapacitás, futó feladatok tartani a minimális küszöbérték alá csökken.

-   Kötegelt készletek automatikusan keresni a cél alacsony prioritású virtuális gépek számát. Ha a leállított virtuális gépeken, kötegelt megpróbálja cserélje ki az elveszett kapacitás, és térjen vissza a cél.

-   Feladatok megszakadnak, amikor a kötegelt észleli, és futtassa újra a feladatok automatikusan requeues.

-   Kis prioritású virtuális gépek rendelkezik, amely eltér a dedikált virtuális gépek egy külön vCPU kvóta. 
    Alacsony prioritású virtuális gép esetében a kvóta nem nagyobb, mint dedikált virtuális gépek, a kvóta, mert alacsony prioritású virtuális gépek költséget. További információkért lásd: [a Batch szolgáltatás kvótái és korlátai](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Alacsony prioritású virtuális gép jelenleg nem támogatottak a Batch-fiókok létrehozása [felhasználói előfizetési mód](batch-api-basics.md#account).
>

## <a name="create-and-update-pools"></a>Hozzon létre, és a készletek frissítése

A Batch-készlet dedikált és alacsony prioritású virtuális gép (más néven a a számítási csomópontokkal) tartalmazhat. Beállíthatja a számítási csomópontok száma cél dedikált és alacsony prioritású virtuális gép esetében. A tároló csomópontok száma azt a van a tárolókészletben kívánt virtuális gépeket.

Például készlet létrehozása a virtuális gépek Azure-felhőszolgáltatásban egy target 5 dedikált virtuális gépek és 20 alacsony prioritású virtuális gép:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4") // WS 2012 R2
);
```

Készlet létrehozása az Azure virtuális gépek (ebben az esetben Linux virtuális gépek) 5 cél dedikált használó virtuális gépek és 20 alacsony prioritású virtuális gép:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04.0-LTS",
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

Dedikált és alacsony prioritású virtuális gépek kaphat a csomópontok száma:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Készlet csomópontjain van-e a tulajdonság azt jelzi, ha a csomópont egy alacsony prioritású vagy dedikált virtuális Gépet:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Amikor egy alkalmazáskészlet egy vagy több csomópontja részesítés, egy lista csomópontok a készlet továbbra is visszaadja azokat a csomópontokat. Alacsony prioritású csomópontok száma változatlan marad, de azokat a csomópontokat, hogy az állapot értéke a **Prioritás miatt kihagyva** állapotát. Kötegelt megpróbálja megkeresni a csere virtuális gépeket, és ha sikeres, a csomópontok halad át **létrehozása** , majd **indítása** állapotok ahhoz, hogy elérhető-e a feladat végrehajtásához, ugyanúgy, mint az új csomópontok.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Kis prioritású virtuális gépeket tartalmazó készlet méretezése

Mint készletek kizárólag álló dedikált virtuális gépek, lehetősége egy alacsony prioritású virtuális gépeket tartalmazó az átméretezési metódus hívása vagy automatikus skálázási készlet méretezése.

Az alkalmazáskészlet átméretezés egy második választható-paramétert fogad, amely frissíti a **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

A készlet automatikus skálázás képlet az alábbiak szerint támogatja az alacsony prioritású virtuális gépek:

-   Futtasson, vagy a szolgáltatás által definiált változó értékét állíthatja be **$TargetLowPriorityNodes**.

-   A szolgáltatás által definiált változó értékének kaphat **$CurrentLowPriorityNodes**.

-   A szolgáltatás által definiált változó értékének kaphat **$PreemptedNodeCount**. 
    Ez a változó preempted állapotban csomópontok számát adja vissza, és lehetővé teszi a méretezési felfelé vagy lefelé a dedikált csomópontok, attól függően, hogy nem használható a leállított csomópontok száma száma.

## <a name="jobs-and-tasks"></a>Feladatok és feladatok

Feladatok és a feladatok kevés további beállításokra van szükség az alacsony prioritású csomópont; a csak támogatása a következőképpen történik:

-   Egy feladat JobManagerTask tulajdonságnak egy új tulajdonság **AllowLowPriorityNode**. 
    Ez a tulajdonság értéke igaz, ha a kezelő feladat egy dedikált vagy alacsony prioritású csomópont is ütemezhető. Ez a tulajdonság értéke HAMIS, ha a feladat manager feladat csak a kijelölt csomópont ütemezett.

-   Egy [környezeti változó](batch-compute-node-environment-variables.md) érhető el egy feladat alkalmazást, hogy megállapíthassa, hogy egy alacsony prioritású vagy dedikált csomóponton futó. A környezeti változó AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>A megelőlegezés kezelése

Virtuális gépek alkalmanként foglalható; a megelőlegezés esetben kötegelt hajtja végre a következő:

-   A preempted virtuális gépek rendelkezik a frissített állapotukra **Prioritás miatt kihagyva**.
-   Ha a csomópont a leállított virtuális gépeken futó feladatokat, majd ezeket a feladatokat helyezte és futtassa újból.
-   A virtuális gép hatékonyan törölték, ami a virtuális Gépre helyileg tárolt adatok elvesztését.
-   A készlet folyamatosan kísérletet tesz az alacsony prioritású csomópontok elérhető cél számát. Csere kapacitás található, a csomópontok azonosítók tartani, de újrainicializálni, keresztül haladó **létrehozása** és **indítása** előtt a feladat ütemezés szerint.
-   A megelőlegezés számát, az Azure portálon metrika érhetők el.

## <a name="metrics"></a>Mérőszámok

Új mérőszámok érhetők el a [Azure-portálon](https://portal.azure.com) az alacsony prioritású csomópont. Ezen adatok gyűjtése le van:

- Alacsony prioritású csomópontok száma
- Alacsony prioritású magok száma 
- Csomópont Count részesítés

Metrikák megtekintése az Azure-portálon:

1. Keresse meg a Batch-fiók a portálon, és a Batch-fiók beállításainak megtekintéséhez.
2. Válassza ki **metrikák** a a **figyelés** szakasz.
3. Válassza ki a vár a metrikák a **elérhető** listája.

![Alacsony prioritású csomópontok metrikák](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>További lépések

* Olvassa el [Az Azure Batch funkcióinak áttekintése](batch-api-basics.md) című témakört, amely hasznos információkkal szolgál a Batch használatára készülőknek. A cikk a Batch szolgáltatás erőforrásainak, például a készleteknek, csomópontoknak, feladatoknak, tevékenységeknek és sok olyan API funkciónak a részletesebb információit tartalmazza, amelyeket a Batch-alkalmazás kiépítésekor használhat.
* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
