---
title: Video Indexer jelenetek, felvételek és kulcsképek – Azure
titlesuffix: Azure Media Services
description: Ez a témakör áttekintést nyújt a Video Indexer jelenetekről, a képekről és a kulcsképekről.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: cdabc1b6bfed519098f656710ef49a946e676cf2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815652"
---
# <a name="scenes-shots-and-keyframes"></a>Jelenetek, felvételek és kulcsképkockák

Video Indexer támogatja a videók szegmentálását a strukturális és szemantikai tulajdonságok alapján. Ez a funkció lehetővé teszi az ügyfeleknek, hogy különböző részletességek alapján könnyedén böngészhetik, kezelhetik és szerkeszthetik a videó tartalmát. Például a jelen témakörben ismertetett jelenetek, felvételek és kulcsképek alapján.   

![Jelenetek, felvételek és kulcsképkockák](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Jelenet észlelése  
 
Video Indexer határozza meg, hogy a jelenet Mikor változik a videóban a vizualizációs célzások alapján. Egy jelenet egyetlen eseményt ábrázol, és egymást követő felvételekből áll, amelyek szemantikai kapcsolatban állnak egymással. A jelenet miniatűrje a mögöttes felvétel első képkockája. A video Indexer egy videót helyez el a jeleneteken az egymást követő felvételek közötti színkoherencia alapján, és lekéri az egyes jelenetek kezdő és záró időpontját. A jelenet észlelése kihívást jelentő feladat, mivel a videók szemantikai szempontjait is magában foglalja.

> [!NOTE]
> Olyan videókra vonatkozik, amelyek legalább 3 jelenetet tartalmaznak.

## <a name="shot-detection"></a>Felvételészlelés

Video Indexer meghatározza, hogy mikor változik a videó a vizualizációs dákó alapján, hogy a rendszer hirtelen és fokozatos átmeneteket is nyomon követ a szomszédos keretek színsémájában. A shot metaadatai a kezdő és a záró időpontot is tartalmazzák, valamint a lövéshez tartozó kulcsképek listáját. A felvételek egymást követő képkockák, amelyek ugyanabból a kamerából származnak.

## <a name="keyframe-detection"></a>Kulcsképek észlelése

Kiválasztja a lövéshez legjobban illő keretet (ka) t. A kulcsképek a teljes videóban az esztétikai tulajdonságok (például a kontraszt és a stabilitás) alapján kiválasztott reprezentatív keretek. Video Indexer lekéri a kulcsképek-azonosítók listáját a shot metaadatainak részeként, amely alapján az ügyfelek kinyerhetik a kulcsképek miniatűrjét. 

A kulcsképek a kimenet JSON-ban található felvételekhez vannak társítva. 

## <a name="next-steps"></a>További lépések

[Vizsgálja meg az API által létrehozott Video Indexer kimenetet](video-indexer-output-json-v2.md#scenes)
