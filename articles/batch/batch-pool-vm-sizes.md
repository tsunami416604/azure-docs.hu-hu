---
title: VIRTUÁLIS gépek méretének kiválasztása készletekhez – Azure Batch | Microsoft Docs
description: Az elérhető virtuálisgép-méretek közül választhat a Azure Batch készletekben lévő számítási csomópontok számára
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: fd88f8e9b32b3fe5a0d7ab0caf233098ea19fde0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323092"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Virtuális gép méretének kiválasztása Azure Batch készlet számítási csomópontjaihoz

Ha kijelöl egy Azure Batch készlethez tartozó csomópont-méretet, az Azure-ban elérhető szinte minden virtuálisgép-méret közül választhat. Az Azure különféle számítási feladatokhoz biztosít különböző méretű Linux és Windows rendszerű virtuális gépeket.

A virtuális gépek méretének kiválasztására néhány kivétel és korlátozás vonatkozik:

* Néhány virtuálisgép-sorozat vagy virtuálisgép-méret nem támogatott a Batchben.
* Bizonyos virtuálisgép-méretek korlátozottak, és a lefoglalásuk előtt kifejezetten engedélyezni kell őket.

## <a name="supported-vm-series-and-sizes"></a>Támogatott VM-sorozatok és-méretek

### <a name="pools-in-virtual-machine-configuration"></a>Készletek a virtuális gép konfigurációjában

A virtuálisgép-konfigurációban található batch-készletek szinte minden VM-méretet támogatnak ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). A támogatott méretekkel és korlátozásokkal kapcsolatos további információkért tekintse meg a következő táblázatot.

A nem felsorolt promóciós vagy előnézeti virtuálisgép-méretek támogatása nem garantált.

| VM-sorozat  | Támogatott méretek | Batch-fiók készletének kiosztási módja<sup>1</sup> |
|------------|---------|-----------------|
| Alapszintű A sorozat | Az összes méret, *kivéve* a Basic_A0 (a0) | Any |
| A-sorozat | A Standard_A0 *kivételével* minden méret | Any |
| Av2-sorozat | Minden méret | Any |
| B sorozat | Nincsenek | Nem érhető el |
| DC sorozat | Nincsenek | Nem érhető el |
| Dv2, Dsv2 sorozat | Minden méret | Any |
| Dv3, Dsv3 sorozat | Minden méret | Any |
| [Memóriára optimalizált méretek](../virtual-machines/linux/sizes-memory.md) | Nincsenek | Nem érhető el |
| Fsv2-sorozat | Minden méret | Any |
| H-sorozat | Minden méret | Any |
| HB –<sup>2</sup> . sorozat | Minden méret | Any |
| HC-sorozat<sup>2</sup> | Minden méret | Any |
| Ls-sorozat | Minden méret | Any |
| Lsv2 sorozat | Nincsenek | Nem érhető el |
| M sorozat | Standard m64ms (csak alacsony prioritású), standard m128s (csak alacsony prioritású) | Any |  
| NCv2 –<sup>2</sup> . sorozat | Minden méret | Any |
| NCv3 –<sup>2</sup> . sorozat | Minden méret | Any |
| ND sorozat<sup>2</sup> | Minden méret | Any |
| NDv2 sorozat | Minden méret | Felhasználói előfizetés mód |
| NV sorozat | Minden méret | Any |
| NVv3 sorozat | Nincsenek | Nem érhető el |
| SAP HANA | Nincsenek | Nem érhető el |

<sup>1</sup> az újabb virtuálisgép-sorozatok kezdetben részlegesen támogatottak. Ezeket a virtuálisgép-sorozatokat a Batch-fiókok a **felhasználói**előfizetésre beállított kiosztási **móddal** tudják lefoglalni. A Batch-fiók konfigurálásával kapcsolatos további információkért lásd: [Batch-fiókok kezelése](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) . Tekintse meg a [kvóták és korlátok](batch-quota-limit.md) című témakört, amelyből megtudhatja, hogyan kérhet kvótát a részben támogatott virtuálisgép-sorozatokhoz a **felhasználói előfizetés** batch  

