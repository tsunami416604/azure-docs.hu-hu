---
title: Mi az a Bing Videókeresési API?
titlesuffix: Azure Cognitive Services
description: Útmutató videók keresése a weben, a Bing Videókeresési API használatával.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 86722f1a69d2f12ec1689854999db52f0ff8f158
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55569497"
---
# <a name="what-is-the-bing-video-search-api"></a>Mi az a Bing Videókeresési API?

A Bing Video Search API megkönnyíti a videó keresését-képességeket adhat a szolgáltatások és alkalmazások. Felhasználói keresési lekérdezések az API-val küldésével lekérése és hasonló releváns és kiváló minőségű videók megjelenítése [Bing Video](https://www.bing.com/video). Az API-val videókat tartalmazó keresési eredmények. A [Bing Web Search API](../bing-web-search/search-the-web.md) webes tartalmat, beleértve a weblapok, videók, hírek és lemezképek más típusú adhat vissza.

## <a name="bing-video-search-api-features"></a>A Bing Video Search API-funkciók

| Szolgáltatás                                                                                                                                                                                 | Leírás                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Keresési kifejezések valós idejű felkínálása](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | A [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) a begépelés során javaslatokat tesz a keresési kifejezésekre, így könnyebbé teszi az alkalmazások használatát. |
| [Szűrés és a videó korlátozása](concepts/get-videos.md#filtering-videos)                      | A videók szerkesztésével a lekérdezési paraméterek visszaadott szűrése.                                                                                                       |
| [Miniatűrök körülvágása, átméretezése és megjelenítése](resize-and-crop-thumbnails.md)                                                | Szerkesztheti, és a Bing Videókeresési API által visszaadott videók a villámnézet megjelenítése.                                                                                      |
| [Felkapott videók beolvasása](trending-videos.md) | A világ különböző pontjain található a felkapott videók keresése.                                                                                                          |
| [Videó elemzések lekérése](video-insights.md) | Népszerű képek testre szabott keresése a világ minden tájáról.                                                                                                          |

## <a name="workflow"></a>Munkafolyamat

A Bing Video Search API egy olyan RESTful webes szolgáltatás, így könnyen hívása minden programozási nyelvet, amely HTTP-kérelmeket és JSON elemzése. A szolgáltatás a [REST API](csharp.md) vagy az [SDK](video-search-sdk-quickstart.md) segítségével használható.

1. Hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Egy érvényes keresési lekérdezéssel, az API-kérés küldése.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.


## <a name="next-steps"></a>További lépések

A Bing Videókeresési API [interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) bemutatja, hogyan testre szabhatja egy keresési lekérdezést, és kereshet videókat a weben.

Amikor kész meghívni az API-t, hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Használja a [rövid](csharp.md) gyorsan az első API-kérelem használatába.

## <a name="see-also"></a>Lásd még

* A [Bing Video Search API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) referencialapja végpontokat, fejlécek és lekérdezési paraméterek a szabadságigények találatok listáját tartalmazza.

* A [Bing használati és megjelenítési követelményei](./useanddisplayrequirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.