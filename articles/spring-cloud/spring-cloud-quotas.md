---
title: Szolgáltatási csomagok és kvóták az Azure Spring Cloud hoz
description: További információ az Azure Spring Cloud szolgáltatási kvótáiról és szolgáltatási csomagjairól
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278894"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóták és szolgáltatási csomagok az Azure Spring Cloud-hoz

Minden Azure-szolgáltatás alapértelmezett korlátokat és kvótákat állít be az erőforrásokhoz és a funkciókhoz.  Az előzetes verzióban az Azure Spring Cloud csak egy szolgáltatási csomagot kínál.

Ez a cikk részletezi az aktuális előzetes verzióban kínált szolgáltatáskvótákat.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Az Azure Spring Cloud szolgáltatási szintjei és kvótái

Az előzetes verzióban az Azure Spring Cloud csak egy szolgáltatási szintet kínál.

Erőforrás | Mennyiség
------- | -------
vCPU | 4 szolgáltatáspéldányonként
Memory (Memória) | 8 GByte szolgáltatáspéldányonként
Azure Spring Cloud szolgáltatáspéldányok régiónként és előfizetésenként | 10
Alkalmazáspéldányok összesen Azure Spring Cloud szolgáltatáspéldányonként | 500
Alkalmazáspéldányok összesen tavaszi alkalmazásonként | 20
Tartós kötetek | 10 x 50 GByte

Amikor elér egy kvótát, 400-as hibaüzenet jelenik meg: "A kvóta túllépi *az előfizetési* korlátot abban a régióban, *ahol az Azure Spring Cloud szolgáltatás létrejön.*

## <a name="next-steps"></a>További lépések

Egyes alapértelmezett korlátok és kvóták növelhetők. Ha az erőforrás növelést igényel, [hozzon létre egy támogatási kérelmet.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
