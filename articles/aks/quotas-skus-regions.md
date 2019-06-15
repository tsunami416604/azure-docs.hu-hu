---
title: Kvóták, SKU-k és régiók rendelkezésre állása az Azure Kubernetes Service (AKS)
description: Ismerje meg az alapértelmezett kvótákat, korlátozott csomópont Virtuálisgép-Termékváltozat-méretek és régiók rendelkezésre állása az Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: 8d4ed8f791858747814972bcf16a9672a7f12610
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65901461"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kvóták, korlátozások a virtuális gép mérete és régiók rendelkezésre állása az Azure Kubernetes Service (AKS)

Az összes Azure-szolgáltatások beállítása az alapértelmezett korlátok és kvóták az erőforrások és szolgáltatások. Egyes virtuális gép (VM) termékváltozatok a következők is korlátozott, használatra.

Ez a cikk részletesen az alapértelmezett erőforráskorlátok Azure Kubernetes Service (AKS)-erőforrások és az Azure-régióban AKS rendelkezésre állását.

## <a name="service-quotas-and-limits"></a>Szolgáltatási kvóták és korlátok

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Üzembe helyezett infrastruktúra

Az üzembe helyezett infrastruktúrára minden egyéb hálózati, számítási és tárterületi korlátozás érvényes. A korlátozásokkal, lásd: [Azure-előfizetés- és Szolgáltatáskorlátok](../azure-subscription-service-limits.md).

> [!IMPORTANT]
> Amikor frissít egy AKS-fürtöt, a további erőforrások átmenetileg használja fel. Ilyen erőforrások többek között a rendelkezésre álló IP-címek egy virtuális hálózat alhálózatához, vagy a virtuális gép vCPU-kvóta. Ha Windows Server-tárolók (jelenleg előzetes verzióban érhető el az aks-ben) használ, az egyetlen támogatott megközelítést alkalmazza a legújabb frissítéseket a csomópontok egy frissítési művelet végrehajtásához. A sikertelen fürtfrissítési folyamat azt jelezheti, hogy nem rendelkezik a rendelkezésre álló IP cím terület vagy vCPU kvótát ideiglenes ezeket az erőforrásokat kezelni. A Windows Server-csomópont frissítési folyamat további információkért lásd: [frissítése az aks-ben csomópontkészletek][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Korlátozott Virtuálisgép-méretek

AKS-fürt egyes csomópontjaihoz például vCPU- és memóriahasználatot számítási erőforrásokon rögzített tartalmazza. Ha egy AKS-csomópont nem elegendő számítási erőforrásokat tartalmaz, a podok megfelelő működéséhez meghiúsulhat. Annak érdekében, hogy a szükséges *kube rendszer* podok és alkalmazások megbízható ütemezhető, ne használja a következő virtuális gépek Termékváltozatait az aks-ben:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

További információ a Virtuálisgép-típusok és a számítási erőforrások: [az Azure-beli virtuális gépek méretei][vm-skus].

## <a name="region-availability"></a>Régiónkénti elérhetőség

A teljes lista arról, hova üzembe helyezése és futtatása a fürtök, lásd: [AKS-régiók rendelkezésre állása][region-availability].

## <a name="next-steps"></a>További lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Ha az erőforrás támogatja-e növelését, növelésére keresztül egy [Azure-támogatási kérést] [ azure-support] (a **Problématípus**, jelölje be **kvóta** ).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
