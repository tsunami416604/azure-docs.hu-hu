---
title: Virtuálisgép-méretek kiválasztása a készletekhez - Azure Batch | Microsoft dokumentumok
description: Hogyan választhat a számítási csomópontok hoz rendelkezésre álló virtuálisgép-méretek közül az Azure Batch-készletekben
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c18190ec5e5d079d51630a976681717a78a46e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087043"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Virtuálisgép-méret kiválasztása az Azure Batch-készletben lévő számítási csomópontokhoz

Ha kiválaszt egy csomópont mérete egy Azure Batch-készlet, közül választhat szinte az összes virtuális gép mérete az Azure-ban elérhető. Az Azure számos méretben kínál Linux és Windows virtuális gépekhez különböző számítási feladatokhoz.

A virtuális gép méretének kiválasztása néhány kivételt és korlátozást jelent:

* Egyes virtuálisgép-sorozatok vagy virtuálisgép-méretek nem támogatottak a Batch.
* Egyes virtuális gép méretek korlátozottak, és külön engedélyezni kell, mielőtt azok leosztása.

## <a name="supported-vm-series-and-sizes"></a>Támogatott virtuális gépsorozatok és méretek

### <a name="pools-in-virtual-machine-configuration"></a>Készletek a virtuális gép konfigurációjában

A virtuális gép konfigurációjában lévő kötegkészletek szinte minden virtuális gépméretet támogatnak ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). A támogatott méretekről és korlátozásokról az alábbi táblázatban olvashat bővebben.

| Virtuálisgép-sorozatok  | Támogatott méretek |
|------------|---------|
| Alapszintű A | Minden méret, *kivéve* Basic_A0 (A0) |
| A | Minden méret, *kivéve* Standard_A0 |
| Av2 között | Minden méretben |
| B | None |
| DC | None |
| Dv2, DSv2 | Minden méretben |
| Dv3, Dsv3 | Minden méretben |
| Dav4, Dasv4 | Nincs - még nem érhető el |
| Ev3, Esv3 | Minden méret, kivéve a E64is_v3 és E64i_v3 |
| Eav4, Easv4 | Nincs - még nem érhető el |
| F, Fs | Minden méretben |
| Fsv2 között | Minden méretben |
| G, Gs | Minden méretben |
| H | Minden méretben |
| HB<sup>1</sup> | Minden méretben |
| HBv2<sup>1</sup> | Minden méretben |
| HC<sup>1</sup> | Minden méretben |
| Ls | Minden méretben |
| Lsv2 között | Nincs - még nem érhető el |
| M<sup>1.</sup> | Minden méretben, kivéve az M64, M64m, M128, M128m |
| Mv2 között | Nincs - még nem érhető el |
| NC | Minden méretben |
| NCv2,<sup>1.</sup> | Minden méretben |
| NCv3<sup>1</sup> | Minden méretben |
| NN<sup>1</sup> | Minden méretben |
| NDv2<sup>1</sup> | Nincs - még nem érhető el |
| NV | Minden méretben |
| NVv3<sup>1</sup> | Minden méretben |
| NVv4 | None |
| SAP HANA | None |

<sup>1</sup> Ezek a virtuális gépméretek a virtuális gép konfigurációjában kötegkészletekben foglalhatók le, de létre kell hoznia egy új Batch-fiókot, és külön [kvótanövelést kell kérnie.](batch-quota-limit.md#increase-a-quota) Ez a korlátozás akkor törlődik, ha a vCPU-kvóta virtuálisgép-sorozatonként teljes mértékben támogatott a Batch-fiókok esetében.

### <a name="pools-in-cloud-service-configuration"></a>Készletek a Cloud Service konfigurációjában

A Felhőszolgáltatás konfigurációjának kötegkészletei támogatják a Felhőszolgáltatások összes [virtuálisgép-méretét,](../cloud-services/cloud-services-sizes-specs.md) **kivéve** az alábbiakat:

| Virtuálisgép-sorozatok  | Nem támogatott méretek |
|------------|-------------------|
| A-sorozat   | Extra kicsi       |
| Av2-sorozat | Standard_A2_v2 Standard_A2m_v2 Standard_A1_v2 |

## <a name="size-considerations"></a>Mérettel kapcsolatos szempontok

* **Alkalmazáskövetelmények** – Fontolja meg a csomópontokon futtatott alkalmazás jellemzőit és követelményeit. Az olyan szempontok, mint hogy az alkalmazás többszálú-e vagy mennyi memóriát fogyaszt, segíthetnek meghatározni a legmegfelelőbb és legköltséghatékonyabb csomópontméretet. Többpéldányos [MPI-számítási feladatok](batch-mpi.md) vagy CUDA-alkalmazások esetén fontolja meg a speciális [HPC-](../virtual-machines/linux/sizes-hpc.md) vagy [GPU-kompatibilis](../virtual-machines/linux/sizes-gpu.md) virtuális gépméreteket. (Lásd: [RDMA-képes vagy GPU-képes példányok használata kötegkészletekben.)](batch-pool-compute-intensive-sizes.md)

* **Feladatok csomópontonként** – Jellemző, hogy válasszon egy csomópont mérete, feltételezve, hogy egy feladat fut egy csomóponton egy időben. Azonban előnyös lehet, hogy több feladat (és így több alkalmazáspéldány) [fut párhuzamosan](batch-parallel-node-tasks.md) a számítási csomópontokon a feladat végrehajtása során. Ebben az esetben gyakori, hogy válasszon egy többmagos csomópont mérete, hogy megfeleljen a megnövekedett igény a párhuzamos feladat végrehajtása.

* **Terhelési szintek a különböző feladatokhoz** – A készlet összes csomópontja azonos méretű. Ha eltérő rendszerigényű és/vagy terhelési szintű alkalmazásokat szándékozik futtatni, javasoljuk, hogy használjon különálló készleteket.

* **A régió elérhetősége** – Előfordulhat, hogy a virtuális gép sorozata vagy mérete nem érhető el azokban a régiókban, ahol a Batch-fiókokat hozza létre. Ha ellenőrizni szeretné, hogy a méret elérhető-e, olvassa el a [Termékek régiónként i elérhető](https://azure.microsoft.com/regions/services/)jelölőnégyzetet.

* **Kvóták** – A [batch-fiókmagkvóták](batch-quota-limit.md#resource-quotas) korlátozhatják a batch készlethez hozzáadható adott méretű csomópontok számát. A kvótanövelésének igényléséhez olvassa el [ezt a cikket.](batch-quota-limit.md#increase-a-quota) 

* **Készlet konfigurációja** – Általában több virtuális gép méretbeállításait, amikor létrehoz egy készletet a virtuális gép konfigurációjában, összehasonlítva a Felhőszolgáltatás konfigurációjával.

## <a name="next-steps"></a>További lépések

* A Batch részletes áttekintését a [Nagyméretű párhuzamos számítási megoldások fejlesztése a Batch segítségével](batch-api-basics.md)című témakörben találja.
* A nagy számítási igényű virtuálisgép-méretek használatáról az [RDMA-kompatibilis vagy GPU-kompatibilis példányok használata kötegkészletekben](batch-pool-compute-intensive-sizes.md)című témakörben talál.
