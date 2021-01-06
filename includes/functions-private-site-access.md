---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 3c9679f3d66d58c7a6c847b54c84438c979ecd39
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936862"
---
Az [Azure privát végpont](../articles/private-link/private-endpoint-overview.md) egy hálózati adapter, amely privát és biztonságos módon csatlakoztatja Önt egy Azure privát kapcsolat által működtetett szolgáltatáshoz.  A privát végpont a virtuális hálózat egyik magánhálózati IP-címét használja, így hatékonyan bekapcsolja a szolgáltatást a virtuális hálózatba.

A [prémium](../articles/azure-functions/functions-premium-plan.md) és [app Service](../articles/azure-functions/dedicated-plan.md) csomagokban üzemeltetett függvények saját végpontot is használhatnak.

Ha egy bejövő magánhálózati végponti kapcsolódást hoz létre a függvények számára, szüksége lesz egy DNS-rekordra is a magánhálózati címek feloldásához.  Alapértelmezés szerint a rendszer létrehoz egy privát DNS-rekordot, amikor a Azure Portal használatával hoz létre privát végpontot.

További információ: [privát végpontok használata web Appshoz](../articles/app-service/networking/private-endpoint.md).