---
title: Az Azure Spring Cloud szolgáltatási csomagjai és kvótái
description: Ismerje meg az Azure Spring Cloud szolgáltatáshoz kapcsolódó kvótákat és szolgáltatási terveket
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7d47311385848f96a12a50c1dcf8b8284869c71d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260356"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóták és szolgáltatási csomagok az Azure Spring Cloud-hoz

Az összes Azure-szolgáltatás alapértelmezett korlátokat és kvótákat állít be az erőforrásokhoz és a szolgáltatásokhoz.   Az Azure Spring Cloud két díjszabási szintet kínál: alapszintű és standard. A jelen cikk mindkét szintjére vonatkozóan részletezjük a korlátot.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Az Azure Spring Cloud Service szintjei és korlátai

| Erőforrás | Alapszintű | Standard
------- | ------- | -------
vCPU | 1/szolgáltatási példány | 4/szolgáltatási példány
Memória | 2 GB/szolgáltatási példány | 8 GB/szolgáltatási példány
Azure Spring Cloud Service instances régiónként/előfizetés | 10 | 10
Alkalmazás-példányok teljes száma Azure Spring Cloud Service-példányon | 25 | 500
Tartós kötetek | 1 GB/app x 10 alkalmazás | 50 GB/app x 10 alkalmazás

## <a name="next-steps"></a>További lépések

Néhány alapértelmezett korlát növelhető. Ha a telepítőnek növekedésre van szüksége, [hozzon létre egy támogatási kérést](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
