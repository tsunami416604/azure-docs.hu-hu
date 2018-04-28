---
title: Azure Container Instances-kvóták és -régiók rendelkezésre állása
description: Az Azure Container Instances szolgáltatás kvótái és a régiók alapértelmezés szerinti rendelkezésre állása.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 02/27/2018
ms.author: marsma
ms.openlocfilehash: 22be921c7ab437e08d8ea5032befb8671dc90ed0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Azure Container Instances-kvóták és -régiók rendelkezésre állása

Minden Azure-szolgáltatás tartalmaz az erőforrásokra és a funkciókra vonatkozó alapértelmezett korlátokat. Az alábbi szakaszokban részletes információkat talál számos Azure Container Instances- (ACI-) erőforrás alapértelmezett erőforráskorlátjáról, valamint az ACI szolgáltatás rendelkezésre állásáról az Azure-régiókban.

## <a name="service-quotas-and-limits"></a>Szolgáltatási kvóták és korlátok

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az Azure Container Instances a következő régiókban érhető el a megadott processzor- és memóriakorlátokkal.

| Hely | Operációs rendszer | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| USA nyugati régiója, USA keleti régiója, Nyugat-Európa, Észak-Európa | Linux | 4 | 14 |
| USA nyugati régiója, 2., Délkelet-Ázsia | Linux | 2 | 7 |
| USA nyugati régiója, USA keleti régiója, Nyugat-Európa, Észak-Európa | Windows | 4 | 14 |
| USA nyugati régiója, 2., Délkelet-Ázsia | Windows | 2 | 3.5 |

Az ezen erőforráskorlátokon belül létrehozott tárolópéldányok az üzembe helyezés régiójában állnak rendelkezésre. Amikor egy régió nagy terhelés alatt áll, hibát észlelhet a példányok üzembe helyezésekor. Az ilyen üzembe helyezési hibák csillapítása érdekében próbálja meg alacsonyabb processzor- és memóriabeállításokkal üzembe helyezni a példányokat, vagy próbálja meg később az üzembe helyezést.

A további szükséges régiókról vagy processzorra vagy memóriára vonatkozó magasabb korlátozásokról az [aka.ms/aci/feedback](https://aka.ms/aci/feedback) oldalon tájékoztathatja az érintett csapatot.

A tárolópéldány üzembe helyezésének hibaelhárításáról további információt [az Azure Container Instances üzembe helyezési problémáinak elhárítását](container-instances-troubleshooting.md) ismertető cikkben talál.

## <a name="next-steps"></a>További lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Az ezt támogató erőforrások növelésének kéréséhez nyújtson be egy [Azure-támogatási kérést][azure-support] (a **Probléma típusa** mezőben válassza a „Kvóta” lehetőséget).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
