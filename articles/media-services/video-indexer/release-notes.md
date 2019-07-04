---
title: Az Azure Media Services Video Indexer kibocsátási megjegyzései |} A Microsoft Docs
description: A legújabb fejlemények az naprakész információkat, ez a cikk az Azure Media Services Video Indexer a legújabb frissítésekkel.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: f1c5f43316292f17547b84d970a0f03a1a2c366f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454021"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Az Azure Media Services Video Indexer kibocsátási megjegyzései

Naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

* A legújabb kiadásaihoz.
* Ismert problémák
* Hibajavítások
* Elavult funkciók

## <a name="june-2019"></a>Június 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer – kelet-japán központi telepítése

Mostantól létrehozhat egy fiókot a kelet-japán régióban fizetős Video Indexer.

### <a name="create-and-repair-account-api-preview"></a>Hozzon létre, és javítsa ki a fiók API (előzetes verzió)

Egy új API-t, amely lehetővé teszi, hogy hozzáadott [frissítése az Azure Media Services-kapcsolat végpontjának vagy kulcs](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>A feltöltés hibakezelési javítása 

Hibás az alapul szolgáló Azure Media Services-fiók esetén leíró üzenetet adja vissza.

### <a name="player-timeline-keyframes-preview"></a>Player ütemterv kulcsképek előzetes verzió 

Most láthatja minden alkalommal, amikor egy lemezkép előzetes verziója a Windows Media player eseményeket egy idővonalon megjelenítve.

### <a name="editor-semi-select"></a>Félvezérelt kijelölési szerkesztő

Most már megjelenik előzetes verziója, amely ki van jelölve, egy adott insight időkeret kiválasztása a szerkesztőben eredményeként az elemzés.

## <a name="may-2019"></a>2019. május

### <a name="update-custom-language-model-from-closed-caption-file"></a>A feliratfájl egyéni nyelvi modell frissítése

[Testreszabott nyelvi modell létrehozásához](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) és [testreszabott nyelvi modell frissítése](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) API mostantól támogatja a VTT, SRT, és TTML fájlformátumok nyelvi modellek bemenetként.

Hívásakor a [frissítés videó szöveges API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), a rendszer automatikusan hozzáadja a szövegben. A tanítási modell a videó társított is automatikusan frissül. Testre szabhatja, és a nyelvi modelleket taníthat be a vonatkozó információkért lásd: [Video Indexer nyelvi modell testreszabása](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Új letöltési szöveges formátumban – TXT és a fürt megosztott kötetei szolgáltatás

A lezárt feliratozási formátuma már támogatott (SRT VTT és TTML), valamint a Video Indexer mostantól támogatja az átiratok a TXT-és a fürt megosztott kötetei szolgáltatás letöltése.

## <a name="next-steps"></a>További lépések

[Áttekintés](video-indexer-overview.md)
