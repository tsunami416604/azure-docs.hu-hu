---
title: Bing insights példái |} Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Példák a Bing.com megjelenített kép elemzések mutatja.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/17/2018
ms.author: scottwhi
ms.openlocfilehash: 102bd0e916491738d74956c209829008d779bcbf
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348703"
---
# <a name="examples-of-bing-insights-usage"></a>Bing insights használati példák

Ebben a szakaszban található példák bemutatják hogyan Bing előfordulhat, hogy meg elemzések a Bing.com.

## <a name="pagesincluding-insight-example"></a>PagesIncluding insight – példa

Az alábbi példában látható, hogyan jelenjen meg a Bing előfordulhat, hogy azon weblapjaira, amelyek közé tartozik a lemezképet. Példa egy hivatkozást az első képernyőn látható weblapon jelenít meg, és lehetővé teszi a felhasználó, bontsa ki és csukja össze a más webhelyek, amelyek tartalmazzák a lemezkép listája.

![Beleértve a bővített lap](./media/pages-including.PNG)


## <a name="shoppingsources-insight-example"></a>ShoppingSources insight – példa

Az alábbi példában látható, hogyan jelenjen meg a Bing előfordulhat, hogy az ábrán látható termékeknél vásárlásra szolgáló adatforrások.

![Vásárlás források](./media/shopping-sources.PNG)


## <a name="visualsearch-insight-example"></a>VisualSearch insight – példa

A következő bemutatja, hogyan Bing előfordulhat, hogy képeket jeleníthessen meg vizuálisan hasonló (lásd: **képek kapcsolódó** a példában).

![Vizuálisan hasonló lemezképek](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Receptet insight – példa

Az alábbi példában látható, hogyan jelenjen meg a Bing előfordulhat, hogy az ábrán látható a használati receptet. A példa lehetővé teszi, hogy a felhasználó tudja, hogy receptet érhető el.

![Beleértve a lapok és receptet](./media/recipes-pages-including.PNG)

 És a hivatkozásra kattintva a receptet biztosít, amikor a felhasználó bővíti a listában.

![Bővített módszereivel lapok többek között](./media/expanded-recipes-pages-including.PNG)


## <a name="relatedsearches-insight-example"></a>RelatedSearches insight – példa

Az alábbi példában látható, hogyan jelenjen meg a Bing előfordulhat, hogy mások által létrehozott lemezképek kapcsolódó kereséseket. Ha a felhasználó a lemezképet, a felhasználó számára, hogy a kapcsolódó lekérdezéshez Bing.com/images keresési eredmények oldalának használatban van.

![Lemezképek kapcsolódó keres](./media/bordered-related-searches.PNG)


## <a name="entity-insight-example"></a>Entitás insight – példa

Az alábbi példában látható, hogyan jelenjen meg a Bing előfordulhat, hogy az ábrán látható entitás (személy, hely vagy dolog) adatait. Ha a felhasználó az entitás hivatkozásra kattint, a felhasználó a Bing.com keresési eredmények oldalának entitás végrehajtott.

![Entitás ábrának](./media/entity.PNG)


## <a name="displaying-other-insights-that-the-user-might-explore"></a>Amely a felhasználó előfordulhat, hogy néhány más insights megjelenítése

Az alábbi példában látható, hogyan jelenjen meg a Bing előfordulhat, hogy a lemezképet, a felhasználó felfedezheti egyéb adatait.

![A kép más észrevételeket felfedezés](./media/apple-pie-more-tags.PNG)


## <a name="bounding-boxes-and-hot-spots"></a>Határoló mezőket és interaktív területek

Nem alapértelmezett címkék közé tartozik a határolókeret, amely azonosítja a lemezképet a címke alkalmazó érdeklődik területén. A határolókeret nem határozza meg a teljes kép, ha a határolókeret használatával hozzon létre egy interaktív terület a lemezképre. A felhasználók rákattinthatnak a interaktív terület az interaktív terület (vagy téglalap) alatt található tartalommal kapcsolatos információkért. Például, ha a kép egy nagy a módon lemezképet, az eredmények címkék (és tartalmazhatnak határoló mezőkbe) a Kellékek, az ábrán látható például egy erszényes, ékszereket, scarfs, stb. A következő példa bemutatja egy interaktív terület négyszöge a sunglasses az ábrán látható.

![Határolókeret és interaktív terület](./media/click-to-search.PNG)



## <a name="next-steps"></a>További lépések

Tekintse meg a JSON ezekben a példákban mögött, lásd: [insights alapértelmezett](default-insights-tag.md) és [JSON-válasz](overview.md#the-response).

Használatának gyors megkezdése az első kérelem, tekintse meg a quickstarts: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md)





