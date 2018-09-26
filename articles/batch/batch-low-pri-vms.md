---
title: Költséghatékony, kis prioritású virtuális gépek az Azure Batch számítási feladatok futtatása |} A Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe az alacsony prioritású virtuális gépeket az Azure Batch számítási feladatait a költségek csökkentése érdekében.
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
ms.openlocfilehash: d42cef944c3b971804ef1417a3877bf919784a02
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47093003"
---
# <a name="use-low-priority-vms-with-batch"></a>A Batch alacsony prioritású virtuális gépek használata

Az Azure Batch alacsony prioritású virtuális gépek (VM) a Batch számítási feladatok költségek csökkentése érdekében kínál. Alacsony prioritású virtuális gépek győződjön meg arról, a Batch számítási feladatok lehetséges, hogy lehetővé teszi nagy mennyiségű számítási teljesítményt használandó nagyon alacsony költség új típusú.
 
Alacsony prioritású virtuális gépek az Azure többletkapacitását előnyeinek kihasználása. Alacsony prioritású virtuális gépek a készletekben található ad meg, az Azure Batch segítségével a többletet, ha elérhető.
 
Alacsony prioritású virtuális gépek használatával ára azonban, hogy azokon a virtuális gépeken nem érhető el, amelyet ki kell, vagy bármikor rendelkezésre álló kapacitás függően előfordulhat, hogy háttérbe. Ebből kifolyólag alacsony prioritású virtuális gépek kiválóan alkalmasak legtöbb bizonyos típusú számítási feladatokat. Alacsony prioritású virtuális gépek használata a batch- és az aszinkron feldolgozás számítási feladatokhoz, ahol a feladat végrehajtási ideje rugalmas, és a munka legyen elosztva a több virtuális gépet.
 
