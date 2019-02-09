---
title: Az Azure Data Box Edge korlátozza |} A Microsoft Docs
description: A Microsoft Azure Data Box Edge ismerteti a rendszer korlátok és az ajánlott méreteket.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: 30e0c37d3d0c03e77b6dab9c06c0a50bff27e8bc
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967300"
---
# <a name="azure-data-box-edge-limits-preview"></a>Az Azure Data Box Edge korlátozza (előzetes verzió)

Vegye figyelembe ezeket a korlátokat, üzembe helyezése és a Microsoft Azure Data Box peremhálózati megoldás üzemeltetése.

> [!IMPORTANT]
> A Data Box Edge előzetes verzióban érhető el. A megoldás üzembe helyezése előtt tekintse át [az előzetes verziókra vonatkozó szolgáltatási feltételeket](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="data-box-edge-service-limits"></a>A Data Box Edge service korlátai

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>A Data Box peremhálózati eszköz korlátai

A következő táblázat ismerteti a korlátokat a Data Box Edge-eszköz.

| Leírás | Érték |
|---|---|
|Nem. a fájlok eszközönként |100 millió |
|Nem. a megosztások eszközönként |24 |
|Nem. a megosztások tárolónként |1 |
|Maximális fájlméret megosztásra írt| 5 TB |

## <a name="azure-storage-limits"></a>Az Azure storage-korlátok

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Adatok feltöltése a figyelmeztetések

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Az Azure storage-fiók mérete és az objektum méretkorlátozások

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Az Azure objektum blobméretének korlátjai

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>További lépések

- [Az Azure Data Box Gateway üzembe helyezésének előkészítése](data-box-gateway-deploy-prep.md)
