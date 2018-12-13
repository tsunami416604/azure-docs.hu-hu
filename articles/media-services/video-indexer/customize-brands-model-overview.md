---
title: A Video Indexer – Azure márkái modell testreszabása
titlesuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt a Video Indexer márkái modell mi és hogyan szabható testre.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 7df709adbd8e45712c112b52fc76920f8b67fe91
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284828"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>A Video Indexer márkái modell testreszabása

A video Indexer márka észlelését a beszéd- és vizuális szöveges indexelést és a video- és audiotartalmak újraindexelés során támogatja. A márka duplikálásészlelési szolgáltatását a termékek, szolgáltatások, hírforrásaiból azonosítja, és a vállalatok a Bing márkái adatbázis által javasolt. Például ha egy videó vagy hang tartalom említett, a Microsoft vagy egy videóban visual szöveg megjelenik, Video Indexer észleli ezt, a tartalom egy adott márkához. Márkái más feltételek környezetet használja a rendszer használatát.

Márka észlelési hasznos számos különböző üzleti felhasználási helyzetek, például az archívum tartalmát és a felderítési, környezetfüggő hirdetési, közösségi média elemzése, kereskedelmi versenyeznek az elemzési és sok más. Video Indexer márka észlelési lehetővé teszi index márka említései a beszéd- és vizuális szöveg, Bing márkái adatbázist használ, valamint a testreszabási létrehozásával egy egyéni márkái modellt használ az egyes Video Indexer-fiókot. Az egyéni márkái modell funkció lehetővé teszi-e a Video Indexer a Bing márkái az adatbázis egyes legyenek márkái észlelt (lényegében létrehozásakor márkái fekete listája) kizárása márkái érzékeli, és márkákat, amelyeket részét kell tartalmaznia válassza a modell, amely nem feltétlenül Bing márkái adatbázisban (lényegében márkái fehér lista létrehozásával).

## <a name="out-of-the-box-detection-example"></a>Kívüli az észlelés – példa

Az a [2. a Microsoft Build 2017 napon](https://www.videoindexer.ai/media/ed6ede78ad/) bemutató, a márka, "A Microsoft Windows" többször jelenik meg. Egyes esetekben az időnként visual szövegként, és soha nem szó, átirat. A video Indexer nagy pontosságú észlel, győződjön meg arról, hogy egy kifejezés valójában a márka alapján a környezetet, több mint 90k kiterjedő márkái beépített és folyamatosan frissítése. A Video Indexer észleli a márka, a beszéd 02:25-kor, és majd újra, 02:40 visual szöveg, amely része a windows-embléma.

![Márkái áttekintése](./media/content-model-customization/brands-overview.png)

Windows konstrukció kontextusában szó, és nem észleli a Box, az Apple, Fox, stb., egy adott márka, valamint azonos "Windows" szót tudja, hogyan kell értelmezni környezetéből fejlett gépi tanulási algoritmusok alapján. A támogatott nyelvek márka észlelési működik. Ide kattintva [teljes 2. a Microsoft Build 2017 napon nyitó előadás és index](http://www.videoindexer.ai/media/ed6ede78ad/).

Ahhoz, hogy a saját márkákat, tekintse meg [további lépések](#next-stpes).

## <a name="next-steps"></a>További lépések

[API-k használatával márkái modell testreszabása](customize-brands-model-with-api.md)

[A webhelyen márkái modell testreszabása](customize-brands-model-with-website.md)
