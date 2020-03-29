---
title: Az Azure Data Box Gateway korlátai | Microsoft dokumentumok
description: A cikk a Microsoft Azure Data Box Gateway rendszerkorlátait és ajánlott méreteit ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e80b03f696a78887676e9f16750055a4dcfac230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60755234"
---
# <a name="azure-data-box-gateway-limits"></a>Az Azure Data Box gateway korlátai

Vegye figyelembe ezeket a korlátokat a Microsoft Azure Data Box Gateway megoldás üzembe helyezéseés működtetése során. 


## <a name="data-box-gateway-service-limits"></a>Data Box Gateway szolgáltatás korlátai

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>A Data Box Gateway eszközkorlátai

Az alábbi táblázat a Data Box Gateway eszköz korlátait ismerteti.

| Leírás | Érték |
|---|---|
|Nem. fájlok eszközenként |100 millió <br> Limit ~ 25 millió kép minden 2 TB-os lemezterület maximális határ 100 millió |
|Nem. megosztások eszközönként |24 |
|Nem. megosztások Azure-tárolónként |1 |
|Megosztásra írt fájlméret maximális mérete|2 TB-os virtuális eszköz esetén a maximális fájlméret 500 GB. <br> A maximális fájlméret az előző arányadatlemez-méretével növekszik, amíg el nem éri a legfeljebb 5 TB-ot. |

## <a name="azure-storage-limits"></a>Az Azure tárolási korlátai

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Az adatfeltöltés kikötései

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Az Azure storage-fiók méretére és az objektumméretre vonatkozó korlátok

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Az Azure-objektumok méretkorlátai

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>További lépések

- [Az Azure Data Box Gateway üzembe helyezésének előkészítése](data-box-gateway-deploy-prep.md)
