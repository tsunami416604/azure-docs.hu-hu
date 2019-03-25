---
title: Az Azure Data Box Gateway korlátozza |} A Microsoft Docs
description: Ismerteti a rendszer korlátok és az ajánlott méreteket a Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e80b03f696a78887676e9f16750055a4dcfac230
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401571"
---
# <a name="azure-data-box-gateway-limits"></a>Az Azure Data Box-Gateway-korlátok

Vegye figyelembe ezeket a korlátokat, üzembe helyezése és működtetése megoldását a Microsoft Azure Data Box-Gateway. 


## <a name="data-box-gateway-service-limits"></a>Data Box Gateway szolgáltatási korlátozásai

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>A Data Box-átjáró eszköz korlátai

A következő táblázat ismerteti a korlátokat a Data Box átjáróeszköz számára.

| Leírás | Érték |
|---|---|
|Nem. a fájlok eszközönként |100 millió <br> Legfeljebb ~ minden 2 TB-nyi szabad lemezterület 100 millió a maximális korlátot a 25-ös millió fájl |
|Nem. a megosztások eszközönként |24 |
|Nem. egy Azure storage-tároló megosztások |1 |
|Maximális fájlméret megosztásra írt|2 TB-os virtuális eszköz a fájl maximális mérete 500 GB-os. <br> Maximális fájlméret egyenes arányban növekszik az adatlemez mérete az előző arány legfeljebb 5 TB-os eléréséig. |

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
