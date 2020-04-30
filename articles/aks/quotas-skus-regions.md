---
title: Erőforrások, SKU-régiók és régiók korlátai
titleSuffix: Azure Kubernetes Service
description: Ismerje meg az alapértelmezett kvótákat, a korlátozott csomópontú virtuális gépek SKU-méreteit és az Azure Kubernetes szolgáltatás (ak) régiójának rendelkezésre állását.
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: e3edbcf9603657ce0c747b01b3c59c2923bc0181
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208023"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kvóták, virtuális gépek méretére vonatkozó korlátozások és a régió elérhetősége az Azure Kubernetes szolgáltatásban (ak)

Az összes Azure-szolgáltatás alapértelmezett korlátokat és kvótákat állít be az erőforrásokhoz és a szolgáltatásokhoz. Bizonyos virtuálisgép-(VM-) SKU-ket is korlátozni kell a használatra.

Ez a cikk az Azure Kubernetes-szolgáltatás (ak) erőforrásainak alapértelmezett erőforrás-korlátait és az AK-ban található AK-régiók elérhetőségét részletezi.

## <a name="service-quotas-and-limits"></a>Szolgáltatási kvóták és korlátok

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Üzembe helyezett infrastruktúra

Az üzembe helyezett infrastruktúrára minden egyéb hálózati, számítási és tárterületi korlátozás érvényes. A vonatkozó korlátokat az [Azure-előfizetések és-szolgáltatások korlátozásai](../azure-resource-manager/management/azure-subscription-service-limits.md)című témakörben tekintheti meg.

> [!IMPORTANT]
> Ha egy AK-fürtöt frissít, a további erőforrásokat átmenetileg felhasználjuk. Ezek az erőforrások a virtuális hálózati alhálózaton vagy a virtuális gép vCPU kvótájában elérhető IP-címeket foglalják magukban. Ha Windows Server-tárolókat használ, az egyetlen támogatott megközelítés, amely a legújabb frissítéseket alkalmazza a csomópontokon, a frissítési művelet végrehajtásához. A fürt sikertelen frissítési folyamata azt jelezheti, hogy nem rendelkezik a rendelkezésre álló IP-címtartomány vagy vCPU kvóta az ideiglenes erőforrások kezeléséhez. További információ a Windows Server-csomópont frissítési folyamatáról: [Node-készlet frissítése az AK-ban][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Korlátozott virtuálisgép-méretek

Az AK-fürtök mindegyik csomópontja rögzített mennyiségű számítási erőforrást (például vCPU és memóriát) tartalmaz. Ha egy AK-csomópont nem elegendő számítási erőforrást tartalmaz, előfordulhat, hogy a hüvelyek nem fognak megfelelően futni. Annak érdekében, hogy a szükséges *Kube* hüvelyek és az alkalmazásai megbízhatóan ütemezhetők legyenek, **ne használja a következő virtuálisgép-SKU-ket az AK-ban**:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

A virtuálisgép-típusokkal és a számítási erőforrásokkal kapcsolatos további információkért lásd: [virtuális gépek méretei az Azure-ban][vm-skus].

## <a name="region-availability"></a>Régiónkénti elérhetőség

A fürtök üzembe helyezésének és futtatásának legújabb listáját lásd: az [AK-régiók rendelkezésre állása][region-availability].

## <a name="next-steps"></a>További lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Ha az erőforrás támogatja a növekedést, kérje a növelést egy [Azure-támogatási kérelemben][azure-support] (a **probléma típusa**beállításnál válassza a **kvóta**lehetőséget).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
