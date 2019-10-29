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
ms.date: 10/27/2019
ms.author: juliako
ms.openlocfilehash: 30e8fa51df42d202d77ecdbc6a31fe3c7aaac6f4
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968624"
---
# <a name="azure-media-services-legacy-components"></a>Örökölt összetevők Azure Media Services

Az idő múlásával a Media Service-összetevők folyamatos fejlesztéseket és fejlesztéseket eredményeztek. Ennek eredményeképpen egyes örökölt összetevők kivonultak. A következő cikkekben talál útmutatást arról, hogyan migrálhatja az alkalmazást az örökölt összetevőből egy aktuális összetevőbe.

## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>A régi összetevők és az áttelepítési útmutató nyugdíjazási tervei

Bejelentjük a *Windows Azure Media Encoder* (Tamás) és a *Azure Media Encoder* (ame) adathordozó-processzorok elavulása. Ezek a processzorok 2019 november 30-án lesznek kivonva.

* [Áttelepítés Windows Azure Media Encoderról Media Encoder Standardre](migrate-windows-azure-media-encoder.md)
* [Áttelepítés Azure Media Encoderról Media Encoder Standard](migrate-azure-media-encoder.md)

A következő Media Analytics adathordozó-processzorok kivonulását is bejelentjük: 

|Médiafeldolgozó neve|Nyugdíjazás dátuma|További megjegyzések|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 2020. január 1.|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/)váltja fel. További információ: [áttelepítés Azure Media Indexer 2 rendszerről Azure Media Services video Indexerre](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|Október 1-től 2020|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/)váltja fel. További információ: [áttelepítés Azure Media Indexerról Azure Media Servicesra video Indexer](migrate-indexer-v1-v2.md)
|[Azure Media Face Detector](media-services-face-and-emotion-detection.md)|Február 1., 2020|Ez a Media Analytics előnézeti processzor kivonásra kerül, és a rendszer nem helyezi át az általánosan elérhetővé. Kiértékeljük a forgatókönyveit és használati eseteit az ügyfelekkel a jövőbeli befektetésekhez.|
|[Azure Media Motion Detector](media-services-motion-detection.md)|Február 1., 2020|Ez a Media Analytics előnézeti processzor kivonásra kerül, és a rendszer nem helyezi át az általánosan elérhetővé. Kiértékeljük a forgatókönyveit és használati eseteit az ügyfelekkel a jövőbeli befektetésekhez.|
|[Azure Media OCR](media-services-video-optical-character-recognition.md)|Február 1., 2020|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) és az [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md)-ra vonatkozó beállításkészlet váltja fel.|
|[Azure Media Video Thumbnails](media-services-video-summarization.md)|Február 1., 2020|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) és az [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md)-ra vonatkozó beállításkészlet váltja fel.|

## <a name="next-steps"></a>Következő lépések

[Áttelepítési útmutató Media Services v2-ről v3-re való áthelyezéshez](../latest/migrate-from-v2-to-v3.md)
