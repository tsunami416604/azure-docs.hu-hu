---
title: Örökölt összetevők Azure Media Services | Microsoft Docs
description: Ez a témakör Azure Media Services örökölt összetevőket ismerteti.
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
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 1c3e50fd64c113aafd2d70cc76c7f789e080ad23
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269522"
---
# <a name="azure-media-services-legacy-components"></a>Örökölt összetevők Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Az idő múlásával a Media Service-összetevők folyamatos fejlesztéseket és fejlesztéseket eredményeztek. Ennek eredményeképpen egyes örökölt összetevők kivonultak. A következő cikkekben talál útmutatást arról, hogyan migrálhatja az alkalmazást az örökölt összetevőből egy aktuális összetevőbe.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>A régi összetevők és az áttelepítési útmutató nyugdíjazási tervei

Bejelentjük a *Windows Azure Media Encoder* (Tamás) és a *Azure Media Encoder* (ame) adathordozó-processzorok elavulása. Ezek a processzorok 2020 március 31-ig lesznek kivonva.

* [Áttelepítés Windows Azure Media Encoderról Media Encoder Standardre](migrate-windows-azure-media-encoder.md)
* [Áttelepítés Azure Media Encoderról Media Encoder Standard](migrate-azure-media-encoder.md)

A következő Media Analytics adathordozó-processzorok kivonulását is bejelentjük: 
 
|Médiafeldolgozó neve|Nyugdíjazás dátuma|További megjegyzések|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|2020. január 1.|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](../video-indexer/index.yml)váltja fel. További információ: [áttelepítés Azure Media Indexer 2 rendszerről Azure Media Services video Indexerre](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|2023. március 1.|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](../video-indexer/index.yml)váltja fel. További információ: [áttelepítés Azure Media Indexerról Azure Media Servicesra video Indexer](migrate-indexer-v1-v2.md)|
|[Mozgás észlelése](media-services-motion-detection.md)|2020. június 1.|Jelenleg nincsenek helyettesítési csomagok.|
|[Videók összegzése](media-services-video-summarization.md)|2020. június 1.|Jelenleg nincsenek helyettesítési csomagok.|
|[Videós optikai karakterfelismerés](media-services-video-optical-character-recognition.md)|2020. június 1.|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](../video-indexer/index.yml)váltja fel. Emellett érdemes lehet [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md)-t használni. <br/>Lásd: [Azure Media Services v3-es előállítók összehasonlítása és video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|
|[Arcérzékelő](media-services-face-and-emotion-detection.md)|2020. június 1.|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](../video-indexer/index.yml)váltja fel. Emellett érdemes lehet [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md)-t használni. <br/>Lásd: [Azure Media Services v3-es előállítók összehasonlítása és video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|
|[Content Moderator](media-services-content-moderation.md)|2020. június 1.|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](../video-indexer/index.yml)váltja fel. Emellett érdemes lehet [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md)-t használni. <br/>Lásd: [Azure Media Services v3-es előállítók összehasonlítása és video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|

## <a name="next-steps"></a>További lépések

[Áttelepítési útmutató Media Services v2-ről v3-re való áthelyezéshez](../latest/migrate-from-v2-to-v3.md)
