---
title: Példák a Bing-elemzési adatokra – Bing Vizuális keresés
titleSuffix: Azure Cognitive Services
description: Ez a cikk példákat tartalmaz arra, hogy a Bing Visual Search hogyan használhatja és jelenítheti meg a képelemzéseket Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: df66dbeebb04209921ff91c4b99a14580f026718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111642"
---
# <a name="examples-of-bing-insights-usage"></a>Példák a Bing insights használatára

Ez a cikk példákat tartalmaz arra, hogy a Bing hogyan használhatja és jelenítheti meg a képelemzéseket Bing.com.

## <a name="pagesincluding-insight-example"></a>PagesBeleértve betekintést példa

Az alábbi adatok az első weblapra mutató hivatkozást jelenítik meg, és lehetővé teszik a felhasználó számára a képet tartalmazó más weblapok listájának kibontását és összecsukását:

![Kibontott oldalak, beleértve](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Példa a ShoppingSources insight

Az alábbiakban bemutatjuk, hogy a Bing hogyan jelenítheti meg a képen látható termékek vásárlási forrásait:

![Vásárlási források](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Példa a VisualSearch insight

Az alábbiakban bemutatjuk, hogy a Bing hogyan jeleníthet meg vizuálisan hasonló képeket (lásd: **Kapcsolódó képek** a példában):

![Vizuálisan hasonló képek](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Receptek betekintést példa

Az alábbiakban bemutatjuk, hogy a Bing hogyan jelenítheti meg a képen látható élelmiszer receptjeit. A példa tudatja a felhasználóval, hogy vannak elérhető receptek:

![Receptek és oldalak, beleértve a](./media/recipes-pages-including.PNG)

 És biztosítja a linket a recepteket, amikor a felhasználó kibontja a listát:

![Bővített receptoldalak, beleértve](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelatedSearches insight példa

Az alábbiakban bemutatjuk, hogy a Bing hogyan jelenítheti meg a mások által készített képek kapcsolódó kereséseit. Ha a felhasználó a képre kattint, a rendszer a Bing.com/images keresési eredményoldalra viszi a kapcsolódó lekérdezést.

![Kapcsolódó keresések képek](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Példa entitásbetekintési példa

Az alábbiakban bemutatjuk, hogy a Bing hogyan jelenítheti meg a képen megjelenő entitással (személy, hely vagy dolog) kapcsolatos információkat. Ha a felhasználó az entitáshivatkozásra kattint, a rendszer az entitás Bing.com keresési eredménylapjára kerül:

![A képen látható entitás](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>A felhasználó által felkutatott egyéb elemzések megjelenítése

Az alábbiakban bemutatjuk, hogy a Bing hogyan jeleníthet meg más információkat a felhasználó által felderíteni.

![További információk a képpel kapcsolatban](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Határolódobozok és forró pontok

A nem alapértelmezett címkék tartalmazzák azt a határolókeretet, amely azonosítja a címke által érintett kép érdeklődési területét. Ha a határolókeret nem azonosítja a teljes képet, a határolókeret segítségével hozzon létre egy gyorspontot a képen. A felhasználó a hot spotra kattintva információkat kaphat az adott hot spot (vagy téglalap) alatt található tartalomról. Ha például a kép egy divatos kép, az eredmények tartalmazhatnak címkéket (és határolókereteket) a képen látható kiegészítőkhöz, például pénztárcához, ékszerekhez, sálakhoz és így tovább. A következő példa a képen látható napszemüveg ekszámára egy gyorspont téglalapot mutat be:

![Határolókeret és forrópont](./media/click-to-search.PNG)

## <a name="next-steps"></a>További lépések

Az első kérés megkezdéséhez tekintse meg a rövid útmutatókat: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





