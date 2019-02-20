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
ms.topic: concepts
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: a10ac319fe362011531e00f832bb8e471fd56fdf
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430898"
---
# <a name="service-levels-for-azure-netapp-files"></a>Szolgáltatási szintek a NetApp Azure-fájlok
NetApp Azure Files szolgáltatást két szinten támogatja: Prémium és Standard. 

## <a name="Premium"></a>A Premium storage

A *prémium* storage akár 64 Tib-ra a kapacitásegységek száma MiB/s biztosít. Átviteli teljesítmény kötet kvótába indexelve van. Például egy kötetet a kiépített kvóta (függetlenül a tényleges fogyasztás) 2 Tib-ra a prémium szintű storage-ból, egy 128 MiB/s adatforgalomból.

## <a name="Standard"></a>Standard szintű storage

A *Standard* storage akár 16 MiB/s átviteli sebesség TiB kiszolgálónként biztosít. Átviteli teljesítmény kötet kvótába indexelve van. Például egy kötetet, a Standard Storage, az üzembe helyezett kvóta (függetlenül a tényleges fogyasztás) 2 Tib-ra, egy 32 MiB/s adatforgalomból.

## <a name="next-steps"></a>További lépések

- Tekintse meg a [Azure NetApp fájlok díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/storage/netapp/) az ár különböző szolgáltatási szintek
- [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
