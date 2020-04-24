---
title: Válasszon virtuálisgép-méreteket a készletekhez
description: Az elérhető virtuálisgép-méretek közül választhat a Azure Batch készletekben lévő számítási csomópontok számára
ms.topic: article
ms.date: 09/12/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: d33b97790d2ce9a276a552fd02fe28c796234b15
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115856"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Virtuális gép méretének kiválasztása Azure Batch készlet számítási csomópontjaihoz

Ha kijelöl egy Azure Batch készlethez tartozó csomópont-méretet, az Azure-ban elérhető szinte minden virtuálisgép-méret közül választhat. Az Azure különféle számítási feladatokhoz biztosít különböző méretű Linux és Windows rendszerű virtuális gépeket.

A virtuális gépek méretének kiválasztására néhány kivétel és korlátozás vonatkozik:

* Néhány virtuálisgép-sorozat vagy virtuálisgép-méret nem támogatott a Batchben.
* Bizonyos virtuálisgép-méretek korlátozottak, és a lefoglalásuk előtt kifejezetten engedélyezni kell őket.

## <a name="supported-vm-series-and-sizes"></a>Támogatott VM-sorozatok és-méretek

### <a name="pools-in-virtual-machine-configuration"></a>Készletek a virtuális gép konfigurációjában

A virtuálisgép-konfigurációban található batch-készletek szinte minden VM-méretet támogatnak ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). A támogatott méretekkel és korlátozásokkal kapcsolatos további információkért tekintse meg a következő táblázatot.

| Virtuálisgép-sorozatok  | Támogatott méretek |
|------------|---------|
| Alapszintű A | Minden méret, *kivéve* a Basic_A0 (a0) |
| A | Minden méret, *kivéve* Standard_A0 |
| Av2 | Minden méret |
| B | None |
| DC | None |
| Dv2, DSv2 | Minden méret |
| Dv3, Dsv3 | Minden méret |
| Dav4, Dasv4 | Nincs – még nem érhető el |
| Ev3, Esv3 | Minden méret E64is_v3 és E64i_v3 kivételével |
| Eav4, Easv4 | Nincs – még nem érhető el |
| F, FS | Minden méret |
| Fsv2 | Minden méret |
| G, GS | Minden méret |
| H | Minden méret |
| HB<sup>1</sup> | Minden méret |
| <sup>1</sup> . HBv2 | Minden méret |
| HC<sup>1</sup> | Minden méret |
| Ls | Minden méret |
| Lsv2 | Nincs – még nem érhető el |
| M<sup>1</sup> | Minden méret, kivéve a M64, a M64m, a M128, a M128m |
| Mv2 | Nincs – még nem érhető el |
| NC | Minden méret |
| <sup>1</sup> . NCv2 | Minden méret |
| <sup>1</sup> . NCv3 | Minden méret |
| <sup>1</sup> . nd | Minden méret |
| <sup>1</sup> . NDv2 | Nincs – még nem érhető el |
| NV | Minden méret |
| <sup>1</sup> . NVv3 | Minden méret |
| NVv4 | None |
| SAP HANA | None |

<sup>1</sup> ezek a virtuálisgép-méretek lefoglalhatók a virtuális gépek konfigurációjában található batch-készletekben, de létre kell hoznia egy új batch-fiókot, és egy adott [kvóta növelését](batch-quota-limit.md#increase-a-quota)kell kérnie. Ez a korlátozás akkor törlődik, ha a virtuálisgép-sorozatok vCPU-kvótája teljes mértékben támogatott a Batch-fiókok esetében.

### <a name="pools-in-cloud-service-configuration"></a>Készletek a Cloud Service-konfigurációban

A Cloud Service-konfigurációban a Batch-készletek támogatják a Cloud Services összes virtuálisgép- [méretét](../cloud-services/cloud-services-sizes-specs.md) , a következők **kivételével** :

| Virtuálisgép-sorozatok  | Nem támogatott méretek |
|------------|-------------------|
| A-sorozat   | Extra kicsi       |
| Av2-sorozat | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Mérettel kapcsolatos szempontok

* **Alkalmazásokra vonatkozó követelmények** – vegye figyelembe a csomópontokon futtatandó alkalmazás jellemzőit és követelményeit. Az olyan szempontok, mint hogy az alkalmazás többszálú-e vagy mennyi memóriát fogyaszt, segíthetnek meghatározni a legmegfelelőbb és legköltséghatékonyabb csomópontméretet. A többpéldányos [MPI-munkaterhelések](batch-mpi.md) vagy a CUDA-alkalmazások esetében érdemes lehet speciális [HPC](../virtual-machines/linux/sizes-hpc.md) [-vagy GPU-kompatibilis virtuálisgép-](../virtual-machines/linux/sizes-gpu.md) méreteket használni. (Lásd: [RDMA-kompatibilis vagy GPU-kompatibilis példányok használata batch-készletekben](batch-pool-compute-intensive-sizes.md).)

* **Feladatok/csomópont** – tipikusan a csomópontok méretének kiválasztását feltételezve, hogy egyszerre egy tevékenység fut egy csomóponton. Azonban érdemes lehet több feladatot (és így több alkalmazás-példányt) [párhuzamosan futtatni](batch-parallel-node-tasks.md) a számítási csomópontokon a feladat végrehajtása során. Ebben az esetben gyakori, hogy egy többprocesszoros csomópontot válasszon a párhuzamos feladat-végrehajtás megnövekedett igényének kielégítéséhez.

* **Különböző feladatok betöltési szintjei** – a készlet összes csomópontja azonos méretű. Ha eltérő rendszerigényű és/vagy terhelési szintű alkalmazásokat szándékozik futtatni, javasoljuk, hogy használjon különálló készleteket.

* **Régió rendelkezésre állása** – előfordulhat, hogy a virtuálisgép-sorozat vagy-méret nem érhető el azokban a régiókban, ahol létrehozza a Batch-fiókokat. Ha szeretné megtekinteni, hogy elérhető-e a méret, tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/regions/services/).

* **Kvóták** – a Batch-fiókban lévő [magok kvótái](batch-quota-limit.md#resource-quotas) korlátozhatják a Batch-készletbe felvehető adott méretű csomópontok számát. A kvóta növeléséhez tekintse meg [ezt a cikket](batch-quota-limit.md#increase-a-quota). 

* **Készlet konfigurációja** – általánosságban több virtuálisgép-méretezési lehetőség is rendelkezésre áll, ha a virtuális gép konfigurációjában hoz létre készletet, a Cloud Service-konfigurációhoz képest.

## <a name="next-steps"></a>További lépések

* A Batch részletes áttekintése: [nagy léptékű párhuzamos számítási megoldások létrehozása a Batch szolgáltatással](batch-api-basics.md).
* További információ a számítási igényű virtuálisgép-méretek használatáról: [RDMA-kompatibilis vagy GPU-kompatibilis példányok használata batch-készletekben](batch-pool-compute-intensive-sizes.md).
