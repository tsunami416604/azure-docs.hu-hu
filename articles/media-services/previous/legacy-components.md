---
title: Örökölt összetevők Azure Media Services | Microsoft Docs
description: Ez a témakör Azure Media Services örökölt összetevőket ismerteti.
services: media-services
documentationcenter: ''
author: myoungerman
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2020
ms.author: v-myoung
ms.openlocfilehash: d5a10ccc887124b2bc5595d66baad833a32275cc
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90600891"
---
# <a name="azure-media-services-legacy-components"></a>Örökölt összetevők Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Az idő múlásával növeljük a Media Service-összetevőket, és kivonják a régi összetevőket. Ebből a cikkből megtudhatja, hogyan telepítheti át alkalmazását egy örökölt összetevőből egy aktuális összetevőbe.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>A régi összetevők és az áttelepítési útmutató nyugdíjazási tervei

A *Windows Azure Media Encoder* (Tamás) és a *Azure Media Encoder* (ame) adathordozó-processzor elavult.

* [Áttelepítés Windows Azure Media Encoderról Media Encoder Standardre](migrate-windows-azure-media-encoder.md)
* [Áttelepítés Azure Media Encoderról Media Encoder Standard](migrate-azure-media-encoder.md)

A következő Media Analytics adathordozó-processzorok elavultak, vagy hamarosan elavulttá válnak:  
 
|Médiafeldolgozó neve|Nyugdíjazás dátuma|További megjegyzések|
|---|---|
|Azure Media Indexer 2|2020. január 1.|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](../video-indexer/index.yml)váltotta fel. További információ: [áttelepítés Azure Media Indexer 2 rendszerről Azure Media Services video Indexerre](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|2023. március 1.|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](../video-indexer/index.yml)váltja fel. További információ: [áttelepítés Azure Media Indexerról Azure Media Services video Indexerre](migrate-indexer-v1-v2.md).|
|Mozgás észlelése|2020. június 1.|Jelenleg nincsenek helyettesítési csomagok.|
|Videó-összefoglalás] (Media-Services-video-summarization.md|2020. június 1.|Jelenleg nincsenek helyettesítési csomagok.|
|Videós optikai karakterfelismerés|2020. június 1.|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](../video-indexer/index.yml)váltotta fel. Emellett érdemes lehet [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md)-t használni. <br/>Lásd: [Azure Media Services v3-es előállítók és video Indexer összehasonlítása](../video-indexer/compare-video-indexer-with-media-services-presets.md).|
|Arcérzékelő|2020. június 1.|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](../video-indexer/index.yml)váltotta fel. Emellett érdemes lehet [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md)-t használni. <br/>Lásd: [Azure Media Services v3-es előállítók és video Indexer összehasonlítása](../video-indexer/compare-video-indexer-with-media-services-presets.md).|
|Tartalommoderátor|2020. június 1.|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](../video-indexer/index.yml)váltotta fel. Emellett érdemes lehet [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md)-t használni. <br/>Lásd: [Azure Media Services v3-es előállítók és video Indexer összehasonlítása](../video-indexer/compare-video-indexer-with-media-services-presets.md).|

## <a name="next-steps"></a>További lépések

[Áttelepítési útmutató Media Services v2-ről v3-re való áthelyezéshez](../latest/migrate-from-v2-to-v3.md)
