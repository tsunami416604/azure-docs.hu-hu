---
title: Mi a Bing Visual Search?
titleSuffix: Azure Cognitive Services
description: A Bing Visual Search olyan részleteket vagy megállapításokat biztosít egy képpel kapcsolatban, mint a hasonló képek vagy a vásárlási lehetőségek.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 61a851b0efbcc4fdb55308e47447d218014ef9e0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154354"
---
# <a name="what-is-the-bing-visual-search-api"></a>Mi az a Bing Visual Search API?

A Bing Visual Search API hasonló képadatokat biztosít, mint a Bing.com/images. Kép feltöltése, vagy egy URL-címet biztosít, az API-t azonosíthatja részleteit, köztük a vizuálisan hasonló képek különböző működtet, amelyek közé tartozik a lemezkép és egyéb forrásokból. Ha használja a [a Bing Image Search API](../bing-image-search/overview.md), csatolt kép feltöltése helyett az API-k keresési eredmények insight-jogkivonatokkal.

## <a name="insights"></a>Insights

Az insights vizuális keresés segítségével megkeresheti a következők:

| Megállapítások                              | Leírás |
|--------------------------------------|-------------|
| Vizuálisan hasonló képek              | A bemeneti kép vizuálisan hasonló képek egy listája. |
| Vizuálisan hasonló termékek            | Olyan termékhez, melyek a vizuálisan hasonló látható termékre.            |
| Vásárlási forrás                     | Helyek, ahol vásárolhat az elemet a bemeneti ábrán látható.            |
| Kapcsolódó keresések                     | Kapcsolódó keresések mások vagy az, hogy a kép tartalma alapulnak.            |
| A lemezképet tartalmazó weblapokra     | A bemeneti kép tartalmazó weblapjait.            |
| Receptek                              | Weblapok, hogy a bemeneti képen látható az étel receptet tartalmazó            |

A megállapításokon kívül a Visual Search egy, a bemeneti képből kinyert kifejezéskészletet (címkék) is visszaad. Ezek a címkék lehetővé teszik a felhasználók számára, hogy felfedezzék a képhez kapcsolódó fogalmakat. Például ha a bemeneti kép egy híres sportolót ábrázol, az egyik címke lehet a sportoló neve, egy másik pedig a Sport. Vagy ha a bemeneti képen egy almás pite látható, az Almás pite, a Piték és a Desszertek mind lehetséges címkék, amelyek lehetővé teszik, hogy a felhasználók felfedezzék a kapcsolódó fogalmakat.

A Visual Search eredményei határoló kereteket is tartalmazhatnak a kép érdeklődésre számot tartó tartományaihoz. Például ha a képen több híresség is szerepel, az eredmények határoló keretet tartalmazhatnak a képen látható minden egyes hírességhez. Vagy ha a Bing felismer egy képen látható terméket vagy ruhát, az eredmény határoló keretet tartalmazhat a felismert termékhez vagy ruházati elemhez.

> [!IMPORTANT]
> Ha a Bing Image Search API használatával képadatok, fontolja meg az áttérést a Bing Visual Search API, amely átfogóbb elemzéseket biztosít.

## <a name="workflow"></a>Munkafolyamat

A Bing Visual Search API egy olyan webes RESTful szolgáltatás, így könnyen hívása minden programozási nyelvet, amely HTTP-kérelmeket és JSON elemzése. Használhatja a szolgáltatást vagy a REST API-t, vagy az SDK használatával.

1. Hozzon létre egy Cognitive Services API-fiókot a Bing Search APIs a hozzáférést. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen létrehozhat egy fiókot.
2. Egy érvényes keresési lekérdezéssel, az API-kérés küldése.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.


## <a name="next-steps"></a>További lépések

Először próbálja ki a Bing Image Search API [interaktív bemutatóját](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/).
Ez bemutatja, hogyan lehet gyorsan testre szabni a keresési lekérdezéseket és felkutatni a webes képeket.

Amikor kész meghívni az API-t, hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Gyorsan megkezdheti az első kérelmét, tekintse meg a rövid útmutató: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).


## <a name="see-also"></a>Lásd még

* A [Bing Visual Search referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) dokumentációja tartalmaz definíciókat és a végpontok, a fejlécek, az API-válaszok és a lekérdezési paraméterek segítségével kérelem lemezkép-alapú keresési eredmények információkat.

* A [Bing használati és megjelenítési követelményei](./use-and-display-requirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.
