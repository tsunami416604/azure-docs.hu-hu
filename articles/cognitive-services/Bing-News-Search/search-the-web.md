---
title: Mi az a Bing News Search API?
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan több kategóriákban, beleértve a hírek és témakörök népszerű aktuális hírek a webes keresés a Bing News Search API használatával.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: overview
ms.date: 01/10/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 32c18508d07fc911366ffc77ebe347efd3c1b6fa
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261451"
---
# <a name="what-is-the-bing-news-search-api"></a>Mi az a Bing News Search API?

A Bing News Search API megkönnyíti a Bing cognitive news keresési képességek integrálása az alkalmazásokba. Az API-t a hasonló élményt nyújt [Bing hírek](https://www.bing.com/news), ami lehetővé teszi a keresési lekérdezéseket küldenek, és megfelelő hírcikkeket fogadásához.

Vegye figyelembe, hogy a Bing News Search API hírkeresési eredményeket csak biztosít-e. Használja a [Bing Web Search API](../bing-web-search/search-the-web.md), [Video Search API](../bing-video-search/search-the-web.md) és [Image Search API](../bing-image-search/overview.md) webes tartalmat más típusú.

## <a name="bing-news-search-api-features"></a>A Bing News Search API-funkciók

Bár a Bing News Search API elsősorban talál, és adja vissza a kapcsolódó hírek, számos funkciót biztosít a intelligens és összpontosítás hírek lekéréséhez a weben.

|Szolgáltatás  |Leírás  |
|---------|---------|
|[Javasolhat, és a keresési feltételek használata](concepts/search-for-news.md#suggest-and-use-search-terms)     | A keresési élmény javításához használatával a [a Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) a javasolt keresési kifejezéseket, éppen gépelt megjelenítéséhez.         |
|[Általános hírek](concepts/search-for-news.md#get-general-news)     | Hírek keresése egy keresési lekérdezést küld a Bing News Search API, és visszaállítani híreket cikkek listáját.           |
|[A mai híreket](concepts/search-for-news.md#get-todays-top-news)      | Szerezze be a legfrissebb híreket a nap minden kategóriák között.       |
|[Hírek kategória szerint](concepts/search-for-news.md)     | Adott kategóriákból hírek keresése.        | 
|[Hírek](concepts/search-for-news.md)     | Keresse meg a főcímeket összes kategóriák között.         |

## <a name="workflow"></a>Munkafolyamat

A Bing News Search API egy olyan webes RESTful szolgáltatás, így könnyen hívása minden programozási nyelvet, amely HTTP-kérelmeket és JSON elemzése. Használhatja a szolgáltatást vagy a REST API-t, vagy az SDK használatával.

1. Hozzon létre egy Cognitive Services API-fiókot a Bing Search APIs a hozzáférést. Ha nem rendelkezik Azure-előfizetéssel, akkor [-fiók létrehozása ingyen](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api).

2. Egy érvényes keresési lekérdezéssel, az API-kérés küldése.

3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

Először próbálja meg a [interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) a Bing News Search API számára. Ez a bemutató bemutatja, hogyan lehet gyorsan keresési lekérdezés testreszabása és hírek keresése a weben.

Gyorsan elsajátíthatja az első API-kérelem, próbálja meg a gyors útmutató: a [REST API](quickstart.md) vagy az egyik a [SDK-k](sdk.md).

## <a name="see-also"></a>Lásd még

* A [Bing News Search API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) referenciaszakasz tartalmaz definíciókat és a végpontok, a fejlécek, az API-válaszok és a lekérdezési paraméterek segítségével kérelem lemezkép-alapú keresési eredmények információkat.

* A [Bing használati és megjelenítési követelményei](./useanddisplayrequirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.
