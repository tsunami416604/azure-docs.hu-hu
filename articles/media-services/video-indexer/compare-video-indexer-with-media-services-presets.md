---
title: A Video Indexer és a Azure Media Services v3-es készletek összehasonlítása | Microsoft Docs
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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 055e210efbb01268654e7823a0fc1320e9c27646
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892821"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Azure Media Services v3 alapkészletek és Video Indexer összehasonlítása 

Ez a cikk a **video Indexer API** -k és a **Media Services V3 API**-k képességeit hasonlítja össze. 

Jelenleg a [video Indexer API](https://api-portal.videoindexer.ai/) -k és a [Media Services V3 API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)-k által kínált funkciók átfedésben vannak. Az alábbi táblázat a különbségek és a hasonlóságok megismerésére szolgáló aktuális útmutatót tartalmazza. 

## <a name="compare"></a>Összehasonlítás

|Szolgáltatás|Video Indexer API-k |A video Analyzer és a audio Analyzer előzetes beállítása<br/>Media Services V3 API-k|
|---|---|---|
|Média-ismeretek|[Bővített](video-indexer-output-json-v2.md) |[Alapok](../latest/intelligence-concept.md)|
|Során|Tekintse meg a támogatott funkciók teljes listáját: <br/> [Áttekintés](video-indexer-overview.md)|Csak a video-bepillantást adja vissza|
|Számlázás|[Díjszabás Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Díjszabás Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Megfelelőség|[Iso 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SoC 1, 2, 3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci)és [HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) tanúsítvánnyal. A legfrissebb frissítésekért tekintse meg [video Indexer aktuális minősítési állapotát](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|A Media Services számos minősítésnek megfelel. Tekintse meg az [Azure megfelelőségi ajánlatait. PDF fájlt](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) , és keressen rá a "Media Services" kifejezésre, és ellenőrizze, hogy az megfelel-e egy fontos tanúsítványnak.|
|Free Trial (Ingyenes próba)|USA keleti régiója|Nincs|
|Régiónkénti elérhetőség|USA 2. keleti régiója, USA déli középső régiója, USA 2. nyugati régiója, Észak-Európa, Nyugat-Európa, Délkelet-Ázsia, Kelet-Ázsia és Kelet-Ausztrália.  A legújabb frissítésekért látogasson el a [termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) oldalára.|Lásd: az [Azure állapota](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Következő lépések

[A Video Indexer áttekintése](video-indexer-overview.md)

[Media Services v3 – áttekintés](../latest/media-services-overview.md)
