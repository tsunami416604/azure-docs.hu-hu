---
title: Példák a Bing insights |} A Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Hasznos képadatok a Bing.com oldalon látható példát.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/17/2018
ms.author: scottwhi
ms.openlocfilehash: 927a02b400c7a7b250b315ad5668feb198fd590a
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005689"
---
# <a name="examples-of-bing-insights-usage"></a>A Bing insights használati példák

Ez a szakasz példákat, hogy a Bing lehet, hogy jelenik meg insights a Bing.com tartalmaz.

## <a name="pagesincluding-insight-example"></a>PagesIncluding insight-példa

Az alábbiakban látható a Bing hogyan jeleníthet meg, amelyek közé tartozik a lemezkép. Példa egy hivatkozást jelenít meg az első weblapjára, és lehetővé teszi, hogy a felhasználó ki vagy csukhatja más weblapjait, amely tartalmazza a rendszerkép listáját.

![Többek között a bővített oldalak](./media/pages-including.PNG)


## <a name="shoppingsources-insight-example"></a>ShoppingSources insight-példa

Az alábbi példában látható, hogyan megjelenhet a Bing, a képen látható termékek vásárlási forrás.

![Vásárlási forrás](./media/shopping-sources.PNG)


## <a name="visualsearch-insight-example"></a>VisualSearch insight-példa

A következő bemutatja, hogyan Bing jeleníthet meg vizuálisan hasonló képek (lásd: **kapcsolatos rendszerképek** a példában).

![Vizuálisan hasonló képek](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Receptek insight-példa

Az alábbi példában látható, hogyan megjelenhet a Bing, az a képen látható élelmiszer készült receptek között. A példában lehetővé teszi, hogy a felhasználó tudja, hogy receptek érhető el.

![Beleértve a lapok és receptek](./media/recipes-pages-including.PNG)

 És a hivatkozásra kattintva a receptek nyújt, ha a felhasználó kibővíti a listában.

![Többek között a bővített recept oldalak](./media/expanded-recipes-pages-including.PNG)


## <a name="relatedsearches-insight-example"></a>RelatedSearches insight-példa

Az alábbi példában látható, hogyan megjelenhet a Bing, kapcsolódó keresésekkel a mások által készített képek. Ha a felhasználó rákattint a lemezképet, a felhasználó a Bing.com/images keresési eredmények oldalát az adott kapcsolódó lekérdezés venni.

![Kapcsolódó keresések erre: képek](./media/bordered-related-searches.PNG)


## <a name="entity-insight-example"></a>Entitás insight-példa

Az alábbi példában látható, hogyan Bing lehet, hogy információkat jelenítenek meg az entitás (személy, hely vagy is) az a képen látható. Ha a felhasználó az entitás hivatkozásra kattint, a felhasználó a Bing.com keresési eredmények oldalát az entitás venni.

![Az ábrán látható entitás](./media/entity.PNG)


## <a name="displaying-other-insights-that-the-user-might-explore"></a>Más felhasználó előfordulhat, hogy tárgyaljuk insights megjelenítése

Az alábbi példában látható, hogyan Bing lehet, hogy információkat jelenítenek meg más a lemezképet, a felhasználó tárgyaljuk.

![Más feltárhatja a kép](./media/apple-pie-more-tags.PNG)


## <a name="bounding-boxes-and-hot-spots"></a>Határoló mezőket és a hotspotok

Nem alapértelmezett címkék között megtalálhatók a határolókeret, amely azonosítja a terület hasznos helyhez az ábrán, amelyekre vonatkozik a címke. A határolókeret nem határozza meg a teljes kép, ha a határolókeret használatával hozzon létre egy interaktív terület a lemezképre. A felhasználó rákattinthat az interaktív terület interaktív (vagy téglalap) alatt található a tartalommal kapcsolatos információkat lekérni. Például ha a kép egy nagy módon rendszerképet, az eredményeket tartalmazhat címkék (és határoló mezők) az a képen látható Kellékek például egy erszényes, ékszerek, scarfs, stb. Az alábbi példa bemutatja egy interaktív négyszöge a napszemüvegeket, ahogy az képen látható.

![Határolókeret és interaktív terület](./media/click-to-search.PNG)



## <a name="next-steps"></a>További lépések

Tekintse át a JSON ezekben a példákban mögött, lásd: [insights alapértelmezett](default-insights-tag.md) és [JSON-válasz](overview.md#the-response).

Gyorsan megkezdheti az első kérelmét, tekintse meg a rövid útmutató: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md)





