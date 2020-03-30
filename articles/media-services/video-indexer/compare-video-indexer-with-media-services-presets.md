---
title: A Video Indexelő és az Azure Media Services v3-készletek összehasonlítása
description: Ez a cikk összehasonlítja a Video Indexer képességeit és az Azure Media Services v3-készleteket.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602189"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Az Azure Media Services v3-készletek és a Video Indexelő összehasonlítása 

Ez a cikk a **Video Indexer API-k** és a **Media Services 3-as számú API-k**képességeit hasonlítja össze. 

Jelenleg átfedés van a [Video Indexer API-k](https://api-portal.videoindexer.ai/) és a [Media Services v3 API-k](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)által kínált funkciók között. Az alábbi táblázat a különbségek és hasonlóságok megértéséhez aktuális iránymutatást ad. 

## <a name="compare"></a>Összehasonlítás

|Szolgáltatás|Videoindexelő API-k |Videoanalizátor és hangelemző készletek<br/>a Media Services 3-as vAPI-iban|
|---|---|---|
|Média elemzési adatok|[Továbbfejlesztett](video-indexer-output-json-v2.md) |[Alapok](../latest/intelligence-concept.md)|
|Tapasztalatok|Tekintse meg a támogatott szolgáltatások teljes listáját: <br/> [Áttekintés](video-indexer-overview.md)|Csak videóelemzéseket ad vissza|
|Számlázás|[A Media Services díjszabása](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[A Media Services díjszabása](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Megfelelőség|A legfrissebb megfelelőségi frissítésekért keresse fel az [Azure Compliance Offerings.pdf fájlt,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) és keressen rá a "Video Indexer" kifejezésre, és nézze meg, hogy megfelel-e az érdeklődési tanúsítványnak.|A legfrissebb megfelelőségi frissítésekért keresse fel az [Azure Compliance Offerings.pdf fájlt,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) és keressen rá a "Media Services" kifejezésre, és nézze meg, hogy az megfelel-e az érdeklődési tanúsítványnak.|
|Ingyenes próbaverzió|USA keleti régiója|Nem érhető el|
|Régiónkénti elérhetőség|Lásd: [Cognitive Services elérhetősége régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Lásd: [Media Services elérhetősége régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>További lépések

[A Video Indexer áttekintése](video-indexer-overview.md)

[A Media Services 3-as v3-as ügyének áttekintése](../latest/media-services-overview.md)
