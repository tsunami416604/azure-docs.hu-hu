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
ms.openlocfilehash: 224c8b05027f51fb99c8d58be34c3604032c0f77
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399495"
---
# <a name="video-indexer-concepts"></a>Video Indexer-fogalmak
 
Ez a témakör néhány olyan fogalmat, a Video Indexer szolgáltatás.
    
## <a name="summarized-insights"></a>Összesített insights

Összesített insights tartalmaznia egy összesített nézetét az adatok: arcok, kulcsszavakat, hangulati. Helyett kapcsolaton át, mindegyik időtartományok több ezer, és győződjön meg róla melyik arcok, például az összesített insights tartalmazza az összes az arcok, és mindegyik, a megjelenő időtartományok és a %-os látható.

## <a name="topicskeywords"></a>A témakörök/kulcsszavak

Témakörök/kulcsszavak olyan listájában, a Video Indexer kiolvassa a szöveget a kulcskifejezéseket. Például a Scott Guthrie videó tartalmazhatja a következő témakörök kulcsszavakat: biztonság, az Azure, a Microsoft Cloud, a bevétel.

## <a name="sentiments"></a>hangulati

A Video Indexer elemzi az átiratok, amikor azt észleli, valamint hangulati. "Ez az esemény nagyon izgalmas" például egy pozitív véleményeket.

## <a name="time-range-vs-adjusted-time-range"></a>időtartomány és a módosított időtartomány

TimeRange az időtartománya az eredeti videó. AdjustedTimeRange az időtartománya képest az aktuális listába. Különböző sornyi különböző videók lejátszási lista hozhat létre, mert 1 óra videó igénybe vehet, és használja azt, ha például 10:00-10:15 csak 1 sort. Ebben az esetben kell egy listát az 1 sort, ahol az időtartomány 10:00-10:15 azonban a adjustedTimeRange 00:00 – 00:15.
 
## <a name="blocks"></a>blokkok

Blokkok úgy van kialakítva, hogy egyszerűbb legyen halad át az adatokat. Például blokk előfordulhat, hogy bonthatók fel alapján előadói módosítása, vagy hosszabb szünet.

## <a name="next-steps"></a>További lépések

Első lépések kapcsolatos információkért lásd: [regisztráljon, és az első videó feltöltése](video-indexer-get-started.md).

## <a name="see-also"></a>Lásd még

[Video Indexer – áttekintés](video-indexer-overview.md)
