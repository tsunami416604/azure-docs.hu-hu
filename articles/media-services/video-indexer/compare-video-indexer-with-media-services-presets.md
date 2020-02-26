---
title: A Video Indexer és a Azure Media Services v3-es előkészletek összehasonlítása
description: Ez a cikk a Video Indexer képességeket és a Azure Media Services v3-es előkészleteket hasonlítja össze.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: dcfc6ea4afe23424e72c625518356be52f62bc81
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77602189"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Azure Media Services v3 alapkészletek és Video Indexer összehasonlítása 

Ez a cikk a **video Indexer API** -k és a **Media Services V3 API**-k képességeit hasonlítja össze. 

Jelenleg a [video Indexer API](https://api-portal.videoindexer.ai/) -k és a [Media Services V3 API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)-k által kínált funkciók átfedésben vannak. Az alábbi táblázat a különbségek és a hasonlóságok megismerésére szolgáló aktuális útmutatót tartalmazza. 

## <a name="compare"></a>Összehasonlítás

|Funkció|Video Indexer API-k |A video Analyzer és a audio Analyzer előzetes beállítása<br/>Media Services V3 API-k|
|---|---|---|
|Média-ismeretek|[Bővített](video-indexer-output-json-v2.md) |[Alapok](../latest/intelligence-concept.md)|
|Során|Tekintse meg a támogatott funkciók teljes listáját: <br/> [Áttekintés](video-indexer-overview.md)|Csak a video-bepillantást adja vissza|
|Számlázás|[Díjszabás Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Díjszabás Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Megfelelőség|A legújabb megfelelőségi frissítésekhez tekintse meg az [Azure megfelelőségi ajánlatait. PDF fájlt](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) , és keressen rá a "video Indexer" kifejezésre, és ellenőrizze, hogy az megfelel-e egy fontos tanúsítványnak.|A legújabb megfelelőségi frissítésekhez tekintse meg az [Azure megfelelőségi ajánlatait. PDF fájlt](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) , és keressen rá a "Media Services" kifejezésre, és ellenőrizze, hogy az megfelel-e egy fontos tanúsítványnak.|
|Ingyenes próbaverzió|USA keleti régiója|Nem érhető el|
|Régiónkénti elérhetőség|Tekintse meg [a Cognitive Services elérhetőségét régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Tekintse meg [a Media Services elérhetőségét régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Következő lépések

[A Video Indexer áttekintése](video-indexer-overview.md)

[Media Services v3 – áttekintés](../latest/media-services-overview.md)
