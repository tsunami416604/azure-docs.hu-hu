---
title: Azure Stack Edge-korlátok | Microsoft Docs
description: Ismerje meg a korlátokat és az ajánlott méreteket Azure Stack Edge üzembe helyezése és üzemeltetése során, beleértve a szolgáltatási korlátokat, az eszközök korlátait és a tárolási korlátokat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: e21f5a89d9f1f21eb99071a141794f99c07a8dfa
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079812"
---
# <a name="azure-stack-edge-limits"></a>Az Azure Stack Edge korlátai

Vegye figyelembe ezeket a korlátokat az Microsoft Azure Stack Edge-megoldás üzembe helyezése és üzemeltetése során. 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge-szolgáltatás korlátai

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-device-limits"></a>Azure Stack eszköz korlátai

Az alábbi táblázat az Azure Stack Edge-eszközre vonatkozó korlátozásokat ismerteti. 

| Leírás | Érték |
|---|---|
|Nem. fájl/eszköz |100 000 000 |
|Nem. a megosztások eszközönként |24 |
|Nem. megosztások száma tárolóban |1 |
|Megosztásba írt fájlok maximális mérete| 5 TB |

## <a name="azure-storage-limits"></a>Azure Storage-korlátok

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Az adatfeltöltés kikötései

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Az Azure Storage-fiók mérete és az objektum méretére vonatkozó korlátok

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure-objektum méretének korlátai

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Következő lépések

- [Felkészülés az Azure Stack Edge üzembe helyezésére](azure-stack-edge-deploy-prep.md)
