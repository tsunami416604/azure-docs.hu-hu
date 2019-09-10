---
title: Video Indexer jelenetek, felvételek és kulcsképek
titleSuffix: Azure Media Services
description: Ez a témakör áttekintést nyújt a Video Indexer jelenetekről, a képekről és a kulcsképekről.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b24778434596f583be44572612c856fa4e0cecde
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860229"
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

## <a name="editorial-shot-type-detection"></a>Szerkesztői shot típusú észlelés

Az adatforrások JSON-beli felvételéhez tartozó shot típus a szerkesztői típust jelöli. Ezek a shot Type-jellemzők hasznosak lehetnek a videók klipek, pótkocsik számára történő szerkesztésekor, illetve a képkockák adott stílusának művészi célokra való keresésekor. A különböző típusok meghatározása az egyes lövések első képkockájának elemzése alapján történik. A képeket az első képkockában megjelenő arcok méretezése, mérete és helye azonosítja. 

A shot méretének és méretezésének meghatározása a kamera és a keretben megjelenő arcok távolsága alapján történik. Ezeknek a tulajdonságoknak a használatával a Video Indexer észleli a következő shot típusokat:

* Széles: egy teljes személy törzsét jeleníti meg.
* Közepes: egy személy felső törzsét és arcát mutatja.
* Bezárás: elsősorban egy személy arcát mutatja.
* Extreme Közelkép: egy személy arcát jeleníti meg a képernyő kitöltésével. 

A lövési típusok a tulajdonos karakterek helye alapján is meghatározhatók a keret közepéhez képest. Ez a tulajdonság határozza meg a következő shot típusokat a Video Indexerban:

* Bal oldali: a keret bal oldalán egy személy jelenik meg.
* Középső arc: egy személy jelenik meg a keret középső régiójában.
* Jobb oldali: egy személy jelenik meg a keret jobb oldalán.
* Kültéri: egy személy jelenik meg egy külső beállításban.
* Beltéri: egy személy megjelenik egy beltéri környezetben.

További jellemzők:

* Két kép: két személy közepes méretű arca látható.
* Több arc: több mint két személy.

## <a name="next-steps"></a>További lépések

[Vizsgálja meg az API által létrehozott Video Indexer kimenetet](video-indexer-output-json-v2.md#scenes)
