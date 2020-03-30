---
title: Videoindexelő-jelenetek, -felvételek és kulcsképek
titleSuffix: Azure Media Services
description: Ez a témakör áttekintést nyújt a Video Indexer jelenetekről, felvételekről és kulcsképekről.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: a833fd808049cfce95b182910e50e38d3c39f4e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245940"
---
# <a name="scenes-shots-and-keyframes"></a>Jelenetek, felvételek és kulcsképkockák

A Video Indexer támogatja a videók szerkezeti és szemantikai tulajdonságokon alapuló időbeli egységekre történő szegmentálását. Ez a funkció lehetővé teszi az ügyfelek számára, hogy könnyedén böngészhessenek, kezelhessenek és szerkeszthessék videotartalmaikat különböző részletességek alapján. A jelen témakörben ismertetett jelenetek, felvételek és kulcsképek alapján például.   

![Jelenetek, felvételek és kulcsképkockák](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Jelenetfelismerés  
 
A Video Indexer vizuális jelzések alapján határozza meg, hogy egy jelenet mikor változik meg a videóban. A jelenet egyetlen eseményt ábrázol, és egymást követő felvételek sorozatából áll, amelyek szemantikailag kapcsolódnak egymáshoz. A jelenet miniatűrje az alapul szolgáló felvétel első kulcsképe. A videóindexelő a videókat jelenetekbe szegezi az egymást követő felvételek színkoherenciája alapján, és lekéri az egyes jelenetek kezdési és befejezési idejét. A jelenetfelismerés kihívást jelentő feladatnak számít, mivel a videók szemantikai aspektusainak számszerűsítését foglalja magában.

> [!NOTE]
> Legalább 3 jelenetet tartalmazó videókra alkalmazható.

## <a name="shot-detection"></a>Lövés észlelése

A Video Indexelő a képolvasás vizuális jelzések alapján határozza meg, hogy mikor változik a felvétel a videóban, a szomszédos képkockák színsémájának hirtelen és fokozatos átmeneteinek követésével. A felvétel metaadatai tartalmazzák a kezdési és befejezési időpontot, valamint a felvételben szereplő kulcsképek listáját. A felvételek egymást követő képkockák, amelyeket ugyanabból a fényképezőgépből vettek.

## <a name="keyframe-detection"></a>Kulcskép észlelése

A Video Indexer kiválasztja az egyes felvételekre legjobban reprezentáló keret(eke)t. A kulcsképek a teljes videóból kiválasztott reprezentatív képkockák az esztétikai tulajdonságok (például kontraszt és állandóság) alapján. A Video Indexer a kép metaadatainak részeként lekéri a kulcsképazonosítók listáját, amely alapján az ügyfelek nagy felbontású képként kinyerhetik a kulcsképet.  

### <a name="extracting-keyframes"></a>Kulcsképek kibontása

A videó nagy felbontású kulcsképének kinyeréséhez először fel kell töltenie és indexelnie kell a videót.

![Kulcsképek](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>A Video Indexer weboldalával

Kulcsképek kinyeréséhez a Video Indexer webhely használatával töltse fel és indexelje a videót. Miután az indexelési feladat befejeződött, kattintson a **Letöltés** gombra, és válassza **az Eltérések (ZIP)** lehetőséget. Ezzel letölti az összetevők mappát a számítógépre. 

![Kulcsképek](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Csomagolja ki és nyissa meg a mappát. A *_KeyframeThumbnail* mappában, és megtalálja az összes kulcsképkockákat, amelyeket kivont a videó. 

#### <a name="with-the-video-indexer-api"></a>A Video Indexer API-val

Ha kulcsképeket szeretne beszerezni a Video Indexer API használatával, töltse fel és indexelje a videót a [Videohívás feltöltése](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) funkcióval. Az indexelési feladat befejezése után hívja meg a [Videoindex bekerülése](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)hívást. Ez megadja az összes betekintést, hogy a Video Indexer kivont a tartalom egy JSON-fájlban.  

Minden egyes felvétel metaadatának részeként megkapja a kulcsképazonosítók listáját. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

Most minden ilyen kulcskép-azonosítót futtatnia kell a [Miniatűrök beírása](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) híváson. Ezzel letölti az egyes kulcsképkocka-képeket a számítógépre. 

## <a name="editorial-shot-type-detection"></a>Szerkesztői felvétel típusának észlelése

Kulcsképek társított felvételek a kimeneti JSON. 

Az egyes felvételekhez társított felvétel típusa az insights JSON-ban a szerkesztői típusát jelöli. Előfordulhat, hogy ezek a felvétel típus jellemzői hasznosak, ha a videókat klipekbe, előzetesekbe vagy művészi célokra egy adott kulcsképstílust keres. A különböző típusok meghatározása az egyes felvételek első kulcsképének elemzése alapján kerül meghatározásra. A felvételeket az első kulcsképkocka képaránya, mérete és helye alapján azonosítják. 

A felvétel mérete és léptéke a kamera és a keretben megjelenő arcok közötti távolság alapján kerül meghatározásra. Ezekkel a tulajdonságokkal a Video Indexer a következő felvételtípusokat észleli:

* Széles: azt mutatja, egy egész személy testét.
* Közepes: mutatja egy személy felsőtest és az arc.
* Közelről: főleg egy személy arcát mutatja.
* Extrém közelkép: egy személy arcát mutatja, amint kitölti a képernyőt. 

A felvétel típusai a tárgy karaktereinek helye alapján is meghatározhatók a keret középpontjához képest. Ez a tulajdonság a következő képtípusokat határozza meg a Video Indexer ben:

* Bal oldali arc: egy személy jelenik meg a bal oldalon a keret.
* Középső lap: egy személy jelenik meg a keret középső részén.
* Jobb oldali arc: egy személy jelenik meg a jobb oldalon a keret.
* Kültéri: egy személy jelenik meg egy szabadtéri környezetben.
* Beltéri: egy személy jelenik meg egy beltéri környezetben.

További jellemzők:

* Két lövés: két személy arcát mutatja közepes méretű.
* Több arc: kettőnél több személy.


## <a name="next-steps"></a>További lépések

[Az API által létrehozott Video Indexer kimenet vizsgálata](video-indexer-output-json-v2.md#scenes)
