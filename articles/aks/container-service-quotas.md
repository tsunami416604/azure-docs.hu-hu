---
title: Az Azure Kubernetes Service- (AKS-) kvóták és -régiók rendelkezésre állása
description: Az Azure Kubernetes Service (AKS) alapértelmezett kvótái és a régiók alapértelmezés szerinti rendelkezésre állása.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 08/01/2018
ms.author: iainfou
ms.openlocfilehash: 266e2c1f986ba78c9aac40887bc9a7dee1cbff82
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420352"
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
- USA 2. keleti régiója
- Kelet-Japán
- Észak-Európa
- Délkelet-Ázsia
- Az Egyesült Királyság déli régiója
- Nyugat-Európa
- USA nyugati régiója
- USA nyugati régiója, 2.

## <a name="next-steps"></a>További lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Az ezt támogató erőforrások növelésének kéréséhez nyújtson be egy [Azure-támogatási kérést][azure-support] (a **Probléma típusa** mezőben válassza a „Kvóta” lehetőséget).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
