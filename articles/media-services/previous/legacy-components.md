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
ms.date: 09/26/2019
ms.author: juliako
ms.openlocfilehash: e4ff157f58a68c68f8610c6c473f5d69897650ad
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338418"
---
# <a name="azure-media-services-legacy-components"></a>Örökölt összetevők Azure Media Services

Az idő múlásával a Media Service-összetevők folyamatos fejlesztéseket és fejlesztéseket eredményeztek. Ennek eredményeképpen egyes örökölt összetevők kivonultak. A következő cikkekben talál útmutatást arról, hogyan migrálhatja az alkalmazást az örökölt összetevőből egy aktuális összetevőbe.

## <a name="legacy-components-and-migration-guidance"></a>Örökölt összetevők és áttelepítési útmutató

Bejelentjük a *Windows Azure Media Encoder* (Tamás) és a *Azure Media Encoder* (ame) adathordozó-processzorok elavulása. Ezek a processzorok 2019 november 30-án lesznek kivonva.

* [Áttelepítés Windows Azure Media Encoderról Media Encoder Standardre](migrate-windows-azure-media-encoder.md)
* [Áttelepítés Azure Media Encoderról Media Encoder Standard](migrate-azure-media-encoder.md)

A *Azure Media Indexer v1* és a *Azure Media Indexer v2 előzetes*verziójának elavulása is bejelentve. A [Azure Media Indexer v1](media-services-index-content.md) adathordozó-processzor a 2020-es október 1-én megszűnik. A (z) [[Azure Media Indexer v2 előzetes verziójú](media-services-process-content-with-indexer2.md) adathordozó-processzorok 2019 január 1-jén lesznek kivonva.  A [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) ezeket a régi adathordozó-processzorokat váltja fel.

* [Migrálás Azure Media Indexer v1 és Azure Media Indexer v2 rendszerről Azure Media Services video Indexerra](migrate-indexer-v1-v2.md).

## <a name="next-steps"></a>További lépések

[Áttelepítési útmutató Media Services v2-ről v3-re való áthelyezéshez](../latest/migrate-from-v2-to-v3.md)
