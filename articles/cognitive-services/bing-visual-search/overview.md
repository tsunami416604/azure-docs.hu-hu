---
title: Mi az a Bing Visual Search API?
titleSuffix: Azure Cognitive Services
description: A Bing Visual Search olyan részleteket vagy megállapításokat biztosít egy képpel kapcsolatban, mint a hasonló képek vagy a vásárlási lehetőségek.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 12/19/2019
ms.author: scottwhi
ms.openlocfilehash: 7dfc704fb38550993adb7835d4500dee890117a8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486980"
---
# <a name="what-is-the-bing-visual-search-api"></a>Mi az a Bing Visual Search API?

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing Visual Search API egy rendszerkép elemzéseit adja vissza. Feltölthet egy képet, vagy megadhat egy URL-címet is. Az elemzések vizuálisan hasonló képek, vásárlási források, weblapok, amelyek tartalmazzák a képet, és így tovább. A Bing Visual Search API által visszaadott bepillantások hasonlóak a Bing.com/images-on láthatók számára. 

Ha a [Bing Image Search API](../bing-image-search/overview.md)használja, a rendszerkép feltöltése helyett az adott API keresési eredményeiből is betekintési jogkivonatokat használhat a Bing Visual Search.

> [!IMPORTANT]
> Ha a Bing Image Search API segítségével képelemzéseket kap, érdemes lehet átváltani a Bing Visual Search APIra, amely átfogóbb elemzéseket tesz lehetővé.

## <a name="insights"></a>Insights

A következő bepillantást a Bing Visual Search használatával derítheti fel:

| Elemzések                              | Description |
|--------------------------------------|-------------|
| Vizuálisan hasonló képek              | A bemeneti képhez hasonló rendszerképek listája. |
| Vizuálisan hasonló termékek            | Termékek, amelyek vizuálisan hasonlóak a termékhez.            |
| Vásárlási források                     | Helyek, ahol megvásárolhatja a bemeneti képen látható elemeket.            |
| Kapcsolódó keresések                     | A mások által készített vagy a rendszerkép tartalmán alapuló kapcsolódó keresések.            |
| A rendszerképet tartalmazó weblapok     | A bemeneti képet tartalmazó weblapok.            |
| Receptek                              | Azokat a weblapokat, amelyekkel az edény a bemeneti képen látható.            |
| Entitások                             | Jól ismert személyek, helyek és dolgok. |

Az elemzések mellett Bing Visual Search a bemeneti képből származtatott különböző kifejezéseket (azaz címkéket) adja vissza. A címkék lehetővé teszik a felhasználók számára a rendszerképben található fogalmak megismerését. Ha például a bemeneti rendszerkép egy híres sportoló, akkor az egyik címke lehet a sportoló neve, egy másik címke is lehet sport. Vagy ha a bemeneti rendszerkép egy Apple Pie, a címkék Apple Pie, piték és desszertek lehetnek.

Bing Visual Search az eredmények közé tartoznak a rendszerképekben érdekes régiókhoz tartozó határoló mezők is. Ha például a rendszerkép több hírességet tartalmaz, az eredmények tartalmazhatják az összes felismert hírességhez tartozó határoló mezőket. Ha a Bing felismeri a terméket vagy a ruhát a képen, az eredmény tartalmazhatja a felismert elemhez tartozó határoló mezőt is.

## <a name="workflow"></a>Munkafolyamat

A Bing Visual Search API egy REST-alapú webszolgáltatás, így könnyen hívható bármely programozási nyelvről, amely HTTP-kéréseket tesz elérhetővé, és értelmezi a JSON-t. A szolgáltatáshoz használhatja a REST API vagy az SDK-t is.

1. Hozzon létre egy [Cognitive Services fiókot](../cognitive-services-apis-create-account.md) a Bing Search API-k eléréséhez. Ha nem rendelkezik Azure-előfizetéssel, [ingyenes fiókot is létrehozhat](https://azure.microsoft.com/free/cognitive-services/).
2. Kérelem küldése az API-nak érvényes keresési lekérdezéssel.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

Először próbálja ki a Bing Visual Search API [interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/).
A bemutató azt mutatja be, hogyan lehet gyorsan testreszabni a keresési lekérdezéseket, és a weben képeket kutatni.

Az első kérés gyors megkezdéséhez tekintse meg a rövid útmutatókat:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)

## <a name="see-also"></a>Lásd még

* A [rendszerképek – Visual Search](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) hivatkozás leírja a végpontokon található definíciókat és információkat, a kérelmek fejléceit, válaszait és lekérdezési paramétereit, amelyek a képalapú keresési eredmények igénylésére használhatók.

* A [BING Search API használata és a megjelenítési követelmények](../bing-web-search/use-display-requirements.md) határozzák meg a tartalom és a Bing Search API-kon keresztül szerzett információk elfogadható felhasználását.

* A többi elérhető API-k megismeréséhez látogasson el a [BING Search API hub oldalára](../bing-web-search/overview.md) .