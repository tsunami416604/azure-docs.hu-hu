---
title: Az Azure Content moderátor - videó moderálás |} Microsoft Docs
description: Nem megfelelő tartalom mérsékelt videó moderálás gép támogatású és emberi felülvizsgálati eszközök segítségével
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/20/2018
ms.author: sajagtap
ms.openlocfilehash: fb26c9af55381c80a3f520b1a0068d8f72c91061
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "35346971"
---
# <a name="video-moderation"></a>Videomoderálás

Használata tartalom moderátor gép támogatású [videó moderálás](video-moderation-api.md) és [emberi felülvizsgálati eszköz](Review-Tool-User-Guide/human-in-the-loop.md) mérsékelt videók és ki a felnőtt (explicit) és a legjobb eredményt ellopható (kétértelmű) tartalom az üzleti.

## <a name="video-trained-classifier"></a>Videó betanítása osztályozó

Videó besorolás gép támogatású vagy érhető el, a modellek betanítása kép vagy videó betanított modellek. Videó osztályozó kép betanítása eltérően a Microsoft felnőtt és ellopható videó osztályozó be van tanítva, és videók. Ez a módszer egyezés magasabb színvonalú eredményez.

## <a name="shot-detection"></a>Hibaüzenetet észlelése

A besorolási részletek megjelenítése, további videó eszközintelligencia segít a videók elemzése nagyobb rugalmasságot. Helyett csak a keretek írását, a Microsoft videó moderálás szolgáltatás hibaüzenetet szintű információkat nyújt túl. Most már lehetősége nyílik a videók hibaüzenetet szintjét és a keret szintjén elemzéséhez.
 
## <a name="key-frame-detection"></a>Kulcskocka észlelése

Helyett szerint kiírta volna keretek rendszeres időközönként, a videó moderálás szolgáltatás azonosítja, és csak a potenciálisan teljes (jó) keretek kimenete. A funkció lehetővé teszi, hogy a hatékony keret generációs keret szintű felnőtt és ellopható elemzés céljából.

A következő lehetséges pillanatfelvételek, kulcs keretek és felnőtt és ellopható pontszámok részleges válasz mutat:

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


## <a name="visualization-for-human-reviews"></a>Emberi ellenőrzésekhez képi megjelenítés

További nuanced esetben vállalatok emberi felülvizsgálati megoldás szükséges a videót, a keretek és a számítógéphez hozzárendelt címkék megjelenítése. Az emberi moderátorok keretek és videók megtekintésével egy teljes megtekintheti a feltárása, címkék módosítása, és küldje el a döntések.

![videó felülvizsgálati eszköz az alapértelmezett nézet](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>A videó szintű felülvizsgálatra Player megtekintése

Videó szintű bináris döntéseket lehetséges felnőtt és ellopható keretek olyan videólejátszó nézetet tesz lehetővé. Emberi felülvizsgáló keresse meg a videót a különböző sebességű beállítások vizsgálata a háttérben. Ezek erősítse meg a saját döntések elvégezte a címkék.

![videó felülvizsgálati eszköz player megtekintése](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Részletes ellenőrzésekhez keretek megjelenítése

A keret-keret elemzéshez videó részletes áttekintése tette lehetővé a keret-alapú nézetet. Emberi felülvizsgáló tekintse át, és jelöljön ki egy vagy több keretet, és a döntések megerősítéséhez címkék bekapcsolására. Következő igény kivonási sértő keretek vagy tartalom.

![videó felülvizsgálati eszköz keretek megjelenítése](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Beszélgetés szövegének moderálás

Videók általában rendelkezik hang, amelyet a moderálás is sértő beszéd. Az Azure Media Indexer szolgáltatás segítségével beszéd szöveggé alakítani, és küldje el a szöveg moderálás tekintse át a segédprogrammal szövegének tartalom moderátor felülvizsgálati API használatával.

![videó felülvizsgálati eszköz Beszélgetés szövegének megtekintése](images/video-review-transcript-view.png)

## <a name="next-steps"></a>További lépések

Az első lépései a [videó moderálás gyors üzembe helyezés](video-moderation-api.md). 

Megtudhatja, hogyan készítése [ellenőrzi, hogy a videó](video-reviews-quickstart-dotnet.md) az emberi véleményező a moderált kimenetből.

Adja hozzá [ellenőrzi, hogy videó Beszélgetés szövegének](video-transcript-reviews-quickstart-dotnet.md) számára a videó értékelést.

Tekintse meg a részletes útmutatót a fejlesztéséhez egy [végezze el a videó moderálás megoldás](video-transcript-moderation-review-tutorial-dotnet.md). 
