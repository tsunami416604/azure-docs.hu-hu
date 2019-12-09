---
title: Video Indexer fogalmak
titleSuffix: Azure Media Services
description: Ez a cikk a Azure Media Services Video Indexer szolgáltatás egyes fogalmait ismerteti.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 73dad1db4f44134f871c9f3d6e7edcdd3bd1e2ea
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900681"
---
# <a name="video-indexer-concepts"></a>Video Indexer fogalmak
 
Ez a cikk a Video Indexer szolgáltatás egyes fogalmait ismerteti.
    
## <a name="summarized-insights"></a>Összesített bepillantást

Az összesített elemzések az alábbi összesített nézetet tartalmazzák: arcok, témakörök, érzelmek. Például ahelyett, hogy a több ezer időtartományra, illetve annak ellenőrzésére, hogy mely arcok találhatók, az összesített megállapítások tartalmazzák az összes arcot és az egyes, a megjelenő időtartományokat és a megjelenített idő%-át.

## <a name="time-range-vs-adjusted-time-range"></a>időtartomány és a beállított időtartomány

A TimeRange az eredeti videó időtartománya. A AdjustedTimeRange az aktuális lejátszási listához viszonyított időtartomány. Mivel a különböző videók különböző soraiból is létrehozhat egy lejátszási listát, igénybe vehet egy 1 órás videót, és akár 1 sort is használhat, például 10:00-10:15. Ebben az esetben egy olyan lejátszási listával fog rendelkezni, amelyben az időtartomány 10:00-10:15, a adjustedTimeRange pedig a 00:00-00:15.
 
## <a name="blocks"></a>blokkok

A blokkok célja, hogy megkönnyítse az adattovábbítást. A blokkokat például aszerint lehet bontani, hogy mikor váltanak a beszélők, vagy hogy mikor tartanak hosszú szünetet.

## <a name="next-steps"></a>Következő lépések

További információ az első lépésekről: [regisztráció és az első videó feltöltése](video-indexer-get-started.md).

## <a name="see-also"></a>Lásd még:

[A Video Indexer áttekintése](video-indexer-overview.md)
