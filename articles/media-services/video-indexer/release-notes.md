---
title: Azure Media Services Video Indexer kibocsátási megjegyzései | Microsoft Docs
description: Ha naprakészen szeretne maradni a legújabb fejleményekkel, ez a cikk a Azure Media Services Video Indexer legújabb frissítéseit tartalmazza.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 07/22/2019
ms.author: juliako
ms.openlocfilehash: fd67e867b5eac58838551ac29e1fca5e860b15ff
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414210"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer kibocsátási megjegyzései

Naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

* A legújabb kiadásaihoz.
* Ismert problémák
* Hibajavítások
* Elavult funkciók

## <a name="june-2019"></a>Június 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer üzembe helyezése Kelet-Japánban

Mostantól létrehozhat egy Video Indexer fizetős fiókot a Kelet-japán régióban.

### <a name="create-and-repair-account-api-preview"></a>Account API létrehozása és javítása (előzetes verzió)

Új API hozzáadása, amely lehetővé teszi [Az Azure Media Service-kapcsolat végpontjának vagy kulcsának frissítését](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>A hibajavítások javítása a feltöltéskor 

A rendszer a mögöttes Azure Media Services fiók helytelen konfigurálása esetén leíró üzenetet ad vissza.

### <a name="player-timeline-keyframes-preview"></a>Játékos idősor-kulcsképek – előzetes verzió 

Most már láthat egy képelőnézett a lejátszó idővonalán.

### <a name="editor-semi-select"></a>Szerkesztő pontosvesszővel kiválasztva

Most már megtekintheti az összes olyan elemzést, amelyet a szerkesztőben megadott betekintési időkeret kiválasztásának eredményeképpen választott ki.

## <a name="may-2019"></a>2019. május

### <a name="update-custom-language-model-from-closed-caption-file"></a>Egyéni nyelvi modell frissítése a lezárt képaláírás-fájlból

[Egyéni nyelvi modell létrehozása](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) és az [Egyéni nyelvi modellek frissítése](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) mostantól támogatja az VTT, az SRT és a TTML fájlformátumokat a nyelvi modellek bemenetének megfelelően.

Az [Update video ÁTIRAT API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)meghívásakor a rendszer automatikusan hozzáadja az átiratot. A videóhoz társított betanítási modell is automatikusan frissül. A nyelvi modellek testreszabásával és betanításával kapcsolatos információkért lásd: [nyelvi modell testreszabása video Indexer](customize-language-model-overview.md)használatával.

### <a name="new-download-transcript-formats--txt-and-csv"></a>Új letöltési átirat formátuma – TXT és CSV

A már támogatott lezárt feliratozási formátum (SRT, VTT és TTML) mellett a Video Indexer mostantól támogatja az átirat TXT-és CSV-formátumokban való letöltését.

## <a name="next-steps"></a>További lépések

[Áttekintés](video-indexer-overview.md)
