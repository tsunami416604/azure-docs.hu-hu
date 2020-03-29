---
title: Az Azure Media Services örökölt összetevői | Microsoft dokumentumok
description: Ez a témakör az Azure Media Services örökölt összetevőit ismerteti.
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
ms.openlocfilehash: 94a70a1234d902787f248890f0cb538a4ba9c2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921079"
---
# <a name="azure-media-services-legacy-components"></a>Az Azure Media Services örökölt összetevői

Az idő múlásával a Media Service összetevői folyamatosan javultak és továbbfejlődtek. Ennek eredményeképpen néhány örökölt összetevő kilett vonva. Az alkalmazás nak az örökölt összetevőből az aktuális összetevőbe történő áttelepítésére vonatkozó utasításokat az alábbi cikkekben találja.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Az örökölt összetevők nyugdíjazási tervei és a migrációs útmutató

A *Windows Azure Media Encoder* (WAME) és az *Azure Media Encoder* (AME) médiaprocesszorok epcét jelentik be. Ezeket a processzorokat 2020. március 31-én vonják ki.

* [Áttelepítés a Windows Azure Media Kódolóról a Media Encoder Standard szolgáltatásra](migrate-windows-azure-media-encoder.md)
* [Áttelepítés az Azure Media Kódolóról a Media Encoder Standard szolgáltatásra](migrate-azure-media-encoder.md)

A Media Analytics médiaprocesszorainak nyugdíjazását is bejelentjük: 
 
|Médiafeldolgozó neve|Nyugdíjazás dátuma|További megjegyzések|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|2020. január 1.|Ezt a médiaprocesszort az [Azure Media Services videoindexelője](https://docs.microsoft.com/azure/media-services/video-indexer/)váltja fel. További információ: [Migrate from Azure Media Indexer 2 to Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|2023. március 1.|Ezt a médiaprocesszort az [Azure Media Services videoindexelője](https://docs.microsoft.com/azure/media-services/video-indexer/)váltja fel. További információ: Áttelepítés az [Azure Media Indexelőről az Azure Media Services videoindexelőjébe.](migrate-indexer-v1-v2.md)|
|[Mozgásérzékelés](media-services-motion-detection.md)|2020. június 1.|Jelenleg nincs csereterv.|
|[Videó összegzése](media-services-video-summarization.md)|2020. június 1.|Jelenleg nincs csereterv.|
|[Videó optikai karakterfelismerés](media-services-video-optical-character-recognition.md)|2020. június 1.|Ezt a médiaprocesszort az [Azure Media Services videoindexelője](https://docs.microsoft.com/azure/media-services/video-indexer/)váltja fel. Fontolja meg az [Azure Media Services v3 API használatát](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)is. <br/>Lásd: [Az Azure Media Services v3-készletek és a Video Indexelő összehasonlítása](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Arcérzékelő](media-services-face-and-emotion-detection.md)|2020. június 1.|Ezt a médiaprocesszort az [Azure Media Services videoindexelője](https://docs.microsoft.com/azure/media-services/video-indexer/)váltja fel. Fontolja meg az [Azure Media Services v3 API használatát](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)is. <br/>Lásd: [Az Azure Media Services v3-készletek és a Video Indexelő összehasonlítása](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Tartalommoderátor](media-services-content-moderation.md)|2020. június 1.|Ezt a médiaprocesszort az [Azure Media Services videoindexelője](https://docs.microsoft.com/azure/media-services/video-indexer/)váltja fel. Fontolja meg az [Azure Media Services v3 API használatát](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)is. <br/>Lásd: [Az Azure Media Services v3-készletek és a Video Indexelő összehasonlítása](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>További lépések

[Áttelepítési útmutató a Media Services 2-es v3-as ról a 3-as vagy a 3-as](../latest/migrate-from-v2-to-v3.md)
