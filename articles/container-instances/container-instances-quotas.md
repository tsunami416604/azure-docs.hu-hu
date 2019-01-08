---
title: Azure Container Instances-kvóták és -régiók rendelkezésre állása
description: Az Azure Container Instances szolgáltatás kvótái és a régiók alapértelmezés szerinti rendelkezésre állása.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 12/17/2018
ms.author: danlep
ms.openlocfilehash: 647890517e6f08a4602ebed8ee1057cb45f10cbe
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075498"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Azure Container Instances-kvóták és -régiók rendelkezésre állása

Minden Azure-szolgáltatás tartalmaz az erőforrásokra és a funkciókra vonatkozó alapértelmezett korlátokat. Az alábbi szakaszokban részletes információkat talál számos Azure Container Instances- (ACI-) erőforrás alapértelmezett erőforráskorlátjáról, valamint az ACI szolgáltatás rendelkezésre állásáról az Azure-régiókban.

## <a name="service-quotas-and-limits"></a>Szolgáltatási kvóták és korlátok

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az Azure Container Instances a következő régiókban érhető el a megadott processzor- és memóriakorlátokkal. Értékek: jelenlegi időpontjában aktuálisak. Naprakész információkat a [szolgáltatásai](/rest/api/container-instances/listcapabilities/listcapabilities) API-t. Rendelkezésre állás és erőforrás-korlátok eltérőek lehetnek, ha az Azure Container Instances használatával egy [virtuális hálózat](container-instances-vnet.md) (előzetes verzió) vagy [GPU-erőforrások](container-instances-gpu.md) (előzetes verzió).

| Hely | Operációs rendszer | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Közép-Kanada | Linux | 4 | 16 |
| USA keleti régiója, Észak-Európa, Nyugat-Európa, USA nyugati régiója, USA 2. nyugati régiója | Linux | 4 | 14 |
| Kelet-Japán | Linux | 2 | 8 |
| Kelet-Ausztrália, USA 2. keleti régiója, Délkelet-Ázsia | Linux | 2 | 7 |
| Közép-India, Kelet-Ázsia, USA északi középső RÉGIÓJA, USA déli középső RÉGIÓJA, Dél-India | Linux | 2 | 3.5 |
| Kelet-Európa, Nyugat-Európa, USA nyugati régiója | Windows | 4 | 14 |
| Kelet-Ausztrália, közép-Kanada, közép-India, Kelet-Ázsia, USA keleti RÉGIÓJA 2, kelet-japán, USA északi középső RÉGIÓJA, Észak-Európa, USA déli középső RÉGIÓJA, Dél-India, Délkelet-Ázsia, USA 2. nyugati | Windows | 2 | 3.5 |

Az ezen erőforráskorlátokon belül létrehozott tárolópéldányok az üzembe helyezés régiójában állnak rendelkezésre. Amikor egy régió nagy terhelés alatt áll, hibát észlelhet a példányok üzembe helyezésekor. Az ilyen üzembe helyezési hibák csillapítása érdekében próbálja meg alacsonyabb processzor- és memóriabeállításokkal üzembe helyezni a példányokat, vagy próbálja meg később az üzembe helyezést.

A további szükséges régiókról vagy processzorra vagy memóriára vonatkozó magasabb korlátozásokról az [aka.ms/aci/feedback](https://aka.ms/aci/feedback) oldalon tájékoztathatja az érintett csapatot.

A tárolópéldány üzembe helyezésének hibaelhárításáról további információt [az Azure Container Instances üzembe helyezési problémáinak elhárítását](container-instances-troubleshooting.md) ismertető cikkben talál.

## <a name="next-steps"></a>További lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Az ezt támogató erőforrások növelésének kéréséhez nyújtson be egy [Azure-támogatási kérést][azure-support] (a **Probléma típusa** mezőben válassza a „Kvóta” lehetőséget).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
