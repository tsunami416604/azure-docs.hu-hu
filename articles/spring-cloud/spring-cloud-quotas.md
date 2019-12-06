---
title: Az Azure Spring Cloud szolgáltatási csomagjai és kvótái
description: Ismerje meg az Azure Spring Cloud szolgáltatáshoz kapcsolódó kvótákat és szolgáltatási terveket
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 0518b13ea228b4834a095a9bf126b131e70a5f45
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851553"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóták és szolgáltatási csomagok az Azure Spring Cloud-hoz

Az összes Azure-szolgáltatás alapértelmezett korlátokat és kvótákat állít be az erőforrásokhoz és a szolgáltatásokhoz.  Az előzetes verzió ideje alatt az Azure Spring Cloud csak egy szolgáltatási csomagot kínál.

Ez a cikk az aktuális előzetes időszakban felkínált szolgáltatási kvótákat részletezi.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure Spring Cloud szolgáltatási szintek és kvóták

Az előzetes verzió ideje alatt az Azure Spring Cloud csak egy szolgáltatási szintet kínál.

Erőforrás | Mennyiség
------- | -------
vCPU | 4/szolgáltatási példány
Memória | 8 GByte
Azure Spring Cloud Service instances régiónként/előfizetés | 10
Alkalmazás-példányok teljes száma Azure Spring Cloud Service-példányon | 500
Alkalmazás-példányok teljes száma tavasszal | 20
Állandó kötetek | 10 x 50 GByte

Amikor eléri a kvótát, egy 400-as hibaüzenet jelenik meg: "a kvóta meghaladja az *előfizetésre* vonatkozó előfizetési korlátot a régió *régiójában, ahol az Azure Spring Cloud Service létrejött*.

## <a name="next-steps"></a>Következő lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Ha az erőforrás növekedésre szorul, [hozzon létre egy támogatási kérést](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
