---
title: Az emberi ellenőrző - Content Moderator videomoderálás
titlesuffix: Azure Cognitive Services
description: Nem megfelelő tartalom mérsékelt videomoderálás gépi támogatású és emberi vizsgálóeszközöket használata
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/20/2018
ms.author: sajagtap
ms.openlocfilehash: 33956e1986265b36f6df9a2d19909cb974bd3197
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222080"
---
# <a name="video-moderation-with-human-review"></a>Az emberi ellenőrző videomoderálás

Használja a Content Moderator gépi támogatású [videomoderálás](video-moderation-api.md) és [emberi vizsgálóeszköz](Review-Tool-User-Guide/human-in-the-loop.md) és szövegekben vonatkozó (kifejezett) felnőtt tartalom mérsékelt videók, és a legjobb eredményt (kétértelmű) pikáns tartalom az üzleti.

## <a name="video-trained-classifier-preview"></a>A videó betanított osztályozó (előzetes verzió)

Videó besorolási gépi támogatású vagy a kép betanított modellek vagy videó betanított modellek érhető el. Videó deklarációkkal kép betanított eltérően a Microsoft felnőtt és szexuális videó osztályozó be van tanítva videók segítségével. Ez a módszer jobban egyezés minőségi eredményez.

## <a name="shot-detection"></a>Jelenetfelismerés

A besorolási részletek megjelenítése, nagyobb rugalmasság a videók elemzése segít a további videó intelligencia. Ahelyett, hogy csak a keretek ad ki, a Microsoft videomoderálás szolgáltatás biztosít célirányítópultot a kiszolgálószintű információkat túl. Most már lehetőség elemezheti videóit a képernyőkép szint és a keret szintjén.
 
## <a name="key-frame-detection"></a>A kulcs keret felismerése

Ahelyett, hogy rendszeres időközönként ad ki a keretek, a videomoderálás szolgáltatás azonosítja, és csak potenciálisan teljes (jó) keretek kimenete. A funkció lehetővé teszi a hatékony keret generációs keret szintű felnőtt és szexuális elemzés céljából.

A következő lehetséges helyességének, a kulcs keretek és a felnőtt és szexuális pontszámok részleges válasz mutat:

    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]


## <a name="visualization-for-human-reviews"></a>Emberi ellenőrző Vizualizáció

További precízebb, vállalatok a videót, a keretek és a számítógéphez hozzárendelt címkék megjelenítése az emberi ellenőrző megoldásra van szükség. Az insights teljes körű képet kaphat, megváltoztathatja a címkéket, és küldje el a döntéshozatal az emberi moderátorok keretek és videók megtekintésével.

![videó felülvizsgálati eszköz alapértelmezett nézet](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Videó-szintű felülvizsgálatra Player megtekintése

Videó-szintű bináris döntés, amely megjeleníti a potenciális felnőtt és szexuális keretek videólejátszó nézetben lehetséges. Az emberi felülvizsgálók keresse meg a videó megvizsgálhatja a színfalak különböző sebességű beállításokkal. Ezek a döntéshozatal a címkék átállításával erősítse meg.

![videó felülvizsgálati eszköz player megtekintése](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>A részletes ismertetők keretek megtekintése

A keret-keret elemzéshez részletes videó felülvizsgálata tette lehetővé, keret-alapú nézetben. Az emberi felülvizsgálók tekintse át, és jelöljön ki egy vagy több képkockát és váltsa át a címkéket, hogy erősítse meg a döntéseket hozhat. Egy nem kötelező következő lépése a sértő keretek vagy a tartalom kivonásra.

![videó felülvizsgálati eszköz keretek megjelenítése](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Átirat moderálása

Videók általában rendelkeznek hangalapú keresztül, amely is szüksége van a moderálás sértő speech. Az Azure Media Indexer szolgáltatás segítségével alakíthatja az írott szöveget, és küldje el az átiratot tud szövegmoderálás belül a vizsgálóeszköz a Content Moderator felülvizsgálati API használatával.

![videó felülvizsgálati eszköz szöveges nézet](images/video-review-transcript-view.png)

## <a name="next-steps"></a>További lépések

Ismerkedés a [videomoderálás rövid](video-moderation-api.md). 

Ismerje meg, hogyan hozhat létre [videót felülvizsgálatok](video-reviews-quickstart-dotnet.md) az emberi véleményező a moderált kimenetből.

Adjon hozzá [videóátiratot felülvizsgálatok](video-transcript-reviews-quickstart-dotnet.md) a videót felülvizsgálatok.

Tekintse meg a részletes oktatóanyag, hogyan hozhat létre egy [videomoderálás megoldás befejezéséhez](video-transcript-moderation-review-tutorial-dotnet.md). 
