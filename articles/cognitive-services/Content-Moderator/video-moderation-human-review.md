---
title: Videó moderálása humán felülvizsgálattal – Content Moderator
titleSuffix: Azure Cognitive Services
description: A videó-moderálás és a humán felülvizsgálati eszközök használata mérsékelt, nem megfelelő tartalomra
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: sajagtap
ms.openlocfilehash: 69075f6b36a0be7cd4d0dd4453ea9cdfc08d77dc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561163"
---
# <a name="video-moderation-with-human-review"></a>Videó moderálása humán felülvizsgálattal

A legjobb eredmények elérése érdekében használja a Content Moderator gépi támogatású [videó](video-moderation-api.md) -moderálási és [emberi felülvizsgálati eszközt](Review-Tool-User-Guide/human-in-the-loop.md) a felnőtteknek (explicit) és a zamatos (szuggesztív) tartalomhoz.

## <a name="video-trained-classifier-preview"></a>Videós képzésen alapuló osztályozó (előzetes verzió)

A géppel támogatott videók besorolása vagy a képesített modellek vagy a videós képzésen alapuló modellek alapján érhető el. A képesített videó-besorolásokkal ellentétben a Microsoft felnőtt és zamatos videós besorolása videókkal van betanítva. Ez a módszer jobb egyezési minőséget eredményez.

## <a name="shot-detection"></a>Felvételészlelés

A besorolási adatok kihelyezése során a további videó-intelligenciával rugalmasabban elemezheti a videókat. A képkockák kihelyezése helyett a Microsoft videó-moderálási szolgáltatása is tartalmaz shot szintű információkat. Most lehetősége van arra, hogy a videókat a lövés szintjén és a keret szintjén elemezze.

## <a name="key-frame-detection"></a>Kulcstároló-észlelés

A keretek rendszeres időközönként történő kihelyezése helyett a video moderációs szolgáltatás csak a lehetséges teljes (jó) kereteket azonosítja és jeleníti meg. A funkció lehetővé teszi a hatékony keret létrehozását a frame-szintű felnőtt és a zamatos elemzésekhez.

A következő kivonat részleges választ mutat a lehetséges felvételek, a kulcstárolók és a felnőtt és a zamatos pontszámok tekintetében:

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

## <a name="visualization-for-human-reviews"></a>Vizualizációk emberi felülvizsgálatokhoz

Árnyaltabb esetekben a vállalatoknak egy emberi felülvizsgálati megoldásra van szükségük a videó, a keretei és a gép által hozzárendelt címkék megjelenítéséhez. A videók és keretek áttekintésére szolgáló emberi moderátorok teljes képet kapnak az elemzésekről, megváltoztathatják a címkéket, és elküldhetik döntéseiket.

![videó-felülvizsgálati eszköz alapértelmezett nézete](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Videó szintű felülvizsgálatra szolgáló lejátszó nézet

A videós szintű bináris döntéseket egy videolejátszó nézet teszi elérhetővé, amely megjeleníti a potenciális felnőtt és a zamatos kereteket. Az emberi felülvizsgálók különböző sebességi lehetőségekkel navigálnak a videóban a jelenetek vizsgálatához. A címkék megerősítik a döntéseiket.

![videó-felülvizsgálati eszköz – lejátszó nézet](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Részletes felülvizsgálatok – keretek nézet

A kereten belüli elemzés részletes videó-áttekintést nyújt a frame-alapú nézettel. Az emberi felülvizsgálók áttekintik, majd kijelölnek egy vagy több képkockát, és felváltják a címkéket a döntéseik megerősítéséhez. Egy választható következő lépés a sértő keretek vagy tartalmak kivonása.

![videó-felülvizsgálati eszköz keretének nézete](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Átiratmoderálás

A videók jellemzően olyan hangvételt igényelnek, amelynek a moderálása a támadó beszédhez is szükséges. A Azure Media Indexer szolgáltatással szöveggé alakíthatja a beszédfelismerést, és a Content Moderator felülvizsgálati API használatával elküldheti a szöveges moderálás átiratát a felülvizsgálati eszközön belül.

![videó-felülvizsgálati eszköz átirat nézete](images/video-review-transcript-view.png)

## <a name="next-steps"></a>További lépések

- Ismerkedjen meg a [videó moderálási](video-moderation-api.md)útmutatójának használatába.
- Ebből a témakörből [](video-reviews-quickstart-dotnet.md) megtudhatja, hogyan hozhatja ki az emberi felülvizsgálók videós felülvizsgálatait a moderált kimenetből.
- [Videó](video-transcript-reviews-quickstart-dotnet.md) -visszajelzéseket adhat hozzá a videókhoz.
- Tekintse meg a [teljes videó-moderálási megoldás](video-transcript-moderation-review-tutorial-dotnet.md)kidolgozásának részletes leírását.