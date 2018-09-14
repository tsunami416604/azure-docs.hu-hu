---
title: Az Azure Video Indexer – fogalmak |} A Microsoft Docs
description: Ez a témakör néhány olyan fogalmat, a Video Indexer szolgáltatás.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 67bbb7046d71b904dfdcaad0e44024e8f30ac288
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543707"
---
# <a name="video-indexer-concepts"></a>Video Indexer-fogalmak
 
Ez a cikk néhány olyan fogalmat, a Video Indexer szolgáltatás ismerteti.
    
## <a name="summarized-insights"></a>Összesített insights

Összesített insights tartalmaznia egy összesített nézetét az adatok: arcok, témakörök, érzelmeket. Helyett kapcsolaton át, mindegyik időtartományok több ezer, és győződjön meg róla melyik arcok, például az összesített insights tartalmazza az összes az arcok, és mindegyik, a megjelenő időtartományok és a %-os látható.

## <a name="time-range-vs-adjusted-time-range"></a>időtartomány és a módosított időtartomány

TimeRange az időtartománya az eredeti videó. AdjustedTimeRange az időtartománya képest az aktuális listába. Különböző sornyi különböző videók lejátszási lista hozhat létre, mert 1 óra videó igénybe vehet, és használja azt, ha például 10:00-10:15 csak 1 sort. Ebben az esetben kell egy listát az 1 sort, ahol az időtartomány 10:00-10:15 azonban a adjustedTimeRange 00:00 – 00:15.
 
## <a name="blocks"></a>blokkok

Blokkok úgy van kialakítva, hogy egyszerűbb legyen halad át az adatokat. Például blokk előfordulhat, hogy bonthatók fel alapján előadói módosítása, vagy hosszabb szünet.

## <a name="next-steps"></a>További lépések

Első lépések kapcsolatos információkért lásd: [regisztráljon, és az első videó feltöltése](video-indexer-get-started.md).

## <a name="see-also"></a>Lásd még

[Video Indexer – áttekintés](video-indexer-overview.md)
