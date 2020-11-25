---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 2d66e7f497f85141de172c59b67676e1bb93955e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020990"
---
Az [Azure privát végpont](../articles/private-link/private-endpoint-overview.md) egy hálózati adapter, amely privát és biztonságos módon csatlakoztatja Önt egy Azure privát kapcsolat által működtetett szolgáltatáshoz.  A privát végpont a virtuális hálózat egyik magánhálózati IP-címét használja, így hatékonyan bekapcsolja a szolgáltatást a virtuális hálózatba.

A [prémium](../articles/azure-functions/functions-premium-plan.md) és [app Service](../articles/azure-functions/functions-scale.md#app-service-plan) csomagokban üzemeltetett függvények saját végpontot is használhatnak.

Ha egy bejövő magánhálózati végponti kapcsolódást hoz létre a függvények számára, szüksége lesz egy DNS-rekordra is a magánhálózati címek feloldásához.  Alapértelmezés szerint a rendszer létrehoz egy privát DNS-rekordot, amikor a Azure Portal használatával hoz létre privát végpontot.

További információ: [privát végpontok használata web Appshoz](../articles/app-service/networking/private-endpoint.md).