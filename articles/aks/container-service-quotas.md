---
title: "Azure Container Service- (AKS-) kvóták és -régiók rendelkezésre állása"
description: "Az Azure Container Service (AKS) alapértelmezett kvótái és a régiók alapértelmezés szerinti rendelkezésre állása."
services: container-service
author: david-stanford
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 01/16/2018
ms.author: dastanfo
ms.openlocfilehash: eddfab874501fd045820d7da3c968d340fdffe93
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="quotas-and-region-availability-for-azure-container-service-aks"></a>Azure Container Service- (AKS-) kvóták és -régiók rendelkezésre állása

Minden Azure-szolgáltatás tartalmaz az erőforrásokra és a funkciókra vonatkozó alapértelmezett korlátokat. Az alábbi szakaszokban részletes információkat talál számos Container Service- (AKS-) erőforrás alapértelmezett erőforráskorlátjáról, valamint az AKS szolgáltatás rendelkezésre állásáról az Azure-régiókban.

## <a name="service-quotas-and-limits"></a>Szolgáltatási kvóták és korlátok

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Üzembe helyezett infrastruktúra

Az üzembe helyezett infrastruktúrára minden egyéb hálózati, számítási és tárterületi korlátozás érvényes. A korlátozásokkal kapcsolatban tekintse meg [az Azure-előfizetések és -szolgáltatások korlátozásait](../azure-subscription-service-limits.md) ismertető cikket.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az Azure Container Service (AKS) szolgáltatás előzetes verziója az alábbi régiókban érhető el:
- USA keleti régiója
- Nyugat-Európa
- USA középső régiója
- Közép-Kanada
- Kelet-Kanada

## <a name="next-steps"></a>További lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Az ezt támogató erőforrások növelésének kéréséhez nyújtson be egy [Azure-támogatási kérést][azure-support] (a **Probléma típusa** mezőben válassza a „Kvóta” lehetőséget).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
