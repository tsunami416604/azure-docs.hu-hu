---
title: Számítási feladatok futtatása költséghatékony, alacsony prioritású virtuális gépeken – Azure Batch | Microsoft Docs
description: Ismerje meg, hogy miként lehet alacsony prioritású virtuális gépeket kiépíteni Azure Batch munkaterhelések díjainak csökkentése érdekében.
services: batch
author: mscurrell
manager: gwallace
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 03/19/2018
ms.author: markscu
ms.custom: seodec18
ms.openlocfilehash: 33d448bc95f4cb12f5a06232cbab168a43d522c1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095201"
---
# <a name="use-low-priority-vms-with-batch"></a>Alacsony prioritású virtuális gépek használata a Batch szolgáltatással

A Azure Batch alacsony prioritású virtuális gépeket (VM) biztosít a Batch-munkaterhelések díjainak csökkentése érdekében. Az alacsony prioritású virtuális gépek új típusú batch-munkaterheléseket tesznek lehetővé azáltal, hogy nagy mennyiségű számítási teljesítményre van szükség, amely nagyon alacsony költségeket jelent.
 
Az alacsony prioritású virtuális gépek kihasználhatják a felesleges kapacitást az Azure-ban. Ha alacsony prioritású virtuális gépeket ad meg a készletekben, Azure Batch használhatja ezt a többletet, ha elérhető.
 
Az alacsony prioritású virtuális gépek használatának kompromisszuma az, hogy ezek a virtuális gépek nem érhetők el, vagy bármikor előzik, a rendelkezésre álló kapacitástól függően. Emiatt az alacsony prioritású virtuális gépek a legmegfelelőbbek bizonyos típusú munkaterhelésekhez. Használjon alacsony prioritású virtuális gépeket a Batch-és aszinkron feldolgozási munkaterhelésekhez, ahol a feladat befejezési ideje rugalmas, és a munka több virtuális gépen van elosztva.
 
