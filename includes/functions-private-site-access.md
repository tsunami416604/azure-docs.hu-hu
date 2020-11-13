---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 2d66e7f497f85141de172c59b67676e1bb93955e
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578814"
---
Az [Azure privát végpont](../articles/private-link/private-endpoint-overview.md) egy hálózati adapter, amely privát és biztonságos módon csatlakoztatja Önt egy Azure privát kapcsolat által működtetett szolgáltatáshoz.  A privát végpont a virtuális hálózat egyik magánhálózati IP-címét használja, így hatékonyan bekapcsolja a szolgáltatást a virtuális hálózatba.

A [prémium](../articles/azure-functions/functions-premium-plan.md) és [app Service](../articles/azure-functions/functions-scale.md#app-service-plan) csomagokban üzemeltetett függvények saját végpontot is használhatnak.

Ha egy bejövő magánhálózati végponti kapcsolódást hoz létre a függvények számára, szüksége lesz egy DNS-rekordra is a magánhálózati címek feloldásához.  Alapértelmezés szerint a rendszer létrehoz egy privát DNS-rekordot, amikor a Azure Portal használatával hoz létre privát végpontot.

További információ: [privát végpontok használata web Appshoz](../articles/app-service/networking/private-endpoint.md).