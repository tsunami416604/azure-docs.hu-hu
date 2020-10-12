---
title: Az Azure Spring Cloud szolgáltatási csomagjai és kvótái
description: Ismerje meg az Azure Spring Cloud szolgáltatáshoz kapcsolódó kvótákat és szolgáltatási terveket
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7c4c832819f61d208d0722823d0a74354960f182
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904264"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóták és szolgáltatási csomagok az Azure Spring Cloud-hoz

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Az összes Azure-szolgáltatás alapértelmezett korlátokat és kvótákat állít be az erőforrásokhoz és a szolgáltatásokhoz.   Az Azure Spring Cloud két díjszabási szintet kínál: alapszintű és standard. A jelen cikk mindkét szintjére vonatkozóan részletezjük a korlátot.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Az Azure Spring Cloud Service szintjei és korlátai

| Erőforrás | Alapszintű | Standard
------- | ------- | -------
vCPU | 1/szolgáltatási példány | 4/szolgáltatási példány
Memória | 2 GB/szolgáltatási példány | 8 GB/szolgáltatási példány
Azure Spring Cloud Service instances régiónként/előfizetés | 10 | 10
Alkalmazás-példányok teljes száma Azure Spring Cloud Service-példányon | 25 | 500
Tartós kötetek | 1 GB/app x 10 alkalmazás | 50 GB/app x 10 alkalmazás

## <a name="next-steps"></a>Következő lépések

Néhány alapértelmezett korlát növelhető. Ha a telepítőnek növekedésre van szüksége, [hozzon létre egy támogatási kérést](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
