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
ms.date: 11/22/2019
ms.author: juliako
ms.openlocfilehash: bbf3ba17e7f91e88136f2dac50c908262100e77a
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048779"
---
# <a name="azure-media-services-legacy-components"></a>Örökölt összetevők Azure Media Services

Az idő múlásával a Media Service-összetevők folyamatos fejlesztéseket és fejlesztéseket eredményeztek. Ennek eredményeképpen egyes örökölt összetevők kivonultak. A következő cikkekben talál útmutatást arról, hogyan migrálhatja az alkalmazást az örökölt összetevőből egy aktuális összetevőbe.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>A régi összetevők és az áttelepítési útmutató nyugdíjazási tervei

Bejelentjük a *Windows Azure Media Encoder* (Tamás) és a *Azure Media Encoder* (ame) adathordozó-processzorok elavulása. Ezek a processzorok 2020 március 31-ig lesznek kivonva.

* [Áttelepítés Windows Azure Media Encoderról Media Encoder Standardre](migrate-windows-azure-media-encoder.md)
* [Áttelepítés Azure Media Encoderról Media Encoder Standard](migrate-azure-media-encoder.md)

A következő Media Analytics adathordozó-processzorok kivonulását is bejelentjük: 
 
|Médiafeldolgozó neve|Nyugdíjazás dátuma|További megjegyzések|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| Október 1-től 2020|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/)váltja fel. További információ: [áttelepítés Azure Media Indexer 2 rendszerről Azure Media Services video Indexerre](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|Október 1-től 2020|Ezt a médialejátszó-processzort [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/)váltja fel. További információ: [áttelepítés Azure Media Indexerról Azure Media Servicesra video Indexer](migrate-indexer-v1-v2.md)

## <a name="next-steps"></a>Következő lépések

[Áttelepítési útmutató Media Services v2-ről v3-re való áthelyezéshez](../latest/migrate-from-v2-to-v3.md)
