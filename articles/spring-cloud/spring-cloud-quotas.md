---
title: Az Azure Spring Cloud szolgáltatási csomagjai és kvótái
description: Ismerje meg az Azure Spring Cloud szolgáltatáshoz kapcsolódó kvótákat és szolgáltatási terveket
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cda22c63a70c5121e6a6972c66bdc0a4bb5158fc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089464"
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


Az előzetes verzió ideje alatt az Azure Spring Cloud csak egy szolgáltatási szintet kínál. Ha eléri a korlátot, egy 400-as hibaüzenet jelenik meg: "a kvóta meghaladja az *előfizetésre* vonatkozó előfizetési korlátot a régió *régiójában, ahol az Azure Spring Cloud Service létrejött*.

## <a name="next-steps"></a>További lépések

Néhány alapértelmezett korlát növelhető. Ha a telepítőnek növekedésre van szüksége, [hozzon létre egy támogatási kérést](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
