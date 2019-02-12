---
title: A Video Indexer – Azure márkái modell testreszabása
titlesuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt a Video Indexer márkái modell mi és hogyan szabható testre.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: b01de55003f620363ed27b95a2bd27bc39fdee3a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991209"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>A Video Indexer márkái modell testreszabása

A video Indexer márka észlelését a beszéd- és vizuális szöveges indexelést és a video- és audiotartalmak újraindexelés során támogatja. A márka duplikálásészlelési szolgáltatását a termékek, szolgáltatások, hírforrásaiból azonosítja, és a vállalatok a Bing márkái adatbázis által javasolt. Például ha egy videó vagy hang tartalom említett, a Microsoft vagy egy videóban visual szöveg megjelenik, Video Indexer észleli ezt, a tartalom egy adott márkához. Márkái más feltételek környezetet használja a rendszer használatát.

Márka észlelési hasznos számos különböző üzleti felhasználási helyzetek, például az archívum tartalmát és a felderítési, környezetfüggő hirdetési, közösségi média elemzése, kereskedelmi versenyeznek az elemzési és sok más. Video Indexer márka észlelési lehetővé teszi index márka említései a beszéd- és vizuális szöveg, Bing márkái adatbázist használ, valamint a testreszabási létrehozásával egy egyéni márkái modellt használ az egyes Video Indexer-fiókot. Az egyéni márkái modell funkció lehetővé teszi-e a Video Indexer a Bing márkái az adatbázis egyes legyenek márkái észlelt (lényegében létrehozásakor márkái fekete listája) kizárása márkái érzékeli, és márkákat, amelyeket részét kell tartalmaznia válassza a modell, amely nem feltétlenül Bing márkái adatbázisban (lényegében márkái fehér lista létrehozásával).

## <a name="out-of-the-box-detection-example"></a>Kívüli az észlelés – példa

Az a [2. a Microsoft Build 2017 napon](https://www.videoindexer.ai/media/ed6ede78ad/) bemutató, a márka, "A Microsoft Windows" többször jelenik meg. Egyes esetekben az időnként visual szövegként, és soha nem szó, átirat. A video Indexer nagy pontosságú észlel, győződjön meg arról, hogy egy kifejezés valójában a márka alapján a környezetet, több mint 90k kiterjedő márkái beépített és folyamatosan frissítése. A Video Indexer észleli a márka, a beszéd 02:25-kor, és majd újra, 02:40 visual szöveg, amely része a windows-embléma.

![Márkái áttekintése](./media/content-model-customization/brands-overview.png)

Windows konstrukció kontextusában szó, és nem észleli a Box, az Apple, Fox, stb., egy adott márka, valamint azonos "Windows" szót tudja, hogyan kell értelmezni környezetéből fejlett gépi tanulási algoritmusok alapján. A támogatott nyelvek márka észlelési működik. Ide kattintva [teljes 2. a Microsoft Build 2017 napon nyitó előadás és index](http://www.videoindexer.ai/media/ed6ede78ad/).

Ahhoz, hogy a saját márkákat, ismerje meg a következő lépéseket.

## <a name="next-steps"></a>További lépések

[API-k használatával márkái modell testreszabása](customize-brands-model-with-api.md)

[A webhelyen márkái modell testreszabása](customize-brands-model-with-website.md)
