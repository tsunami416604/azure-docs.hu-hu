---
title: Az Azure Kubernetes Service- (AKS-) kvóták és -régiók rendelkezésre állása
description: Az Azure Kubernetes Service (AKS) alapértelmezett kvótái és a régiók alapértelmezés szerinti rendelkezésre állása.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: iainfou
ms.openlocfilehash: 6d4fa9a06f3baeb51505c3e6060e9689428f8386
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100675"
---
# <a name="quotas-and-region-availability-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)-kvóták és -régiók rendelkezésre állása

Minden Azure-szolgáltatás tartalmaz az erőforrásokra és a funkciókra vonatkozó alapértelmezett korlátokat. Az alábbi szakaszokban részletes információkat talál számos Azure Kubernetes Service- (AKS-) erőforrás alapértelmezett erőforráskorlátjáról, valamint az AKS szolgáltatás rendelkezésre állásáról az Azure-régiókban.

## <a name="service-quotas-and-limits"></a>Szolgáltatási kvóták és korlátok

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Üzembe helyezett infrastruktúra

Az üzembe helyezett infrastruktúrára minden egyéb hálózati, számítási és tárterületi korlátozás érvényes. A korlátozásokkal kapcsolatban tekintse meg [az Azure-előfizetések és -szolgáltatások korlátozásait](../azure-subscription-service-limits.md) ismertető cikket.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az Azure Kubernetes Service (AKS) szolgáltatás az alábbi régiókban érhető el:

- Kelet-Ausztrália
- Közép-Kanada
- Kelet-Kanada
- USA középső régiója
- USA keleti régiója
- Észak-Európa
- Az Egyesült Királyság déli régiója
- Nyugat-Európa
- USA nyugati régiója
- USA nyugati régiója, 2.

## <a name="next-steps"></a>További lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Az ezt támogató erőforrások növelésének kéréséhez nyújtson be egy [Azure-támogatási kérést][azure-support] (a **Probléma típusa** mezőben válassza a „Kvóta” lehetőséget).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
