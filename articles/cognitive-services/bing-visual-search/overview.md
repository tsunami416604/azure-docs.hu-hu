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
ms.openlocfilehash: 8bcb0372ebb60ac3a46cf06bf85322b288e153ba
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630210"
---
# <a name="what-is-the-bing-visual-search-api"></a>Mi az a Bing Visual Search API?

A Bing Visual Search API a képhez insights adja vissza. Töltsön fel egy képet, vagy adjon meg egy URL-címet. Insights vizuálisan hasonló képek, a vásárlási forrás, a weblapok, például a lemezkép és a további. A Bing Visual Search API által visszaadott insights hasonlóak kiépítettektől Bing.com/images jelenik meg.

Ha használja a [a Bing Image Search API](../bing-image-search/overview.md), helyett kép feltöltése a Bing Visual Search, hogy API-k a keresési eredmények insight-jogkivonatokat is használhat.

> [!IMPORTANT]
> Ha a Bing Image Search API használatával képadatok, fontolja meg az áttérést a Bing Visual Search API, amely átfogóbb elemzéseket biztosít.

## <a name="insights"></a>Insights

A Bing Visual Search használatával is felderítheti, a következő információkat:

| Megállapítások                              | Leírás |
|--------------------------------------|-------------|
| Vizuálisan hasonló képek              | A bemeneti kép vizuálisan hasonló képek egy listája. |
| Vizuálisan hasonló termékek            | Olyan termékhez, melyek a vizuálisan hasonló látható termékre.            |
| Vásárlási forrás                     | Helyek, ahol vásárolhat az elemet a bemeneti ábrán látható.            |
| Kapcsolódó keresések                     | Kapcsolódó keresések mások vagy az, hogy a kép tartalma alapulnak.            |
| Amelyek közé tartozik a lemezkép     | A bemeneti kép tartalmazó weblapjait.            |
| Receptek                              | Például, hogy a bemeneti képen látható az étel receptek weblapokat.            |

Információkhoz a Bing vizuális keresési feltételek (vagyis a címkék) származik a bemeneti kép különböző adja vissza. A címkék engedélyezze a felhasználóknak, hogy a lemezképben található fogalmak. Például ha a bemeneti kép híres sportoló, a címkék egyikét lehet a athlete nevét, egy másik címke lehet vagy sporttal kapcsolatosak. Vagy, ha a bemeneti kép egy apple torta, a címkék Apple torta tortadiagram és édességeket választottam.

A Bing vizuális keresési eredmények határoló hasznos helyhez az ábrán régiók mezők is. Például ha a lemezképet tartalmaz számos hírességek, az eredmények tartalmazhat határoló mezőkbe a felismert hírességek mindegyikéhez. Vagy, a Bing egy termék vagy a kép ruházati észleli, ha az eredmény tartalmazhat egy határolókeret a felismert elemhez.

## <a name="workflow"></a>Munkafolyamat

A Bing Visual Search API egy olyan webes RESTful szolgáltatás, így könnyen hívása minden programozási nyelvet, amely HTTP-kérelmeket és JSON elemzése. A szolgáltatás a REST API vagy az SDK-t használhat.

1. Hozzon létre egy [Cognitive Services-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Bing keresési API-k eléréséhez. Ha nem rendelkezik Azure-előfizetéssel, akkor [-fiók létrehozása ingyen](https://azure.microsoft.com/free/). Megtekintheti az előfizetési kulcs, a [az Azure portal](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-apis-create-account#access-your-resource) a fiók létrehozását követően vagy [Azure-webhelyen](https://azure.microsoft.com/try/cognitive-services/my-apis) ingyenes próbaverzió aktiválása után.
2. Az API-t egy érvényes keresési lekérdezést egy kérés küldése.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

Először próbálja meg a Bing Visual Search API [interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/).
A bemutató bemutatja, hogyan lehet gyorsan testre szabhatja a keresési lekérdezés és a webes képkeresésekben.

Gyorsan megkezdheti az első kérelmét, tekintse meg a rövid útmutató: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Lásd még

* A [rendszerképek – vizuális keresés](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) leírása ismertet a definíciók és a végpont, a kérelem fejlécei, válaszokat, és a keresési eredmények, amelyek segítségével a kérelem lemezkép-alapú lekérdezési paraméterek.

* A [Bing keresési API-t használja, és megjelenítési követelményeihez](../bing-web-search/use-display-requirements.md) adja meg a tartalom és a Bing search API-k által gyűjtött információk elfogadható használja.
