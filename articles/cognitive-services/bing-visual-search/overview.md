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
ms.openlocfilehash: 52e3e65fb4f42530373decca547674a28a60f759
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446661"
---
# <a name="what-is-the-bing-visual-search-api"></a>Mi az a Bing Visual Search API?

A Bing Visual Search API egy kép elemzési adatait adja vissza. Feltölthetsz egy képet, vagy megadhatsz egy URL-címet az egyikhez. Az elemzési adatok vizuálisan hasonló képek, vásárlási források, a képet tartalmazó weblapok stb. A Bing Visual Search API által visszaadott elemzések hasonlóak a Bing.com/images. 

Ha a [Bing Image Search API-t](../bing-image-search/overview.md)használja, a rendszerkép feltöltése helyett az adott API keresési eredményeiből származó insight tokeneket is használhat a Bing Visual Search szolgáltatáshoz.

> [!IMPORTANT]
> Ha a Bing Image Search API használatával kap képelemzéseket, érdemes lehet átváltani a Bing Visual Search API-ra, amely átfogóbb elemzéseket biztosít.

## <a name="insights"></a>Insights

A Bing Visual Search segítségével a következő elemzéseket fedezheti fel:

| Elemzések                              | Leírás |
|--------------------------------------|-------------|
| Vizuálisan hasonló képek              | A bemeneti képhez vizuálisan hasonló képek listája. |
| Vizuálisan hasonló termékek            | A bemutatott termékhez vizuálisan hasonló termékek.            |
| Vásárlási források                     | Helyek, ahol meg lehet vásárolni a tétel jelenik meg a bemeneti kép.            |
| Kapcsolódó keresések                     | A mások által végzett vagy a kép tartalmán alapuló kapcsolódó keresések.            |
| A képet tartalmazó weblapok     | A bemeneti képet tartalmazó weblapok.            |
| Receptek                              | Weboldalak, amelyek recepteket tartalmaznak a bemeneti képen látható étel elkészítéséhez.            |
| Entitások                             | Jól ismert emberek, helyek, meg ilyenek. |

Az elemzések mellett a Bing Visual Search a bemeneti képből származó különböző kifejezéseket (azaz címkéket) ad vissza. A címkék lehetővé teszik a felhasználók számára, hogy felfedezzék a képen található fogalmakat. Ha például a bemeneti kép egy híres sportolóé, az egyik címke lehet a sportoló neve, egy másik címke lehet Sport. Vagy ha a bemeneti kép almás pitéből áll, a címkék lehetnek Apple Pie, Pies és Desszertek.

A Bing Vizuális keresési eredményei a kép érdeklődési területeinek határolókereteit is tartalmazzák. Ha például a kép több hírességet tartalmaz, az eredmények tartalmazhatnak határolókereteket az egyes elismert hírességekhez. Ha a Bing felismer egy terméket vagy ruházatot a képen, az eredmény tartalmazhat egy határolókeretet a felismert elemhez.

## <a name="workflow"></a>Munkafolyamat

A Bing Visual Search API egy RESTful webszolgáltatás, amely megkönnyíti a http-kérelmek et és a JSON elemzésére alkalmas programozási nyelvről történő hívását. Használhatja a REST API-t vagy az SDK-t a szolgáltatáshoz.

1. Hozzon létre egy [Cognitive Services-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Bing Search API-k eléréséhez. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy fiókot ingyenesen.](https://azure.microsoft.com/free/) Az előfizetési kulcsot a fiók létrehozása után az [Azure Portalon,](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) vagy az [Azure-webhelyen](https://azure.microsoft.com/try/cognitive-services/my-apis) egy ingyenes próbaverzió aktiválása után.
2. Küldjön egy kérelmet az API-nak egy érvényes keresési lekérdezéssel.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

Először próbálja ki a Bing Visual Search API [interaktív bemutatóját.](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)
A bemutató bemutatja, hogyan szabhatja gyorsan a keresési lekérdezést, és hogyan kutathatja át a képeket az interneten.

Az első kérés gyors megkezdéséhez tekintse meg a rövid útmutatókat: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Lásd még

* A [Képek – Vizuális keresés](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) hivatkozás a végpontokra vonatkozó definíciókat és információkat, a kérelemfejléceket, a válaszokat és a lekérdezési paramétereket ismerteti, amelyek segítségével képalapú keresési eredményeket kérhet.

* A [Bing Search API-k használatára és megjelenítési követelményeire](../bing-web-search/use-display-requirements.md) vonatkozó követelmények határozzák meg a Bing keresési API-kon keresztül szerzett tartalom és információ elfogadható használatát.

* Látogasson el a [Bing Search API-központ oldalára](../bing-web-search/search-the-web.md) a többi elérhető API-k felfedezéséhez.