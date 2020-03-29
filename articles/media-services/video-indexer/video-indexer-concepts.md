---
title: Videoindexelő fogalmak
titleSuffix: Azure Media Services
description: Ez a cikk az Azure Media Services videoindexelő szolgáltatásának néhány fogalmait ismerteti.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 73dad1db4f44134f871c9f3d6e7edcdd3bd1e2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900681"
---
# <a name="video-indexer-concepts"></a>Videoindexelő fogalmak
 
Ez a cikk a Video Indexer szolgáltatás néhány fogalomát ismerteti.
    
## <a name="summarized-insights"></a>Összefoglaló elemzések

Az összesített elemzések az adatok összesített nézetét tartalmazzák: arcok, témák, érzelmek. Például ahelyett, hogy végighaladna a több ezer időtartományon, és ellenőrizné, hogy mely arcok vannak benne, az összesített elemzések tartalmazzák az összes arcot, és mindegyikhez az időtartományokat, amelyekben megjelenik, és az idő %- át.

## <a name="time-range-vs-adjusted-time-range"></a>időtartomány és beállított időtartomány

TimeRange az eredeti videó időtartománya. AdjustedTimeRange az aktuális lejátszási listához viszonyított időtartomány. Mivel különböző videók különböző soraiból hozhat létre lejátszási listát, készíthet egy 1 órás videót, és csak 1 sort használhat belőle, például 10:00-10:15. Ebben az esetben egy 1 soros lejátszási lista lesz, ahol az időtartomány 10:00-10:15, de a módosítottTimeRange 00:00-00:15.
 
## <a name="blocks"></a>Blokkok

A blokkok célja, hogy megkönnyítsék az adatok átnézését. A blokkokat például aszerint lehet bontani, hogy mikor váltanak a beszélők, vagy hogy mikor tartanak hosszú szünetet.

## <a name="next-steps"></a>További lépések

Az első lépésekről a [Regisztráció és az első videó feltöltése](video-indexer-get-started.md)című témakörben talál további információt.

## <a name="see-also"></a>Lásd még

[A Video Indexer áttekintése](video-indexer-overview.md)
