---
title: Márkák modell testreszabása a Video Indexerben – Azure
titleSuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt arról, hogy mi a Márkák modell a Video Indexerben, és hogyan szabhatja testre.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: ca3d825fb2f4184448cc279d9408f47ad4ad004a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838361"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Márkák modell testreszabása a Video Indexerben

A Video Indexer támogatja a beszéd- és vizuális szövegből származó márkaészlelést a video- és hangtartalmak indexelése és újraindexelése során. A márkaészlelési funkció azonosítja a Bing márkaadatbázisa által javasolt termékek, szolgáltatások és vállalatok említését. Ha például a Microsoft ot megemlítik egy video- vagy hangtartalomban, vagy ha vizuális szövegben jelenik meg egy videóban, a Video Indexer márkaként észleli azt a tartalomban. A márkák at a kontextust használó más kifejezésektől eltérő kifejezéseket használnak.

A márkafelismerés számos üzleti forgatókönyvben hasznos, például a tartalomarchívumban és a felfedezésben, a kontextuális hirdetésekben, a közösségi média elemzésben, a kiskereskedelmi versenyelemzésben és még sok másban. A Video Indexer márkaészlelés lehetővé teszi a márkanevek indexelését beszéd- és vizuális szövegben, a Bing márkaadatbázisának használatával, valamint a testreszabással, ha minden egyes Video Indexer-fiókhoz egyéni Brands modellt hoz létre. Az egyéni Márkák modell funkció lehetővé teszi annak kiválasztását, hogy a Video Indexer észleli-e a márkákat a Bing márkák adatbázisából, kizárjon-e bizonyos márkákat az észlelésből (lényegében a márkák feketelistájának létrehozása), és olyan márkákat is tartalmazzon, amelyeknek a modell, amely nem lehet a Bing márkák adatbázisában (lényegében a márkák fehér listájának létrehozása). A létrehozott egyéni Márkák modell csak abban a fiókban lesz elérhető, amelyben létrehozta a modellt.

## <a name="out-of-the-box-detection-example"></a>Dobozon kívüli észlelési példa

A [Microsoft Build 2017 Day 2](https://www.videoindexer.ai/media/ed6ede78ad/) bemutatóban a "Microsoft Windows" márka többször jelenik meg. Néha az átiratban, néha vizuális szövegként, és soha nem szó szerint. Video Indexer érzékeli nagy pontossággal, hogy a kifejezés valóban márka alapján a kontextus, amely több mint 90k márkák ki a dobozból, és folyamatosan frissíti. 02:25-kor a Video Indexer észleli a márkát a beszédből, majd ismét 02:40-kor a vizuális szövegből, amely az ablakok emblémájának része.

![Márkák – áttekintés](./media/content-model-customization/brands-overview.png)

Beszél ablakok keretében az építőipar nem ismeri fel a "Windows", mint a márka, és ugyanaz a Box, Apple, Fox, stb, alapuló fejlett Machine Learning algoritmusok, amelyek tudják, hogyan kell félrefogalmazni összefüggésben. A márkaészlelés minden támogatott nyelvünkön működik. Kattintson ide a [teljes Microsoft Build 2017 Day 2 vitaindító videó ért és indexhez.](https://www.videoindexer.ai/media/ed6ede78ad/)

Ahhoz, hogy a saját márkák, nézd meg A következő lépéseket.

## <a name="next-steps"></a>További lépések

[Márkák modell testreszabása API-k használatával](customize-brands-model-with-api.md)

[Márkák modell testreszabása a webhely használatával](customize-brands-model-with-website.md)