<sup>2</sup> ezek a virtuálisgép-méretek lefoglalhatók a virtuális gépek konfigurációjában található batch-készletekben, de az adott [kvóta növelését](batch-quota-limit.md#increase-a-quota)kell kérnie.

### <a name="pools-in-cloud-service-configuration"></a>Készletek a Cloud Service-konfigurációban

A Cloud Service-konfigurációban a Batch-készletek támogatják a [Cloud Services](../cloud-services/cloud-services-sizes-specs.md) összes virtuálisgép-méretét, a következők **kivételével** :

| VM-sorozat  | Nem támogatott méretek |
|------------|-------------------|
| A-sorozat   | Extra kicsi       |
| Av2-sorozat | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Mérettel kapcsolatos megfontolások

* **Alkalmazásokra vonatkozó követelmények** – vegye figyelembe a csomópontokon futtatandó alkalmazás jellemzőit és követelményeit. Az olyan szempontok, mint hogy az alkalmazás többszálú-e vagy mennyi memóriát fogyaszt, segíthetnek meghatározni a legmegfelelőbb és legköltséghatékonyabb csomópontméretet. A többpéldányos [MPI](batch-mpi.md) -munkaterhelések vagy a CUDA-alkalmazások esetében érdemes lehet speciális [HPC](../virtual-machines/linux/sizes-hpc.md) [-vagy GPU-kompatibilis virtuálisgép-](../virtual-machines/linux/sizes-gpu.md) méreteket használni. (Lásd: [RDMA-kompatibilis vagy GPU-kompatibilis példányok használata batch-készletekben](batch-pool-compute-intensive-sizes.md).)

* **Feladatok/csomópont** – tipikusan a csomópontok méretének kiválasztását feltételezve, hogy egyszerre egy tevékenység fut egy csomóponton. Azonban érdemes lehet több feladatot (és így több alkalmazás-példányt) [párhuzamosan futtatni](batch-parallel-node-tasks.md) a számítási csomópontokon a feladat végrehajtása során. Ebben az esetben gyakori, hogy egy többprocesszoros csomópontot válasszon a párhuzamos feladat-végrehajtás megnövekedett igényének kielégítéséhez.

* **Különböző feladatok betöltési szintjei** – a készlet összes csomópontja azonos méretű. Ha eltérő rendszerigényű és/vagy terhelési szintű alkalmazásokat szándékozik futtatni, javasoljuk, hogy használjon különálló készleteket.

* **Régió rendelkezésre állása** – előfordulhat, hogy a virtuálisgép-sorozat vagy-méret nem érhető el azokban a régiókban, ahol létrehozza a Batch-fiókokat. Ha szeretné megtekinteni, hogy elérhető-e a méret, tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/regions/services/).

* **Kvóták** – a Batch-fiókban lévő [magok kvótái](batch-quota-limit.md#resource-quotas) korlátozhatják a Batch-készletbe felvehető adott méretű csomópontok számát. A kvóta növeléséhez tekintse meg [ezt a cikket](batch-quota-limit.md#increase-a-quota). 

* **Készlet konfigurációja** – általánosságban több virtuálisgép-méretezési lehetőség is rendelkezésre áll, ha a virtuális gép konfigurációjában hoz létre készletet, a Cloud Service-konfigurációhoz képest.

## <a name="next-steps"></a>További lépések

* A Batch részletes áttekintése: [nagy léptékű párhuzamos számítási megoldások létrehozása a Batch szolgáltatással](batch-api-basics.md).
* További információ a számítási igényű virtuálisgép-méretek használatáról: [RDMA-kompatibilis vagy GPU-kompatibilis példányok használata batch](batch-pool-compute-intensive-sizes.md)-készletekben.
