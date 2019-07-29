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
ms.date: 03/27/2019
ms.author: scottwhi
ms.openlocfilehash: 15abbfd8892464f3a2c21a85c07bc5b8e94e47ad
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594703"
---
# <a name="what-is-the-bing-visual-search-api"></a>Mi az a Bing Visual Search API?

A Bing Visual Search API egy rendszerkép elemzéseit adja vissza. Feltölthet egy képet, vagy megadhat egy URL-címet is. Az elemzések vizuálisan hasonló képek, vásárlási források, weblapok, amelyek tartalmazzák a képet, és így tovább. A Bing Visual Search API által visszaadott bepillantások hasonlóak a Bing.com/images-on láthatók számára.

Ha a [Bing Image Search API](../bing-image-search/overview.md)használja, a rendszerkép feltöltése helyett az adott API keresési eredményeiből is betekintési jogkivonatokat használhat a Bing Visual Search.

> [!IMPORTANT]
> Ha a Bing Image Search API segítségével képelemzéseket kap, érdemes lehet átváltani a Bing Visual Search APIra, amely átfogóbb elemzéseket tesz lehetővé.

## <a name="insights"></a>Insights

A következő bepillantást a Bing Visual Search használatával derítheti fel:

| Megállapítások                              | Leírás |
|--------------------------------------|-------------|
| Vizuálisan hasonló képek              | A bemeneti képhez hasonló rendszerképek listája. |
| Vizuálisan hasonló termékek            | Termékek, amelyek vizuálisan hasonlóak a termékhez.            |
| Vásárlási források                     | Helyek, ahol megvásárolhatja a bemeneti képen látható elemeket.            |
| Kapcsolódó keresések                     | A mások által készített vagy a rendszerkép tartalmán alapuló kapcsolódó keresések.            |
| A rendszerképet tartalmazó weblapok     | A bemeneti képet tartalmazó weblapok.            |
| Receptet                              | Azokat a weblapokat, amelyekkel az edény a bemeneti képen látható.            |

Az elemzések mellett Bing Visual Search a bemeneti képből származtatott különböző kifejezéseket (azaz címkéket) adja vissza. A címkék lehetővé teszik a felhasználók számára a rendszerképben található fogalmak megismerését. Ha például a bemeneti rendszerkép egy híres sportoló, akkor az egyik címke lehet a sportoló neve, egy másik címke is lehet sport. Vagy ha a bemeneti rendszerkép egy Apple Pie, a címkék Apple Pie, piték és desszertek lehetnek.

Bing Visual Search az eredmények közé tartoznak a rendszerképekben érdekes régiókhoz tartozó határoló mezők is. Ha például a rendszerkép több hírességet tartalmaz, az eredmények tartalmazhatják az összes felismert hírességhez tartozó határoló mezőket. Ha a Bing felismeri a terméket vagy a ruhát a képen, az eredmény tartalmazhatja a felismert elemhez tartozó határoló mezőt is.

## <a name="workflow"></a>Munkafolyamat

A Bing Visual Search API egy REST-alapú webszolgáltatás, így könnyen hívható bármely programozási nyelvről, amely HTTP-kéréseket tesz elérhetővé, és értelmezi a JSON-t. A szolgáltatáshoz használhatja a REST API vagy az SDK-t is.

1. Hozzon létre egy [Cognitive Services fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Bing Search API-k eléréséhez. Ha nem rendelkezik Azure-előfizetéssel, [ingyenes fiókot is létrehozhat](https://azure.microsoft.com/free/). Az előfizetési kulcsot a fiók létrehozása után, vagy az ingyenes próbaverzió aktiválása után az [Azure](https://azure.microsoft.com/try/cognitive-services/my-apis) -webhelyről szerezheti be a [Azure Portal](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) .
2. Kérelem küldése az API-nak érvényes keresési lekérdezéssel.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

Először próbálja ki a Bing Visual Search API [interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/).
A bemutató azt mutatja be, hogyan lehet gyorsan testreszabni a keresési lekérdezéseket, és a weben képeket kutatni.

Az első kérés gyors megkezdéséhez tekintse meg a rövid útmutatókat: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Lásd még

* A [rendszerképek – Visual Search](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) hivatkozás leírja a végpontokon található definíciókat és információkat, a kérelmek fejléceit, válaszait és lekérdezési paramétereit, amelyek a képalapú keresési eredmények igénylésére használhatók.

* A [BING Search API használata és a megjelenítési követelmények](../bing-web-search/use-display-requirements.md) határozzák meg a tartalom és a Bing Search API-kon keresztül szerzett információk elfogadható felhasználását.
