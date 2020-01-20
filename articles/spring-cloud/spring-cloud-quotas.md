---
title: Az Azure Spring Cloud szolgáltatási csomagjai és kvótái
description: Ismerje meg az Azure Spring Cloud szolgáltatáshoz kapcsolódó kvótákat és szolgáltatási terveket
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278894"
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

Egyes alapértelmezett korlátok és kvóták növelhetők. Ha az erőforrás növekedésre szorul, [hozzon létre egy támogatási kérést](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
