---
title: Válassza ki a virtuális gépek méretei készletek – Azure Batch |} A Microsoft Docs
description: Az Azure Batch-készletekben található számítási csomópontok az elérhető Virtuálisgép-méretek kiválasztása
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
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
ms.openlocfilehash: 1848891a0a37235c9769b3cee18262239e19df5a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502663"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Az Azure Batch-készlet számítási csomópontok Virtuálisgép-méretének kiválasztása

Az Azure Batch-készlet a csomópont méretének kiválasztásakor szinte valamennyi az elérhető Virtuálisgép-méretek az Azure-ban közül választhat. Az Azure számos szolgáltatást kínál méretek Linux és Windows virtuális gépek különböző számítási feladatokhoz.

Van néhány kivételeket és korlátozásokat a virtuális gép méretének kiválasztása:

* Néhány Virtuálisgép-sorozatok vagy Virtuálisgép-méretek nem támogatottak a Batch szolgáltatásban.
* Egyes Virtuálisgép-méretek korlátozva, és kifejezetten engedélyezni, mielőtt oszthat ki kell.

## <a name="supported-vm-series-and-sizes"></a>Támogatott Virtuálisgép-sorozatok és méret

### <a name="pools-in-virtual-machine-configuration"></a>A virtuális gép konfigurációs készletek

A virtuális gép konfigurációja a Batch-készletek szinte összes Virtuálisgép-méretek támogatják ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Tekintse meg a következő táblázat további információ a támogatott méretek és korlátozások.

Minden promóciós vagy előzetes Virtuálisgép-méretek nem szerepel a listán a támogatás nem garantált.

| Virtuálisgép-sorozatok  | Támogatott méretek | Fiók készletlefoglalási módja a batch<sup>1</sup> |
|------------|---------|-----------------|
| Alapszintű A sorozat | Méretek *kivételével* Basic_A0 (A0) | Bármely |
| A-sorozat | Méretek *kivételével* Standard_A0 | Bármely |
| Av2-sorozat | Minden méretben | Bármely |
| B sorozat | None | Nem érhető el |
| DC sorozat | None | Nem érhető el |
| A Dv2 sorozat Dsv2 | Minden méretben | Bármely |
| Dv3, Dsv3 sorozat | Minden méretben | Bármely |
| [Memóriahasználatra optimalizált méretek](../virtual-machines/linux/sizes-memory.md) | None | Nem érhető el |
| Fsv2-sorozat | Minden méretben | Bármely |
| H-sorozat | Minden méretben | Bármely |
| Hb-series<sup>2</sup> | Minden méretben | Bármely |
| Hibrid kapcsolat sorozat<sup>2</sup> | Minden méretben | Bármely |
| Ls-sorozat | Minden méretben | Bármely |
| Lsv2 sorozat | None | Nem érhető el |
| M sorozat | Standard m 64 MS (az alacsony prioritású csak), Standard m128s (csak az alacsony prioritású verzió esetén) | Bármely |  
| NCv2 sorozat<sup>2</sup> | Minden méretben | Bármely |
| Az NCv3 sorozatú<sup>2</sup> | Minden méretben | Bármely |
| ND sorozat<sup>2</sup> | Minden méretben | Bármely |
| NDv2-sorozat | Minden méretben | Felhasználói előfizetés mód |
| NV sorozat | Minden méretben | Bármely |
| NVv3 sorozat | None | Nem érhető el |
| SAP HANA | None | Nem érhető el |

<sup>1</sup> néhány újabb Virtuálisgép-sorozatok részben kezdetben támogatott. Ezek a Virtuálisgép-sorozatok Batch-fiókokat kell elosztania az **készletlefoglalási mód** beállítása **felhasználói előfizetés**. Lásd: [kezelése a Batch-fiókok](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) további információt a Batch-fiók konfigurációját. Lásd: [kvótái és korlátai](batch-quota-limit.md) megtudhatja, hogyan kérhetnek ezek kvóta részben támogatott a Virtuálisgép-sorozatok **felhasználói előfizetés** Batch-fiókok.  

