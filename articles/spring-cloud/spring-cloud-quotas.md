---
title: Az Azure Spring Cloud szolgáltatási csomagjai és kvótái
description: Ismerje meg az Azure Spring Cloud szolgáltatáshoz kapcsolódó kvótákat és szolgáltatási terveket
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 41a2b1a7d9aa5089ba2ee73cd3c5c5c5e31f5225
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607665"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóták és szolgáltatási csomagok az Azure Spring Cloud-hoz

Az összes Azure-szolgáltatás alapértelmezett korlátokat és kvótákat állít be az erőforrásokhoz és a szolgáltatásokhoz.  Az előzetes verzió ideje alatt az Azure Spring Cloud csak egy szolgáltatási csomagot kínál.

Ez a cikk az aktuális előzetes időszakban felkínált szolgáltatási kvótákat részletezi.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure Spring Cloud szolgáltatási szintek és kvóták

Az előzetes verzió ideje alatt az Azure Spring Cloud csak egy szolgáltatási szintet kínál.

Erőforrás | Mennyiség
------- | -------
vCPU | 4
Memory (Memória) | 8 GByte
Azure Spring Cloud-előfizetés | 1
Azure Spring Cloud Service instances régiónként/előfizetés | 2
Alkalmazás-példányok teljes száma Azure Spring Cloud Service-példányon | 50
Alkalmazás-példányok teljes száma tavasszal | 20
Állandó kötetek | 10 x 50 GByte

Amikor eléri a kvótát, egy 400-as hibaüzenet jelenik meg: "a kvóta meghaladja az *előfizetésre* vonatkozó előfizetési korlátot a régió *régiójában, ahol az Azure Spring Cloud Service létrejött*.

## <a name="next-steps"></a>További lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Ha az erőforrás növekedésre szorul, [hozzon létre egy támogatási kérést](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
