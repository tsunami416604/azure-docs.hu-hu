---
title: Kvóták, Termékváltozat és régiók rendelkezésre állása az Azure Kubernetes Service (AKS)
description: Ismerje meg az alapértelmezett kvótákat, korlátozott csomópont Virtuálisgép-Termékváltozat-méretek és régiók rendelkezésre állása az Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: abeb9ef6e467b62cf7332e01e1b77c710b9ba4f4
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "64413043"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kvóták, korlátozások a virtuális gép mérete és régiók rendelkezésre állása az Azure Kubernetes Service (AKS)

Minden Azure-szolgáltatás tartalmaz az erőforrásokra és a funkciókra vonatkozó alapértelmezett korlátokat. A csomópont méretét, az egyes virtuális gépek (VM) termékváltozatok a következők majd használatra korlátozott.

Ez a cikk részletesen az alapértelmezett erőforráskorlátok az Azure Kubernetes Service (AKS)-erőforrások, valamint az Azure-régióban az AKS szolgáltatás rendelkezésre állását.

## <a name="service-quotas-and-limits"></a>Szolgáltatási kvóták és korlátok

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Üzembe helyezett infrastruktúra

Az üzembe helyezett infrastruktúrára minden egyéb hálózati, számítási és tárterületi korlátozás érvényes. A korlátozásokkal kapcsolatban tekintse meg [az Azure-előfizetések és -szolgáltatások korlátozásait](../azure-subscription-service-limits.md) ismertető cikket.

## <a name="restricted-vm-sizes"></a>Korlátozott Virtuálisgép-méretek

AKS-fürt egyes csomópontjaihoz például vCPU- és memóriahasználatot számítási erőforrásokon rögzített tartalmazza. Ha egy AKS-csomópont nem elegendő számítási erőforrásokat tartalmaz, a podok sikertelen lehet megfelelő működéséhez. Annak érdekében, hogy a szükséges *kube rendszer* podok és alkalmazások megbízható ütemezhető, a következő virtuális gépek Termékváltozatait nem használható az aks-ben:

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

Egyes alapértelmezett korlátok és kvóták növelhetők. Az ezt támogató erőforrások növelésének kéréséhez nyújtson be egy [Azure-támogatási kérést][azure-support] (a **Probléma típusa** mezőben válassza a „Kvóta” lehetőséget).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
