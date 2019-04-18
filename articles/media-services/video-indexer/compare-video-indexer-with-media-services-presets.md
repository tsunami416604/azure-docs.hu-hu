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
ms.date: 04/07/2019
ms.author: juliako
ms.openlocfilehash: 2c98f6d12f4868e5f90874fe3210fe5368f7ca2d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270336"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Hasonlítsa össze az Azure Media Services v3 készletek és a Video Indexer 

Ez a cikk összehasonlítja a képességeit **Video Indexer API-k** és **Media Services v3 API-k**. 

Jelenleg nincs által nyújtott szolgáltatások között átfedés a [Video Indexer API-k](https://api-portal.videoindexer.ai/) és a [Media Services v3 API-k](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). Az alábbi táblázat a különbségeket és hasonlóságokat megértéséhez aktuális arról kínál. 

## <a name="compare"></a>Összehasonlítás

|Szolgáltatás|A video Indexer API-k |Videó Analyzer és hang Analyzer készletek<br/>a Media Services v3 API-k|
|---|---|---|
|Media Insights|[Továbbfejlesztett](video-indexer-output-json-v2.md) |[Alapok](../latest/intelligence-concept.md)|
|Élményeket|Támogatott szolgáltatások teljes listáját lásd: <br/> [Áttekintés](video-indexer-overview.md)|Csak a videó insights adja vissza|
|Számlázás|[A Media Services-díjszabás](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[A Media Services-díjszabás](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Megfelelőség|[Az ISO 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC 1,2,3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci), és [ HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) certified. Látogasson el a legújabb frissítésekkel [minősítések aktuális állapotát a Video Indexer](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|A Media Services számos tanúsítványok megfeleljenek. Tekintse meg [Azure megfelelőségi Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) , és keressen a "Media Services", ha megfelel a lényeges tanúsítvány megtekintéséhez.|
|Free Trial (Ingyenes próba)|USA keleti régiója|Nem érhető el|
|Régiónkénti elérhetőség|USA keleti RÉGIÓJA 2, USA déli középső RÉGIÓJA, 2. nyugati RÉGIÓJA, Észak-Európa, Nyugat-Európa, Délkelet-Ázsia, Kelet-Ázsia és Kelet-Ausztrália.  A legújabb frissítésekkel, látogasson el a [termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) lapot.|Lásd: [az Azure állapota](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>További lépések

[A Video Indexer áttekintése](video-indexer-overview.md)

[Media Services v3 áttekintése](../latest/media-services-overview.md)