<sup>2</sup> ezek Virtuálisgép-méretek kiosztható a Batch-készletekben található virtuálisgép-konfiguráció, de egy adott kell igényelnie [kvótanövelést](batch-quota-limit.md#increase-a-quota).

### <a name="pools-in-cloud-service-configuration"></a>A Felhőszolgáltatás konfigurációs készletek

A Felhőszolgáltatás konfigurációjában Batch-készleteket támogatja az összes [a Cloud Services Virtuálisgép-méretek](../cloud-services/cloud-services-sizes-specs.md) **kivételével** az alábbiakat:

| Virtuálisgép-sorozatok  | Nem támogatott méretek |
|------------|-------------------|
| A-sorozat   | Nagyon kicsi       |
| Av2-sorozat | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Méretére vonatkozó szempontok

* **Alkalmazáskövetelmények** – vegye figyelembe a jellemzőit és követelményeit az alkalmazás fogja futtatni a csomópontokon. Az olyan szempontok, mint hogy az alkalmazás többszálú-e vagy mennyi memóriát fogyaszt, segíthetnek meghatározni a legmegfelelőbb és legköltséghatékonyabb csomópontméretet. A többpéldányos [MPI számítási feladatok](batch-mpi.md) vagy CUDA-alkalmazások, fontolja meg a speciális [HPC](../virtual-machines/linux/sizes-hpc.md) vagy [GPU-kompatibilis](../virtual-machines/linux/sizes-gpu.md) rendre a virtuális gép mérete. (Lásd: [használata RDMA-kompatibilis vagy a GPU-kompatibilis példányok a Batch-készletek](batch-pool-compute-intensive-sizes.md).)

* **Csomópontonkénti tevékenységek** – általában az válasszon egy csomópont mérete feltéve, hogy egy feladat egyszerre egy csomóponton futnak. Azonban érdemes lehet több tevékenységet (és így több alkalmazáspéldányt) előnyös [párhuzamos](batch-parallel-node-tasks.md) egy számítási csomóponton a feladat végrehajtása során. Ebben az esetben szokás válassza ki a Többmagos csomópont méretét a párhuzamos tevékenység-végrehajtás megnövekedett igényének befogadásához.

* **Terhelési szintű a különböző feladatokhoz** – a csomópontok a készlet összes megegyező méretű. Ha eltérő rendszerigényű és/vagy terhelési szintű alkalmazásokat szándékozik futtatni, javasoljuk, hogy használjon különálló készleteket.

* **Régiónkénti elérhetőség** – egy Virtuálisgép-sorozat és a méret nem áll rendelkezésre a régiókban, ahol létrehozhatja a Batch-fiókok. Ellenőrizze, hogy elérhető-e méret, lásd: [elérhető termékek régiók szerint](https://azure.microsoft.com/regions/services/).

* **Kvóták** – a [kvóták mag](batch-quota-limit.md#resource-quotas) a kötegben fiókot is hozzáadhat egy Batch-készlet adott méretű csomópontok számát korlátozhatja. Kérje egy kvótájának növelését, lásd: [Ez a cikk](batch-quota-limit.md#increase-a-quota). 

* **Tárolókészlet konfigurációját** – általában több virtuális gép mérete lehetősége van Ha készletet hoz létre a virtuális gép konfigurációja, mint a Felhőszolgáltatás-konfigurációval.

## <a name="next-steps"></a>További lépések

* A Batch részletesebb áttekintéséért lásd: [Develop nagy léptékű párhuzamos számítási megoldások Batch segítségével történő](batch-api-basics.md).
* Nagy számítási igényű Virtuálisgép-méretek használatával kapcsolatos információkért lásd: [használata RDMA-kompatibilis vagy a GPU-kompatibilis példányok a Batch-készletek](batch-pool-compute-intensive-sizes.md).
