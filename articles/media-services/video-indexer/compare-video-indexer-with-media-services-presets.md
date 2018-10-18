---
title: A Video Indexer és az Azure Media Services v3 készletek összehasonlítása |} A Microsoft Docs
description: Ez a témakör a Video Indexer, és az Azure Media Services v3 készletek hasonlítja össze.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: juliako
ms.openlocfilehash: e9b66721135456320134f41c614b194d1e22b10f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49381034"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Hasonlítsa össze az Azure Media Services v3 készletek és a Video Indexer 

Ez a cikk összehasonlítja a képességeit **Video Indexer API-k** és **Media Services v3 API-k**. 

Jelenleg nincs által nyújtott szolgáltatások között átfedés a [Video Indexer v2 API-k](https://api-portal.videoindexer.ai/) és a [Media Services v3 API-k](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). Az alábbi táblázat a különbségeket és hasonlóságokat megértéséhez aktuális arról kínál. 

## <a name="compare"></a>Összehasonlítás

|Szolgáltatás|A video Indexer API-k |Videó Analyzer és hang Analyzer készletek<br/>a Media Services v3 API-k|
|---|---|---|
|Media Insights|[Továbbfejlesztett](../../cognitive-services/video-indexer/video-indexer-output-json-v2.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json) |[Alapok](../latest/intelligence-concept.md)|
|Élményeket|Támogatott szolgáltatások teljes listáját lásd: <br/> [Áttekintés](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)|Csak a videó insights adja vissza|
|Számlázás|[A Media Services-díjszabás](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[A Media Services-díjszabás](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Megfelelőség|TBD|A Media Services számos tanúsítványok megfeleljenek. Tekintse meg [Azure megfelelőségi Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) , és keressen a "Media Services", ha megfelel a lényeges tanúsítvány megtekintéséhez.|
|Free Trial (Ingyenes próba)|USA keleti régiója|Nem érhető el|
|Rendelkezésre állás |USA nyugati RÉGIÓJA, Kelet-Ázsiában, Észak-Európa|Lásd: [az Azure állapota](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>További lépések

[A Video Indexer áttekintése](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)

[Media Services v3 áttekintése](../../media-services/latest/media-services-overview.md)
