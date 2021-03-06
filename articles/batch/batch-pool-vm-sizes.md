---
title: Válassza ki a virtuális gépek méretét és lemezképeit a készletekhez
description: Az elérhető virtuálisgép-méretek és operációsrendszer-verziók kiválasztása Azure Batch-készletekben lévő számítási csomópontokhoz
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: seodec18
ms.openlocfilehash: 8bb54a4db62f56f442f7cec81e6768241a05ffee
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95895230"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>Virtuális gép méretének és rendszerképének kiválasztása Azure Batch készlet számítási csomópontjaihoz

Ha kijelöl egy Azure Batch készlethez tartozó csomópont-méretet, az Azure-ban elérhető szinte minden virtuálisgép-méret közül választhat. Az Azure különféle számítási feladatokhoz biztosít különböző méretű Linux és Windows rendszerű virtuális gépeket.

## <a name="supported-vm-series-and-sizes"></a>Támogatott VM-sorozatok és-méretek

A Batch-készlethez a virtuális gépek méretének kiválasztására néhány kivétel és korlátozás vonatkozik:

- Néhány virtuálisgép-sorozat vagy virtuálisgép-méret nem támogatott a Batchben.
- Bizonyos virtuálisgép-méretek korlátozottak, és a lefoglalásuk előtt kifejezetten engedélyezni kell őket.

### <a name="pools-in-virtual-machine-configuration"></a>Készletek a virtuális gép konfigurációjában

A virtuális gép konfigurációjában a Batch-készletek szinte minden virtuálisgép- [méretet](../virtual-machines/sizes.md)támogatnak. A támogatott méretekkel és korlátozásokkal kapcsolatos további információkért tekintse meg a következő táblázatot.

| Virtuálisgép-sorozatok  | Támogatott méretek |
|------------|---------|
| Alapszintű A | Minden méret, *kivéve* a Basic_A0 (a0) |
| A | Minden méret, *kivéve* Standard_A0 |
| Av2 | Minden méret |
| B | Nem támogatott |
| DC | Nem támogatott |
| Dv2, DSv2 | Minden méret |
| Dv3, Dsv3 | Minden méret |
| <sup>1</sup> . Dav4 | Minden méret |
| <sup>1</sup> . Dasv4 | Minden méret |
| Ddv4, Ddsv4 |  Minden méret |
| DV4, Dsv4 | Nem támogatott |
| Ev3, Esv3 | Minden méret, kivéve a E64is_v3 |
| <sup>1</sup> . Eav4 | Minden méret |
| <sup>1</sup> . Easv4 | Minden méret |
| Edv4, Edsv4 |  Minden méret |
| Ev4, Esv4 | Nem támogatott |
| F, FS | Minden méret |
| Fsv2 | Minden méret |
| G, GS | Minden méret |
| H | Minden méret |
| HB<sup>1</sup> | Minden méret |
| <sup>1</sup> . HBv2 | Minden méret |
| HC<sup>1</sup> | Minden méret |
| Ls | Minden méret |
| <sup>1</sup> . Lsv2 | Minden méret |
| M<sup>1</sup> | Minden méret |
| Mv2<sup>1, 2</sup> | Minden méret |
| NC | Minden méret |
| <sup>1</sup> . NCv2 | Minden méret |
| <sup>1</sup> . NCv3 | Minden méret |
| NCasT4_v3 | Nincs – még nem érhető el |
| <sup>1</sup> . nd | Minden méret |
| <sup>1</sup> . NDv2 | Nincs – még nem érhető el |
| NV | Minden méret |
| <sup>1</sup> . NVv3 | Minden méret |
| <sup>1</sup> . NVv4 | Minden méret |
| SAP HANA | Nem támogatott |