Az alacsony prioritású virtuális gépeket a dedikált virtuális gépekhez képest jelentősen csökkentett áron kínáljuk. A díjszabás részleteiért lásd: [Batch-díjszabás](https://azure.microsoft.com/pricing/details/batch/).

## <a name="use-cases-for-low-priority-vms"></a>Alacsony prioritású virtuális gépekre vonatkozó esetek használata

Az alacsony prioritású virtuális gépek jellemzői miatt milyen számítási feladatok használhatók és nem használhatók? Általánosságban elmondható, hogy a Batch-feldolgozási munkaterhelések jól illeszkednek, mivel a feladatok számos párhuzamos feladatra vannak bontva, vagy sok olyan feladat van, amely több virtuális gépre kiterjedő és elosztott.

-   Az Azure-ban a felesleges kapacitások maximalizálása érdekében a megfelelő feladatok felskálázásra képesek.

-   Előfordulhat, hogy esetenként a virtuális gépek nem állnak rendelkezésre vagy előzik, ami csökkenti a feladatok mennyiségét, és a feladatok megszakítását és ismétlését eredményezheti. A feladatoknak ezért rugalmasnak kell lenniük a futtatásához szükséges idő alatt.

-   A hosszabb feladatokkal rendelkező feladatok továbbra is befolyásolhatják a megszakítást. Ha a hosszan futó feladatok végrehajtják az ellenőrzőpontokat a végrehajtásuk folyamatának elvégzéséhez, akkor a megszakítás hatása csökken. A rövidebb végrehajtási idővel rendelkező feladatok általában alacsony prioritású virtuális gépekkel működnek legjobban, mivel a megszakítás hatása jóval kevesebb.

-   A több virtuális gépet használó, hosszú ideig futó MPI-feladatok nem alkalmasak az alacsony prioritású virtuális gépek használatára, mivel az egyik előzik virtuális gép a teljes feladat futtatásához vezethet.

Néhány példa a Batch-feldolgozási használati esetekre, amelyek alacsony prioritású virtuális gépek használatára alkalmasak:

-   **Fejlesztés és tesztelés**: Különösen, ha nagy léptékű megoldások vannak kifejlesztve, jelentős megtakarítás valósítható meg. Az összes típusú tesztelés kihasználható, de a nagy léptékű terheléses tesztelés és a regressziós tesztelés nagyszerűen használható.

-   **Igény szerinti kapacitás kiegészítése**: Az alacsony prioritású virtuális gépeket a normál dedikált virtuális gépek kiegészítéseként lehet használni – ha elérhetők, a feladatok méretezhetők, ezért az alacsonyabb költségeket is meggyorsítják; Ha nem érhető el, a dedikált virtuális gépek alapterve továbbra is elérhető marad.

-   **Rugalmas feladatok végrehajtási ideje**: Ha az időbeli feladatokban rugalmasságra van lehetőség, akkor a kapacitás potenciális cseppei tolerálva lehetnek; Ugyanakkor az alacsony prioritású virtuális gépekkel kapcsolatos feladatok gyakran gyorsabban futnak, és alacsonyabb költségeket jelentenek.

A Batch-készletek konfigurálható úgy, hogy az alacsony prioritású virtuális gépeket néhány módon, a feladat végrehajtási idejének rugalmassága alapján konfigurálja.

-   Az alacsony prioritású virtuális gépeket kizárólag készletben lehet használni. Ebben az esetben a Batch minden előzik kapacitást helyreállít, ha elérhető. Ez a konfiguráció a feladatok végrehajtásának legolcsóbb módja, mivel csak alacsony prioritású virtuális gépek használata szükséges.

-   Az alacsony prioritású virtuális gépeket a dedikált virtuális gépek rögzített alaptervével együtt lehet használni. A dedikált virtuális gépek rögzített száma biztosítja, hogy mindig legyen elegendő kapacitás a feladatok végrehajtásához.

-   A dedikált és alacsony prioritású virtuális gépek dinamikus kombinációja lehet, így az alacsony prioritású virtuális gépeket csak akkor használják, ha elérhetők, de a teljes árú dedikált virtuális gépek igény szerint méretezhetők. Ez a konfiguráció a feladatok előrehaladásának megtartása érdekében a lehető legkevesebb kapacitást tartja elérhetővé.

## <a name="batch-support-for-low-priority-vms"></a>Batch-támogatás alacsony prioritású virtuális gépekhez

A Azure Batch számos olyan képességet biztosít, amely megkönnyíti az alacsony prioritású virtuális gépek használatát és előnyeit:

-   A Batch-készletek a dedikált virtuális gépeket és az alacsony prioritású virtuális gépeket is tartalmazhatják. Az egyes virtuálisgép-típusok száma megadható a készlet létrehozásakor, illetve egy meglévő készlet esetében, az explicit átméretezési művelet vagy az automatikus méretezés használatával. A feladat és a feladat elküldése változatlan marad, a készletben lévő virtuálisgép-típusoktól függetlenül. A készletet úgy is konfigurálhatja, hogy az alacsony prioritású virtuális gépeket a lehető legolcsóbban fusson, de a dedikált virtuális gépeket akkor is elindítsa, ha a kapacitás a minimális küszöbérték alá esik, így a feladatok folyamatosan futnak.

-   A Batch-készletek automatikusan megkeresik az alacsony prioritású virtuális gépek céljának számát. Ha a virtuális gépek előzik, a Batch megpróbálja lecserélni az elveszett kapacitást, és visszatérni a célhoz.

-   A feladatok megszakításakor a Batch észleli és automatikusan újravárólistába helyezi a feladatokat.

-   Az alacsony prioritású virtuális gépek különálló vCPU-kvótával rendelkeznek, amely eltér a dedikált virtuális gépektől. 
    Az alacsony prioritású virtuális gépekre vonatkozó kvóta magasabb a dedikált virtuális gépekre vonatkozó kvótánál, mert az alacsony prioritású virtuális gépek olcsóbbak. További információ: [Batch szolgáltatás kvótái és korlátai](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Az alacsony prioritású virtuális gépek jelenleg nem támogatottak a [felhasználói előfizetési módban](batch-api-basics.md#account)létrehozott batch-fiókok esetében.
>

## <a name="create-and-update-pools"></a>Készletek létrehozása és frissítése

A Batch-készletek A dedikált és alacsony prioritású virtuális gépeket (más néven számítási csomópontokat) is tartalmazhatják. Megadhatja a számítási csomópontok megcélzott számát a dedikált és alacsony prioritású virtuális gépek esetében is. A csomópontok célként megadott száma határozza meg a készletben használni kívánt virtuális gépek számát.

Ha például az Azure Cloud Service virtuális gépeket használó készletet 5 dedikált virtuális gép és 20 alacsony prioritású virtuális gép céljával szeretné létrehozni:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Készlet létrehozása az Azure Virtual Machines használatával (ebben az esetben a Linux virtuális gépeken) 5 dedikált virtuális gép és 20 alacsony prioritású virtuális gép céljával:

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

A dedikált és alacsony prioritású virtuális gépekhez tartozó csomópontok aktuális számát lekérheti:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

A készlet csomópontjai rendelkeznek egy tulajdonsággal, amely jelzi, hogy a csomópont dedikált vagy alacsony prioritású virtuális gép-e:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Ha a készlet egy vagy több csomópontja előzik, akkor a készletben lévő lista csomópontjainak művelete továbbra is visszaadja ezeket a csomópontokat. Az alacsony prioritású csomópontok jelenlegi száma változatlan marad, de ezek a csomópontok állapota a **előzik** állapotra van állítva. A Batch megkísérli a helyettesítő virtuális gépek megtalálását, és ha a művelet sikeres, a csomópontok az új csomópontokhoz hasonlóan a feladat-végrehajtáshoz való elérhetővé válás előtt megkezdik az állapotok **létrehozását** és **indítását** .

## <a name="scale-a-pool-containing-low-priority-vms"></a>Alacsony prioritású virtuális gépeket tartalmazó készlet méretezése

Csakúgy, mint a kizárólag dedikált virtuális gépekből álló készletek esetében, az átméretezési módszer meghívásával vagy az automatikus skálázás használatával lehetséges az alacsony prioritású virtuális gépeket tartalmazó készlet méretezése.

A készlet átméretezési művelete egy második opcionális paramétert is végrehajt, amely frissíti a **targetLowPriorityNodes**értékét:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

A készlet autoskálázási képlete az alacsony prioritású virtuális gépeket az alábbiak szerint támogatja:

-   Lekérheti vagy beállíthatja a szolgáltatás által definiált változó értékét **$TargetLowPriorityNodes**.

-   Lekérheti a szolgáltatás által definiált változó értékét **$CurrentLowPriorityNodes**.

-   Lekérheti a szolgáltatás által definiált változó értékét **$PreemptedNodeCount**. 
    Ez a változó visszaadja a csomópontok számát a előzik állapotban, és lehetővé teszi a dedikált csomópontok számának vertikális fel-vagy leskálázását a nem elérhető előzik-csomópontok számától függően.

## <a name="jobs-and-tasks"></a>Feladatok és feladatok

A feladatokhoz és a feladatokhoz kevés további konfiguráció szükséges az alacsony prioritású csomópontok számára; az egyetlen támogatás a következő:

-   A feladatokhoz tartozó JobManagerTask tulajdonság egy új tulajdonsággal rendelkezik, a **AllowLowPriorityNode**. 
    Ha a tulajdonság értéke igaz, a Feladatkezelő feladat ütemezhető egy dedikált vagy alacsony prioritású csomóponton is. Ha ez a tulajdonság hamis, a Feladatkezelő feladat csak dedikált csomópontra van ütemezve.

-   Egy [környezeti változó](batch-compute-node-environment-variables.md) elérhető egy adott alkalmazásban, így megállapítható, hogy alacsony prioritású vagy dedikált csomóponton fut-e. A környezeti változó a AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Megelőlegezése-kezelő

Előfordulhat, hogy a virtuális gépek esetenként előzik; Ha a megelőlegezése történik, a Batch a következő műveleteket végzi el:

-   A előzik virtuális gépek állapota frissítve a **előzik**.
-   Ha a feladatok a előzik csomópont virtuális gépeken futnak, akkor ezek a feladatok újra várólistára kerülnek, és újra futnak.
-   A virtuális gép gyakorlatilag törölve lett, ami a virtuális gépen helyileg tárolt összes adat elvesztését eredményezi.
-   A készlet folyamatosan megkísérli elérni a rendelkezésre álló alacsony prioritású csomópontok számát. Ha a rendszer kicseréli a kapacitást, a csomópontok megőrzik az azonosítókat, de újrainicializálják őket, és az állapotok **létrehozása** és **elindítása** előtt a feladatütemezés számára elérhetővé válnak.
-   A megelőlegezése a Azure Portal metrikaként érhetők el.

## <a name="metrics"></a>Mérőszámok

Az alacsony prioritású csomópontok [Azure Portal](https://portal.azure.com) az új metrikák érhetők el. Ezek a metrikák a következők:

- Alacsony prioritású csomópontok száma
- Alacsony prioritású mag száma 
- Előzik-csomópontok száma

A Azure Portal metrikáinak megtekintése:

1. Navigáljon a Batch-fiókjához a portálon, és tekintse meg a Batch-fiók beállításait.
2. A **figyelés** szakaszban válassza a **metrikák** lehetőséget.
3. Válassza ki a kívánt mérőszámokat az **elérhető metrikák** listájából.

![Alacsony prioritású csomópontok metrikái](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>További lépések

* Olvassa el [Az Azure Batch funkcióinak áttekintése](batch-api-basics.md) című témakört, amely hasznos információkkal szolgál a Batch használatára készülőknek. A cikk a Batch szolgáltatás erőforrásainak, például a készleteknek, csomópontoknak, feladatoknak, tevékenységeknek és sok olyan API funkciónak a részletesebb információit tartalmazza, amelyeket a Batch-alkalmazás kiépítésekor használhat.
* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
