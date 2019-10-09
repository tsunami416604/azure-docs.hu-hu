---
title: Az Azure Spring Cloud szolgáltatási csomagjai és kvótái
description: Ismerje meg az Azure Spring Cloud szolgáltatáshoz kapcsolódó kvótákat és szolgáltatási terveket
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: 89934ee0ab0c901a904a1a5ac2fb620185571a23
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038781"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóták és szolgáltatási csomagok az Azure Spring Cloud-hoz

Az összes Azure-szolgáltatás alapértelmezett korlátokat és kvótákat állít be az erőforrásokhoz és a szolgáltatásokhoz.  Az előzetes verzió ideje alatt az Azure Spring Cloud csak egy szolgáltatási csomagot kínál.

Ez a cikk az aktuális előzetes időszakban felkínált szolgáltatási kvótákat részletezi.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure Spring Cloud szolgáltatási szintek és kvóták

Az előzetes verzió ideje alatt az Azure Spring Cloud csak egy szolgáltatási szintet kínál.

Resource | Mennyiség
------- | -------
vCPU | 4
Memory (Memória) | 8 GByte
Azure Spring Cloud-előfizetés | 1
Azure Spring Cloud Service instances régiónként/előfizetés | 2
Alkalmazás-példányok teljes száma Azure Spring Cloud Service-példányon | 50
Alkalmazás-példányok teljes száma tavasszal | 20
Állandó kötetek | 10 x 50 GByte

Amikor eléri a kvótát, egy 400-es hibaüzenet jelenik meg, amely a következőket olvassa: "A kvóta meghaladja *az előfizetés előfizetésének* korlátját a régió *régiójában, ahol az Azure Spring Cloud Service létrejött*.

## <a name="next-steps"></a>További lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Ha az erőforrás növekedésre szorul, küldje el nekünk kérelmét: azure-spring-cloud@service.microsoft.com.
