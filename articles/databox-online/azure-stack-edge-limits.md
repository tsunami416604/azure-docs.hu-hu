---
title: Azure Stack Edge Pro-korlátok | Microsoft Docs
description: Ismerje meg a korlátokat és az ajánlott méreteket a Azure Stack Edge Pro üzembe helyezése és üzemeltetése során, beleértve a szolgáltatási korlátokat, az eszközök korlátait és a tárolási korlátokat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 5f3c39ce7334145d3ffc1d54badb1f7b766da70f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904452"
---
# <a name="azure-stack-edge-pro-limits"></a>Az Azure Stack Edge Pro korlátai

Ezeket a korlátokat a Microsoft Azure Stack Edge Pro-megoldás üzembe helyezése és üzemeltetése során érdemes figyelembe venni. 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge-szolgáltatás korlátai

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Azure Stack Edge-eszközök korlátai

Az alábbi táblázat az Azure Stack Edge Pro-eszköz korlátozásait ismerteti. 

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

- [Felkészülés a Azure Stack Edge Pro üzembe helyezésére](azure-stack-edge-deploy-prep.md)
