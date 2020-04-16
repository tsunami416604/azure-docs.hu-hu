---
title: Videómoderálás emberi értékeléssel - Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: Gépi videomoderálás és a Véleményezés eszköz használata a nem megfelelő tartalom moderálásához
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 0c031a890efc7fad7e5d9caefce3b0e66c515d90
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404240"
---
# <a name="video-moderation-with-human-review"></a>Videómoderálás emberi értékeléssel

A Tartalommoderátor gépi támogatású [videómoderálási](video-moderation-api.md) és [-felülvizsgálati eszközével](Review-Tool-User-Guide/human-in-the-loop.md) moderálhatja a felnőtteknek készült (explicit) és pikáns (szuggesztív) tartalmakról készült videókat és átiratokat, hogy a legjobb eredményt hozhatja vállalkozása számára.

## <a name="video-trained-classifier-preview"></a>Videóval betanított osztályozó (előzetes verzió)

A géppel támogatott videobesorolás képpel képzett vagy videóbe tanított modellekkel érhető el. A képpel betanított videoosztályozókkal ellentétben a Microsoft felnőtt és pikáns videóosztályozója videókkal van betanítva. Ez a módszer jobb egyezési minőséget eredményez.

## <a name="shot-detection"></a>Lövés észlelése

A besorolás részleteinek kihelyezésekor a további videointelligencia nagyobb rugalmasságot biztosít a videók elemzésében. Helyett kiküldött csak a test, Mikroszkóp' video moderálás szolgáltatás szolgáltat szemcsésedik- szint információ túl. Most lehetősége van arra, hogy elemezze a videókat a felvétel szintjén és a keret szintjén.

## <a name="key-frame-detection"></a>Kulcskeret észlelése

A képkockák rendszeres időközönkénti kihelyezése helyett a videomoderálási szolgáltatás csak potenciálisan teljes (jó) képkockákat azonosít és ad ki. A funkció lehetővé teszi a hatékony képkocka-generálást a keretszintű felnőtt és pikáns elemzéshez.

A következő kivonat részleges választ mutat a lehetséges felvételek, kulcskeretek, és a felnőtt és pikáns pontszámok:

```json
"fragments":[  
  {  
    "start":0,
    "duration":18000
  },
  {  
    "start":18000,
    "duration":3600,
    "interval":3600,
    "events":[  
      [  
        {  
          "reviewRecommended":false,
          "adultScore":0.00001,
          "racyScore":0.03077,
          "index":5,
          "timestamp":18000,
          "shotIndex":0
        }
      ]
    ]
  },
  {  
    "start":18386372,
    "duration":119149,
    "interval":119149,
    "events":[  
      [  
        {  
          "reviewRecommended":true,
          "adultScore":0.00000,
          "racyScore":0.91902,
          "index":5085,
          "timestamp":18386372,
          "shotIndex":62
        }
      ]
    ]
```

## <a name="visualization-for-human-reviews"></a>Képi megjelenítés emberi véleményekhez

Az árnyaltabb esetekben a vállalkozásoknak emberi felülvizsgálati megoldásra van szükségük a videó, a képkockák és a géppel hozzárendelt címkék megjelenítéséhez. A videókat és kereteket felülvizsgáló emberi moderátorok teljes képet kapnak az elemzésekről, megváltoztatják a címkéket, és benyújtják döntéseiket.

![videóellenőrző eszköz alapértelmezett nézete](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Játékos nézet videószintű ellenőrzéshez

A videószintű bináris döntéseket egy videólejátszó nézet teszi lehetővé, amely potenciális felnőtt és pikáns képkockákat jelenít meg. Az emberi látogató navigálni a videó különböző sebesség lehetőségeket, hogy vizsgálja meg a jeleneteket. A címkék átkapcsolásával erősítik meg döntéseiket.

![videó ellenőrző eszköz lejátszó nézet](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Keretek nézet részletes felülvizsgálatok

A képkockák on-frame elemzésének részletes videóáttekintése keretalapú nézetben lehetséges. Az emberi véleményezők áttekintik és kijelölik egy vagy több képkockát, és a döntéseket megerősítenék. A következő opcionális lépés a támadó keretek vagy tartalom kivonása.

![videóellenőrző eszköz keretei nézet](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Átiratmoderálás

A videók általában olyan hangon vannak, amely moderálást és sértő beszédhez is szükséges. Az Azure Media Indexer szolgáltatás használatával konvertálja a beszéd szöveggé, és a tartalommoderátor felülvizsgálati API-t használja a szövegmoderálás átiratának elküldéséhez a felülvizsgálati eszközön belül.

![videóellenőrző eszköz átiratának nézete](images/video-review-transcript-view.png)

## <a name="next-steps"></a>További lépések

- Ismerkedés a [videómoderálás rövid útmutatójával.](video-moderation-api.md)
- További információ [arról,](video-reviews-quickstart-dotnet.md) hogyan hozhat létre videoértékeléseket az emberi véleményezők számára a moderált kimenetből.
- [Videóátirat-vélemények hozzáadása](video-transcript-reviews-quickstart-dotnet.md) a videóértékelésekhez.
- Nézze meg a részletes bemutató, hogyan kell fejleszteni a [teljes videó moderálás megoldás](video-transcript-moderation-review-tutorial-dotnet.md).