<sup>1</sup> ezek a virtuálisgép-sorozatok lefoglalhatók a virtuális gépek konfigurációjában található batch-készletekben, de létre kell hoznia egy új batch-fiókot, és egy adott [kvóta növelését](batch-quota-limit.md#increase-a-quota)kell kérnie. Ez a korlátozás akkor törlődik, ha a virtuálisgép-sorozatok vCPU-kvótája teljes mértékben támogatott a Batch-fiókok esetében.

<sup>2</sup> ezek a virtuálisgép-sorozatok csak a 2. generációs virtuálisgép-rendszerképekkel használhatók.

### <a name="using-generation-2-vm-images"></a>A 2. generációs VM-rendszerképek használata

Egyes virtuálisgép-sorozatok (például a [Mv2](../virtual-machines/mv2-series.md)) csak 2. [generációs virtuálisgép-rendszerképekkel](../virtual-machines/generation-2.md)használhatók. A 2. generációs virtuálisgép-lemezképek a ["imageReference"](/rest/api/batchservice/pool/add#imagereference) konfigurációjának "SKU" tulajdonságával vannak megadva, mint bármely virtuálisgép-rendszerkép. az "SKU" karakterlánchoz utótag tartozik, például "-G2" vagy "-Gen2". A Batch által támogatott virtuálisgép-rendszerképek listájának lekéréséhez, beleértve a 2. generációs képeket, használja a ["támogatott lemezképek listázása"](/rest/api/batchservice/account/listsupportedimages) API-t, a [PowerShellt](/powershell/module/az.batch/get-azbatchsupportedimage)vagy az [Azure CLI](/cli/azure/batch/pool/supported-images)-t.

### <a name="pools-in-cloud-service-configuration"></a>Készletek a Cloud Service-konfigurációban

A Cloud Service-konfigurációban a Batch-készletek támogatják a Cloud Services összes virtuálisgép- [méretét](../cloud-services/cloud-services-sizes-specs.md) , a következők **kivételével** :

| Virtuálisgép-sorozatok  | Nem támogatott méretek |
|------------|-------------------|
| A-sorozat   | Extra kicsi       |
| Av2-sorozat | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Mérettel kapcsolatos szempontok

- **Alkalmazásokra vonatkozó követelmények** – vegye figyelembe a csomópontokon futtatandó alkalmazás jellemzőit és követelményeit. Az olyan szempontok, mint hogy az alkalmazás többszálú-e vagy mennyi memóriát fogyaszt, segíthetnek meghatározni a legmegfelelőbb és legköltséghatékonyabb csomópontméretet. A többpéldányos [MPI-munkaterhelések](batch-mpi.md) vagy a CUDA-alkalmazások esetében érdemes lehet speciális [HPC](../virtual-machines/sizes-hpc.md) [-vagy GPU-kompatibilis virtuálisgép-](../virtual-machines/sizes-gpu.md) méreteket használni. További információ: [RDMA-kompatibilis vagy GPU-kompatibilis példányok használata batch-készletekben](batch-pool-compute-intensive-sizes.md).

- **Feladatok/csomópont** – tipikusan a csomópontok méretének kiválasztását feltételezve, hogy egyszerre egy tevékenység fut egy csomóponton. Azonban érdemes lehet több feladatot (és így több alkalmazás-példányt) [párhuzamosan futtatni](batch-parallel-node-tasks.md) a számítási csomópontokon a feladat végrehajtása során. Ebben az esetben gyakori, hogy egy többprocesszoros csomópontot válasszon a párhuzamos feladat-végrehajtás megnövekedett igényének kielégítéséhez.

- **Különböző feladatok betöltési szintjei** – a készlet összes csomópontja azonos méretű. Ha eltérő rendszerigényű és/vagy terhelési szintű alkalmazásokat szándékozik futtatni, javasoljuk, hogy használjon különálló készleteket.

- **Régió rendelkezésre állása** – előfordulhat, hogy a virtuálisgép-sorozat vagy-méret nem érhető el azokban a régiókban, ahol létrehozza a Batch-fiókokat. Ha szeretné megtekinteni, hogy elérhető-e a méret, tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/regions/services/).

- **Kvóták** – a Batch-fiókban lévő [magok kvótái](batch-quota-limit.md#resource-quotas) korlátozhatják a Batch-készletbe felvehető adott méretű csomópontok számát. Ha szükséges, [a kvóta növelését is kérheti](batch-quota-limit.md#increase-a-quota).

- **Készlet konfigurációja** – általánosságban több virtuálisgép-méretezési lehetőség is rendelkezésre áll, ha a virtuális gép konfigurációjában hoz létre készletet, a Cloud Service-konfigurációhoz képest.

## <a name="supported-vm-images"></a>Támogatott VM-rendszerképek

A következő API-k egyikével visszaállíthatja a Batch által jelenleg támogatott Windows-és Linux-alapú virtuálisgép-rendszerképek listáját, beleértve az egyes lemezképek csomópont-ügynök SKU-azonosítóit:

- Batch szolgáltatás REST API: [támogatott lemezképek listázása](/rest/api/batchservice/account/listsupportedimages)
- PowerShell: [Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- Azure CLI: [az batch Pool által támogatott – lemezképek](/cli/azure/batch/pool/supported-images)

## <a name="next-steps"></a>További lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- További információ a számítási igényű virtuálisgép-méretek használatáról: [RDMA-kompatibilis vagy GPU-kompatibilis példányok használata batch-készletekben](batch-pool-compute-intensive-sizes.md).
