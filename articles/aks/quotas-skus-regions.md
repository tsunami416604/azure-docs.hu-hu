---
title: Kvóták, termékkészletek és a régió elérhetősége az Azure Kubernetes szolgáltatásban (AKS)
description: Ismerje meg az alapértelmezett kvótákat, a korlátozott csomóponti virtuálisgép-termékváltozatok méretét és az Azure Kubernetes-szolgáltatás (AKS) régióelérhetőségét.
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 03e7396932f0813ef4bd00d644dcdaddfe229e6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252830"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kvóták, virtuálisgép-méretkorlátozások és a régió elérhetősége az Azure Kubernetes szolgáltatásban (AKS)

Minden Azure-szolgáltatás alapértelmezett korlátokat és kvótákat állít be az erőforrásokhoz és a funkciókhoz. Bizonyos virtuálisgép-(VM) ska-k használata is korlátozott.

Ez a cikk részletezi az Azure Kubernetes-szolgáltatás (AKS) erőforrásainak alapértelmezett erőforráskorlátait és az AKS azure-régiókban való elérhetőségét.

## <a name="service-quotas-and-limits"></a>Szolgáltatási kvóták és korlátok

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Üzembe helyezett infrastruktúra

Az üzembe helyezett infrastruktúrára minden egyéb hálózati, számítási és tárterületi korlátozás érvényes. A megfelelő korlátokat az [Azure-előfizetési és szolgáltatási korlátok című témakörben tés) című témakörben tetszésszerint.](../azure-resource-manager/management/azure-subscription-service-limits.md)

> [!IMPORTANT]
> Az AKS-fürt frissítésekén ideiglenesen további erőforrások at használ fel. Ezek az erőforrások tartalmazzák a virtuális hálózati alhálózatban rendelkezésre álló IP-címeket vagy a virtuális gép vCPU kvótáját. Ha Windows Server-tárolókat használ (jelenleg előzetes verzióban az AKS-ben), az egyetlen jóváhagyott módszer a legújabb frissítések csomópontokra való alkalmazásához egy frissítési művelet végrehajtása. A sikertelen fürtfrissítési folyamat azt jelezheti, hogy nem rendelkezik az ideiglenes erőforrások kezeléséhez rendelkezésre álló IP-címterülettel vagy vCPU-kvótával. A Windows Server csomópontfrissítési folyamatáról az [AKS csomópontkészletének frissítése][nodepool-upgrade]című témakörben talál további információt.

## <a name="restricted-vm-sizes"></a>Korlátozott virtuálisgép-méretek

Az AKS-fürt minden csomópontja meghatározott mennyiségű számítási erőforrást, például vCPU-t és memóriát tartalmaz. Ha egy AKS-csomópont nem tartalmaz elegendő számítási erőforrást, előfordulhat, hogy a podok nem futnak megfelelően. Annak érdekében, hogy a szükséges *kube-system* podok és az alkalmazások megbízhatóan ütemezhetők legyenek, **ne használja a következő VM-termékváltozatokat az AKS-ben:**

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

A virtuálisgép-típusokról és azok számítási erőforrásairól az [Azure-beli virtuális gépek méretei][vm-skus]című témakörben talál további információt.

## <a name="region-availability"></a>Régiónkénti elérhetőség

A fürtök üzembe helyezésének és futtatásának legfrissebb listáját az [AKS-régió elérhetősége című témakörben tájékírja.][region-availability]

## <a name="next-steps"></a>További lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Ha az erőforrás támogatja a növekedést, kérje a növelést egy [Azure támogatási kérelemen][azure-support] keresztül **(a Probléma típusa mezőben**válassza **a Kvóta lehetőséget).**

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
