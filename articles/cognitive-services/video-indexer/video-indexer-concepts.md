---
title: Az Azure videó indexelő fogalmak |} Microsoft Docs
description: Ez a témakör néhány olyan fogalmat, a videó indexelő szolgáltatás.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 04/17/2017
ms.author: juliako
ms.openlocfilehash: 01d92a6b55d2fb2c09cee333f482d79d2cdf763c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348999"
---
# <a name="video-indexer-concepts"></a>Videó indexelő fogalmak
 
Ez a témakör néhány olyan fogalmat, a videó indexelő szolgáltatás.
    
## <a name="summarized-insights"></a>Összesített insights

Összesített insights tartalmaznak az adatok összesített nézete: lapokat, kulcsszavakat, hangulati elemek. Helyett meghaladja az összes időtartományhoz ezer fog, és ellenőrzése, hogy mely lapok vannak benne, például az összesített insights minden lap tartalmaz, és mindegyik megjelenik időtartományhoz és a %-ában látható.

## <a name="topicskeywords"></a>Témakörök/kulcsszavak

Témakörök/kulcsszavak a listáját, amelyek a videó indexelő kiolvassa a szöveget a legfontosabb kifejezések szerepelnek. Például a Scott Guthrie-t videó tartalmazhatja a következő témakörök kulcsszavak: biztonsági, az Azure, a Microsoft Cloud, a bevétel.

## <a name="sentiments"></a>Hangulati elemek

Videó indexelő elemzi ki, ha azt észleli, valamint hangulati elemek. "Ez az esemény nagyon izgalmas" például egy pozitív véleményeket.

## <a name="time-range-vs-adjusted-time-range"></a>időtartomány és módosított időtartomány

TimeRange az időt, amely az eredeti videó. AdjustedTimeRange az időt, amely az aktuális lista viszonyítva. Mivel a lista másik sornyi különböző videók hozhat létre, 1 órás videó igénybe vehet, és csak 1 használja azt, például 10:00 – 10:15. Ebben az esetben, hogy a lista 1 sorral, ha az időtartományt 10:00 – 10:15, de a adjustedTimeRange érték 00:00 – 00:15.
 
## <a name="blocks"></a>Blokkok

Blokkok úgy van kialakítva, hogy könnyebben halad át az adatokat. Például blokk előfordulhat, hogy kell bontani alapján amikor hangszórók változnak, vagy hosszú szünetet.

## <a name="next-steps"></a>További lépések

Első lépések kapcsolatos információkért lásd: [iratkozzon fel, és töltse fel az első videó](video-indexer-get-started.md).

## <a name="see-also"></a>Lásd még

[Az indexelő áttekintő videó](video-indexer-overview.md)