Alacsony prioritású virtuális gépek dedikált virtuális gépek képest jelentősen kedvezményes áron érhető el. A díjszabás részleteiért lásd: [Batch díjszabása](https://azure.microsoft.com/pricing/details/batch/).

## <a name="use-cases-for-low-priority-vms"></a>Alacsony prioritású virtuális gépek alkalmazási helyzetei

Figyelembe véve az alacsony prioritású virtuális gépeket, milyen számítási feladatokat is, és nem használja őket? Általánosságban elmondható kötegelt feldolgozási számítási feladatok áll a jó megoldás, feladatok párhuzamos feladatok vannak osztva, vagy számos feladat, horizontálisan felskálázott és elosztott több virtuális gépen.

-   Használja az Azure többletkapacitását maximalizálása horizontálisan megfelelő feladatokat.

-   Alkalmanként virtuális gépek nem érhető el vagy leállított, aminek eredményeképpen feladatok korlátozott kapacitás és a feladat megszakítása és ismétlések vezethet. Feladatok ezért kell azok futtatása eltarthat az idő rugalmas.

-   Feladatok a hosszabb feladatok több érintheti, ha megszakad. Ha hosszú ideig futó feladatok megvalósítása mentése folyamatban van, akkor hajtsa végre az ellenőrzőpontok használata, majd megszakadásának hatását csökken. Rövidebb a végrehajtási időpontok a feladatok általában működnek a legjobban az alacsony prioritású virtuális gépeket, mert megszakadásának hatását jóval kevesebb.

-   Mivel egy leállított virtuális gép nem kell újra futtatni a teljes feladat vezethet, amelyek hosszú ideig futó MPI-feladatok, amelyek ténylegesen használják több virtuális gép nem jól használható alacsony prioritású virtuális gépek.

Néhány példa a kötegelt feldolgozási használati esetek jól, alacsony prioritású virtuális gépek használatára alkalmas a következők:

-   **Fejlesztési és tesztelési**: különösen, ha nagy méretű megoldásokat fejlesztenek, jelentős megtakarítást megvalósíthatók. Minden típusú tesztelés előnyeit, de nagy méretű terheléses tesztelés és regressziós tesztelési nagyszerű használja.

-   **Igény szerinti kapacitás kiegészítése**: alacsony prioritású virtuális gépek segítségével kiegészítik regular dedikált virtuális gépek – Ha elérhető, a feladatok méretezhetők, és így gyorsabb befejezéséhez alacsonyabb költségek mellett; nem érhető el, ha az alapkonfiguráció dedikált virtuális gépek továbbra is elérhető marad .

-   **Rugalmas feladat-végrehajtási idő**: Ha nincs ideje feladatok rugalmasságot kell végrehajtania, majd a kapacitás lehetséges csepp tolerálható; azonban alacsony prioritású virtuális gépek igény szerinti hozzáadásával feladatok gyakran futtatása gyorsabban és alacsony költségek mellett.

Batch-készletek konfigurálhatók, alacsony prioritású virtuális gépek használata több módon is, attól függően, a rugalmasságot, a feladat-végrehajtási idő:

-   Alacsony prioritású virtuális gépek kizárólag a készletben használható. Ebben az esetben kötegelt helyreállítja valamelyik preempted kapacitáshoz, ha elérhető. Ez a konfiguráció módja a legolcsóbb futtathatnak feladatokat, csak az alacsony prioritású virtuális gépeket használ.

-   Alacsony prioritású virtuális gépek dedikált virtuális gépek rögzített alapterv együtt használható. A rögzített dedikált virtuális gépek száma biztosítja, hogy mindig van valamilyen formában, hogy a feladat halad előre.

-   Lehet dedikált és alacsony prioritású virtuális gépek, a dinamikus arányát, hogy az olcsóbb alacsony prioritású virtuális gépek kizárólag használni, ha elérhető, de a teljes árú dedikált virtuális gépek skálázható fel, ha szükséges. Ez a konfiguráció biztosítja, hogy minimális kapacitás elérhető, a feladatok halad előre.

## <a name="batch-support-for-low-priority-vms"></a>Alacsony prioritású virtuális gépek Batch támogatása

Azure Batch lehetőséget biztosít a különböző képességeket, amelyek megkönnyítik a és a alacsony prioritású virtuális gépeket felhasználására:

-   Batch-készletekben dedikált virtuális gépek és az alacsony prioritású virtuális gépek is tartalmazhat. A virtuális gép különböző típusú száma adható meg a készlet létrehozásakor, vagy egy meglévő készlet, az explicit átméretezés vagy automatikus skálázás használatával bármikor módosítható. Feladatok és tevékenységek küldésének változatlan marad, függetlenül a virtuális gépek típusai a készletben marad. Beállíthatja, hogy a készlet teljes mértékben az alacsony prioritású virtuális gépek használatával, de dedikált virtuális gépek elindítása olcsón futtathat feladatokat, ha a kapacitás csökkenése miatt növekedhet, hogy a futó feladatokat, minimális küszöbérték alá.

-   Batch-készletek automatikusan az alacsony prioritású virtuális gépek célszáma keresik. Ha a leállított virtuális gépeket, majd a Batch megkísérli a kimaradt kapacitást, és térjen vissza a cél.

-   Feladatok megszakadnak, amikor a Batch észleli, és futtassa újra a feladatok automatikusan requeues.

-   Alacsony prioritású virtuális gépek rendelkeznek egy külön vCPU-kvóta, amely eltér a dedikált virtuális gépek esetében. 
    Alacsony prioritású virtuális gépekre vonatkozó kvóta azért magasabb, mint a kvóta a dedikált virtuális gépek esetében, mert alacsony prioritású virtuális gépek kevesebb költséggel jár. További információkért lásd: [Batch szolgáltatás kvótái és korlátai](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Alacsony prioritású virtuális gépek jelenleg nem támogatottak a Batch-fiókokkal létrehozott [felhasználói előfizetési módban](batch-api-basics.md#account).
>

## <a name="create-and-update-pools"></a>Hozzon létre, és a készletek frissítése

Batch-készlet dedikált és alacsony prioritású virtuális gépek (is hivatkoznak, a számítási csomópontok) is tartalmazhat. Beállíthatja a számítási csomópontok célszámát a dedikált vagy alacsony prioritású virtuális gépeket. Csomópontok célszáma tartozik a készlethez kívánt virtuális gépek számát adja meg.

Például hoz létre egy készletet használó virtuális gépek Azure-felhőszolgáltatásban 5. cél dedikált virtuális gépek és a 20 alacsony prioritású virtuális gépek:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Készlet létrehozása (ebben az esetben Linux rendszerű virtuális gépek) az Azure virtual machines használatával, 5. cél dedikált virtuális gépek és a 20 alacsony prioritású virtuális gépek:

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

Dedikált vagy alacsony prioritású virtuális gépeket is igénybe csomópontok jelenlegi száma:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Készlet csomópontjain a tulajdonság azt jelzi, ha a csomópont egy dedikált vagy alacsony prioritású virtuális gép rendelkezik:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

A leállított egy vagy több csomópont a készletben, egy lista csomópontok a készlet műveletet továbbra is visszaadja azokat a csomópontokat. Alacsony prioritású csomópontok jelenlegi száma változatlan marad, de azokat a csomópontokat kell azok állapot értéke a **Prioritás miatt kihagyva** állapota. Batch megpróbálja megkeresni a virtuális gépek helyettesítő, és ha sikeres, a csomópontok áthaladnia **létrehozása** , majd **kezdő** állapotok, mielőtt Gopher lett érhető el, illetve a feladatok végrehajtásához, ugyanúgy, mint az új csomópontok.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Alacsony prioritású virtuális gépeket tartalmazó készlet méretezése

Mint dedikált virtuális gépek, kizárólag álló készletek lehetősége egy tartalmazó alacsony prioritású virtuális gépek átméretezése metódus meghívásának vagy az automatikus skálázási készlet méretezésére.

A készlet átméretezés vesz igénybe egy második nem kötelező paraméter, amely frissíti a **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

A készlet automatikus skálázási képletet módon támogatja az alacsony prioritású virtuális gépek:

-   Get, vagy a szolgáltatás által definiált változó értékét állíthatja be **$TargetLowPriorityNodes**.

-   A szolgáltatás által definiált változó értékét kap **$CurrentLowPriorityNodes**.

-   A szolgáltatás által definiált változó értékét kap **$PreemptedNodeCount**. 
    Ezzel a változóval preempted állapotú csomópontok számát adja vissza, és lehetővé teszi, hogy növeljék vagy csökkentsék a dedikált csomópontok háttérbe csomópontot, amely nem érhető el számától függően.

## <a name="jobs-and-tasks"></a>Feladatok és tevékenységek

Feladatok és tevékenységek szükséges további konfigurációs kis alacsony prioritású csomópontok; az egyetlen támogatási a következőképpen történik:

-   Egy feladat JobManagerTask tulajdonságnak egy új tulajdonság **AllowLowPriorityNode**. 
    Ez a tulajdonság értéke igaz, ha a Feladatkezelő tevékenység egy dedikált vagy alacsony prioritású csomópontot is ütemezhető. Ha ez a tulajdonság értéke HAMIS, a Feladatkezelő tevékenység csak a dedikált csomópont van ütemezve.

-   Egy [környezeti változó](batch-compute-node-environment-variables.md) érhető el a tevékenységhez tartozó alkalmazást úgy, hogy megállapíthatja, hogy az alacsony prioritású és dedikált csomóponton futó. A környezeti változó AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>A megelőlegezés kezelése

Virtuális gép időnként háttérbe; Ha a megelőlegezés történik, a Batch a következőket teszi:

-   A preempted virtuális gépek rendelkeznek az állapotuk a frissített **Prioritás miatt kihagyva**.
-   Ha a csomópont a leállított virtuális gépeken futó feladatokat, majd ezeket a feladatokat várólistára és futtassa újból.
-   A virtuális gép hatékonyan törlése, ami a virtuális Gépen található helyileg tárolt adatok elvesztését.
-   A készlet folyamatosan próbál elérni elérhető alacsony prioritású csomópontok célszáma. Ha helyettesítő kapacitás található, a csomópontok tartsa a hozzájuk tartozó azonosítóik, de a újrainicializálni, keresztül **létrehozása** és **kezdő** még nem érhető el a feladat ütemezés szerint.
-   A megelőlegezés counts metrikaként az Azure Portalon érhetők el.

## <a name="metrics"></a>Mérőszámok

Új metrikák érhetők el a [az Azure portal](https://portal.azure.com) alacsony prioritású csomópontok számára. Ezeket a metrikákat a következők:

- Alacsony prioritású csomópontok száma
- Alacsony prioritású magok száma 
- A leállított csomópontok száma

Metrikák megtekintése az Azure Portalon:

1. Keresse meg a portálon a Batch-fiókjába, és a Batch-fiók beállításainak megtekintéséhez.
2. Válassza ki **metrikák** származó a **figyelés** szakaszban.
3. Válassza ki a vár a metrikák a **rendelkezésre álló metrikák** listája.

![Alacsony prioritású csomópontok metrikái](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>További lépések

* Olvassa el [Az Azure Batch funkcióinak áttekintése](batch-api-basics.md) című témakört, amely hasznos információkkal szolgál a Batch használatára készülőknek. A cikk a Batch szolgáltatás erőforrásainak, például a készleteknek, csomópontoknak, feladatoknak, tevékenységeknek és sok olyan API funkciónak a részletesebb információit tartalmazza, amelyeket a Batch-alkalmazás kiépítésekor használhat.
* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
