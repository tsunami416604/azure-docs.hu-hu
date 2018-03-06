---
title: "Válassza ki az Azure Batch-címkészletek Virtuálisgép-méretek |} Microsoft Docs"
description: "A számítási csomópontok Azure Batch készletek rendelkezésre álló Virtuálisgép-méretek kiválasztása"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: danlep
ms.openlocfilehash: addd1e9314a754b40cc5d49c0299f007580f512f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Válassza ki a számítási csomópontok Virtuálisgép-méretet az Azure Batch-készlet

Amikor kiválaszt egy csomópont mérete az Azure Batch-készlet, szinte minden a Virtuálisgép-méretek az Azure-ban rendelkezésre álló közül választhat. Azure számos méretek a Linux és a Windows virtuális gépek a különböző munkaterhelések. 

Van néhány kivételeket és korlátozásokat a Virtuálisgép-méret kiválasztása:
* Néhány virtuális gép családok vagy a Virtuálisgép-méretek nem támogatott kötegben. 
* Egyes Virtuálisgép-méretek korlátozva, és ahhoz, azok foglalhatók kifejezetten engedélyezni kell.


## <a name="supported-vm-families-and-sizes"></a>Támogatott Virtuálisgép-családokat és mérete

### <a name="pools-in-virtual-machine-configuration"></a>Virtuálisgép-konfigurációjához célkészleteknek

A virtuális gép konfigurációjához célkészleteknek kötegelt támogatja az egyes Virtuálisgép-méretek ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)) *kivéve* a következő:

| Család  | Nem támogatott méretek  |
|---------|---------|
| Alapszintű A-sorozat | Basic_A0 (A0) |
| A-sorozat | Standard_A0 |
| B sorozat | Összes |
| Fsv2-sorozat<sup>*</sup> | Összes |

<sup>*</sup>A sorozat az ütemterv a jövőbeli támogatási értékek.

### <a name="pools-in-cloud-service-configuration"></a>A felhőalapú szolgáltatás konfigurációs készletek

A felhőalapú szolgáltatás konfigurációjában kötegelt készletek támogatja az összes [Virtuálisgép-méretek a Felhőszolgáltatások](../cloud-services/cloud-services-sizes-specs.md) *kivéve* a következő:

| Család  | Nem támogatott méretek  |
|---------|---------|
| A-sorozat | ExtraSmall |
| Av2-sorozat | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="restricted-vm-families"></a>Korlátozott VM családok
A következő virtuális gép családok kötegelt készletek oszthat ki, de egy adott kvótájának növelését kell igényelnie (lásd: [Ez a cikk](batch-quota-limit.md#increase-a-quota)):
* NCv2 sorozat
* NCv3 sorozat
* ND sorozat

Ezen méretét csak a virtuális gép konfigurációjához célkészleteknek használható.

## <a name="size-considerations"></a>Méret kapcsolatos szempontok

* **Alkalmazásfeltételek** -érdemes lehet jellemzőit és a csomópontokon fogja futtatni az alkalmazás követelményeinek. Az olyan szempontok, mint hogy az alkalmazás többszálú-e vagy mennyi memóriát fogyaszt, segíthetnek meghatározni a legmegfelelőbb és legköltséghatékonyabb csomópontméretet. A többpéldányos [MPI terhelések](batch-mpi.md) vagy CUDA alkalmazást, gondolja át, kifejezetten [HPC](../virtual-machines/linux/sizes-hpc.md) vagy [GPU-kompatibilis](../virtual-machines/linux/sizes-gpu.md) virtuális gép méretét, illetve. (Lásd: [használja az RDMA-kompatibilis vagy a GPU-t példány kötegelt készletek](batch-pool-compute-intensive-sizes.md).) 

* **Csomópontonkénti tevékenységek** – általában egy csomópont mérete feltéve, hogy egyszerre csak egy feladat fut, a csomópont kiválasztása. Azonban előfordulhat, hogy több feladat (és így több alkalmazáspéldányt) előnyös [párhuzamos futtatása](batch-parallel-node-tasks.md) a számítási csomópontok feladat végrehajtása során. Ebben az esetben általában a párhuzamos feladat végrehajtásának igényeknek megfelelően multicore csomópont méret kiválasztása.

* **A különböző feladatokhoz szintek betöltése** -készlet csomópontjain összes azonos méretűnek. Ha eltérő rendszerigényű és/vagy terhelési szintű alkalmazásokat szándékozik futtatni, javasoljuk, hogy használjon különálló készleteket. 

* **Régiónkénti elérhetőség** -méret vagy A virtuális gép termékcsalád nem feltétlenül érhető el a régiókban, ahol a Batch-fiókokat hozhat létre. Ellenőrizze, hogy rendelkezésre áll-e a mérete, lásd: [régiónként rendelkezésre álló termékek](https://azure.microsoft.com/regions/services/).

* **Kvóták** – a [kvóták processzormag](batch-quota-limit.md#resource-quotas) a kötegben fiókot is hozzáadhat a Batch-készlet a megadott méretet a csomópontok számát korlátozhatja. Kérje meg a kvótájának növelését, lásd: [Ez a cikk](batch-quota-limit.md#increase-a-quota). 

* **Tárolókészlet konfigurációs** -készlet létrehozásakor, a virtuálisgép-konfiguráció, a felhőalapú szolgáltatás konfigurációja képest általában rendelkeznek további virtuális gépek méretbeállításai.

## <a name="next-steps"></a>További lépések

* Kötegelt, áttekintéséért lásd: [Develop nagyméretű párhuzamos számítási solutions a kötegelt](batch-api-basics.md).
* Számítási igényű Virtuálisgép-méretek használatával kapcsolatos információkért lásd: [használja az RDMA-kompatibilis vagy a GPU-t példány kötegelt készletek](batch-pool-compute-intensive-sizes.md). 


