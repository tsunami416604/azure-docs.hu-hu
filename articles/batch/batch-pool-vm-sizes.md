---
title: Válassza ki a virtuális gépek méretei az Azure Batch-készletek |} A Microsoft Docs
description: Az Azure Batch-készletekben található számítási csomópontok az elérhető Virtuálisgép-méretek kiválasztása
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: danlep
ms.openlocfilehash: 8f428df07de029bc79f26d5941d22e7472addb95
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915996"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Az Azure Batch-készlet számítási csomópontok Virtuálisgép-méretének kiválasztása

Az Azure Batch-készlet a csomópont méretének kiválasztásakor szinte valamennyi az elérhető Virtuálisgép-méretek az Azure-ban közül választhat. Az Azure számos szolgáltatást kínál méretek Linux és Windows virtuális gépek különböző számítási feladatokhoz. 

Van néhány kivételeket és korlátozásokat a virtuális gép méretének kiválasztása:
* Néhány Virtuálisgép-családok vagy Virtuálisgép-méretek nem támogatottak a Batch szolgáltatásban. 
* Egyes Virtuálisgép-méretek korlátozva, és kifejezetten engedélyezni, mielőtt oszthat ki kell.


## <a name="supported-vm-families-and-sizes"></a>Támogatott VM-családban és méret

### <a name="pools-in-virtual-machine-configuration"></a>A virtuális gép konfigurációs készletek

A virtuális gép konfigurációja a Batch-készleteket támogatja a Virtuálisgép-méretek ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)) *kivételével* az alábbiakat:

| Család  | Nem támogatott méretek  |
|---------|---------|
| Alapszintű A sorozat | Basic_A0 (A0) |
| A-sorozat | Standard_A0 |
| B sorozat | Összes |



### <a name="pools-in-cloud-service-configuration"></a>A Felhőszolgáltatás konfigurációs készletek

A Felhőszolgáltatás konfigurációjában Batch-készleteket támogatja az összes [a Cloud Services Virtuálisgép-méretek](../cloud-services/cloud-services-sizes-specs.md) *kivételével* az alábbiakat:

| Család  | Nem támogatott méretek  |
|---------|---------|
| A-sorozat | ExtraSmall |
| Av2-sorozat | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="restricted-vm-families"></a>Korlátozott Virtuálisgép-család
A következő Virtuálisgép-családok kiosztható a Batch-készletekben, de egy adott kvótanövelést kell igényelnie (lásd: [Ez a cikk](batch-quota-limit.md#increase-a-quota)):
* NCv2 sorozat
* NCv3 sorozat
* ND sorozat

Ezeket a méreteket csak a virtuális gép konfigurációja készletei használható.

## <a name="size-considerations"></a>Méretére vonatkozó szempontok

* **Alkalmazáskövetelmények** – vegye figyelembe a jellemzőit és követelményeit az alkalmazás fogja futtatni a csomópontokon. Az olyan szempontok, mint hogy az alkalmazás többszálú-e vagy mennyi memóriát fogyaszt, segíthetnek meghatározni a legmegfelelőbb és legköltséghatékonyabb csomópontméretet. A többpéldányos [MPI számítási feladatok](batch-mpi.md) vagy CUDA-alkalmazások, fontolja meg a speciális [HPC](../virtual-machines/linux/sizes-hpc.md) vagy [GPU-kompatibilis](../virtual-machines/linux/sizes-gpu.md) rendre a virtuális gép mérete. (Lásd: [használata RDMA-kompatibilis vagy a GPU-kompatibilis példányok a Batch-készletek](batch-pool-compute-intensive-sizes.md).) 

* **Csomópontonkénti tevékenységek** – általában az válasszon egy csomópont mérete feltéve, hogy egy feladat egyszerre egy csomóponton futnak. Azonban érdemes lehet több tevékenységet (és így több alkalmazáspéldányt) előnyös [párhuzamos](batch-parallel-node-tasks.md) egy számítási csomóponton a feladat végrehajtása során. Ebben az esetben szokás válassza ki a Többmagos csomópont méretét a párhuzamos tevékenység-végrehajtás megnövekedett igényének befogadásához.

* **Terhelési szintű a különböző feladatokhoz** – a csomópontok a készlet összes megegyező méretű. Ha eltérő rendszerigényű és/vagy terhelési szintű alkalmazásokat szándékozik futtatni, javasoljuk, hogy használjon különálló készleteket. 

* **Régiónkénti elérhetőség** – A Virtuálisgép-család vagy a méret nem áll rendelkezésre a régiókban, ahol létrehozhatja a Batch-fiókok. Ellenőrizze, hogy elérhető-e méret, lásd: [elérhető termékek régiók szerint](https://azure.microsoft.com/regions/services/).

* **Kvóták** – a [kvóták mag](batch-quota-limit.md#resource-quotas) a kötegben fiókot is hozzáadhat egy Batch-készlet adott méretű csomópontok számát korlátozhatja. Kérje egy kvótájának növelését, lásd: [Ez a cikk](batch-quota-limit.md#increase-a-quota). 

* **Tárolókészlet konfigurációját** – általában több virtuális gép mérete lehetősége van Ha készletet hoz létre a virtuális gép konfigurációja, mint a Felhőszolgáltatás-konfigurációval.

## <a name="next-steps"></a>További lépések

* A Batch részletesebb áttekintéséért lásd: [Develop nagy léptékű párhuzamos számítási megoldások Batch segítségével történő](batch-api-basics.md).
* Nagy számítási igényű Virtuálisgép-méretek használatával kapcsolatos információkért lásd: [használata RDMA-kompatibilis vagy a GPU-kompatibilis példányok a Batch-készletek](batch-pool-compute-intensive-sizes.md). 


