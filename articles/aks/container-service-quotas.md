---
title: Az Azure Kubernetes Service- (AKS-) kvóták és -régiók rendelkezésre állása
description: Az Azure Kubernetes Service (AKS) alapértelmezett kvótái és a régiók alapértelmezés szerinti rendelkezésre állása.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: c8a2c0cac963fcc0622cff547e85593a13aa076a
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243831"
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
- Délkelet-Ausztrália
- Közép-Kanada
- Kelet-Kanada
- USA középső régiója
- Kelet-Ázsia
- USA keleti régiója
- USA 2. keleti régiója
- Közép-Franciaország
- Kelet-Japán
- Észak-Európa
- Délkelet-Ázsia
- Dél-India
- Az Egyesült Királyság déli régiója
- Az Egyesült Királyság nyugati régiója
- Nyugat-Európa
- USA nyugati régiója
- USA nyugati régiója, 2.

## <a name="next-steps"></a>További lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Az ezt támogató erőforrások növelésének kéréséhez nyújtson be egy [Azure-támogatási kérést][azure-support] (a **Probléma típusa** mezőben válassza a „Kvóta” lehetőséget).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
