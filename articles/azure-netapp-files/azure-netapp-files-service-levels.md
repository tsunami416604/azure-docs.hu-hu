---
title: Szolgáltatásszintek NetApp Azure-fájlok |} A Microsoft Docs
description: Ismerteti a szolgáltatási szintekhez Azure NetApp fájlok teljesítménye szempontjából.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 1f9c427045c9d42f6a11cc4bcc798cfc47a4428c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523103"
---
# <a name="service-levels-for-azure-netapp-files"></a>Az Azure NetApp Files szolgáltatásszintjei
Szolgáltatási szintekhez olyan attribútum, amely a kapacitás-készletben. Szolgáltatási szintek és határozza meg az engedélyezett maximális átviteli sebesség egy kötet a kapacitás-készlet, amely a kötethez hozzárendelt kvóta alapján különbözteti meg.

## <a name="supported-service-levels"></a>Támogatott szolgáltatási szintek

NetApp Azure Files három szolgáltatási szintet támogat: *Ultranagy*, *prémium*, és *Standard*. 

* <a name="Ultra"></a>Ultranagy storage

    Az Ultranagy tárolási szintet biztosít akár 128 MiB/s sebességet hozzárendelt köteten kvóta 1 Tib-ra. 

* <a name="Premium"></a>A Premium storage

    A prémium szintű storage akár 64 MiB/s sebességet hozzárendelt köteten kvóta 1 TiB biztosít. 

* <a name="Standard"></a>Standard szintű storage

    A standard szintű tárolási réteget biztosít, akár 16 MiB/s sebességet hozzárendelt köteten kvóta 1 Tib-ra.

## <a name="throughput-limits"></a>Teljesítmény korlátok

Az átviteli sebességhatár egy kötet a következő tényezőket kombinációja határozza meg:
* A kapacitás-készlet, amely a kötet tartozik a szolgáltatási szint
* A kötethez hozzárendelt kvóta  

A koncepciót az alábbi ábrán:

![Szolgáltatás szintű ábra](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

A fenti példa 1, a prémium szintű storage hozzárendelt kvóta 2 Tib-ra a kapacitás-készletből egy kötet hozzá lesz rendelve egy 128 MiB/s átviteli Sebességhatár (2 Tib-ra * 64 MiB/s). Ebben a forgatókönyvben a kapacitás a készlet méretét vagy a kötet tényleges fogyasztás függetlenül érvényes.

A fenti példában 2, a kötet kapacitása készletből a prémium szintű storage hozzárendelt kvóta 100 GB-hozzá lesz rendelve egy 6.25 MiB/s átviteli Sebességhatár (0.09765625 TiB * 64 MiB/s). Ebben a forgatókönyvben a kapacitás a készlet méretét vagy a kötet tényleges fogyasztás függetlenül érvényes.

## <a name="next-steps"></a>További lépések

- Tekintse meg a [Azure NetApp fájlok díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/storage/netapp/) az ár különböző szolgáltatási szintek
- Lásd: [Azure NetApp fájlok költségmodell](azure-netapp-files-cost-model.md) a kapacitás felhasználását, a kapacitás készlet kiszámítása 
- [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)