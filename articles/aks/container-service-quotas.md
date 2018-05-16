---
title: Az Azure Kubernetes Service- (AKS-) kvóták és -régiók rendelkezésre állása
description: Az Azure Kubernetes Service (AKS) alapértelmezett kvótái és a régiók alapértelmezés szerinti rendelkezésre állása.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 04/26/2018
ms.author: nepeters
ms.openlocfilehash: adf2d57961df9a4e8d03f2b3fe43ca0603685eb2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="quotas-and-region-availability-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)-kvóták és -régiók rendelkezésre állása

Minden Azure-szolgáltatás tartalmaz az erőforrásokra és a funkciókra vonatkozó alapértelmezett korlátokat. Az alábbi szakaszokban részletes információkat talál számos Azure Kubernetes Service- (AKS-) erőforrás alapértelmezett erőforráskorlátjáról, valamint az AKS szolgáltatás rendelkezésre állásáról az Azure-régiókban.

## <a name="service-quotas-and-limits"></a>Szolgáltatási kvóták és korlátok

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Üzembe helyezett infrastruktúra

Az üzembe helyezett infrastruktúrára minden egyéb hálózati, számítási és tárterületi korlátozás érvényes. A korlátozásokkal kapcsolatban tekintse meg [az Azure-előfizetések és -szolgáltatások korlátozásait](../azure-subscription-service-limits.md) ismertető cikket.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az Azure Kubernetes Service (AKS) szolgáltatás előzetes verziója az alábbi régiókban érhető el:
- USA keleti régiója
- Nyugat-Európa
- USA középső régiója
- Közép-Kanada
- Kelet-Kanada

## <a name="next-steps"></a>További lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Az ezt támogató erőforrások növelésének kéréséhez nyújtson be egy [Azure-támogatási kérést][azure-support] (a **Probléma típusa** mezőben válassza a „Kvóta” lehetőséget).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
