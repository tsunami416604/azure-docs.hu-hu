---
title: Mi a Bing Autosuggest?
titlesuffix: Azure Cognitive Services
description: Útmutató a Bing Autosuggest API használatához.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 09/12/2017
ms.author: scottwhi
ms.openlocfilehash: c7ac631ded5d781b2d2949d65f6197e194521055
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268923"
---
# <a name="what-is-bing-autosuggest"></a>Mi a Bing Autosuggest?

Ha az alkalmazás lekérdezéseket küld a Bing Search APIs valamelyik, a Bing Autosuggest API segítségével a felhasználók keresési élmény javításához. A Bing Autosuggest API kifejezést a keresőmezőbe a részleges lekérdezési karakterlánc alapján javasolt lekérdezések listáját adja vissza. Megadott karakterek szöveget a keresőmezőbe, mivel javaslatok megjelenítheti egy legördülő listában.

## <a name="bing-autosuggest-api-features"></a>A Bing Autosuggest API-funkciók

| Szolgáltatás                                                                                                                                                                                 | Leírás                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Keresési kifejezések valós idejű felkínálása](concepts/get-suggestions.md) | Javíthatja az alkalmazás: az automatikus kiegészítés API használatával, éppen gépelt javasolt keresési kifejezéseket jeleníti meg. |

## <a name="workflow"></a>Munkafolyamat

A Bing Autosuggest API egy olyan webes RESTful szolgáltatás, könnyen minden programozási nyelvet, amely HTTP-kérelmeket és JSON elemzése, hívja meg. 

1. Hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. A kérés küldése az API-t minden alkalommal, amikor a felhasználó beír egy új karaktert a keresőmezőbe az alkalmazás.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

Általában akkor hívható meg az API-t minden alkalommal, amikor a felhasználó beír egy új karaktert a keresőmezőbe az alkalmazás. Több karakterből álló megadta, mivel az API több megfelelő ajánlott keresési lekérdezések adja vissza. Például előfordulhat, hogy visszaadni a javaslatok API egyetlen `s` valószínűleg kevésbé fontos, mint megjelennek a `sail`.

Az alábbi példa bemutatja egy legördülő keresőmezőbe javasolt lekérdezés feltételeket, a Bing Autosuggest API.

![Autosuggest legördülő keresőmező-lista](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Ha a felhasználó kiválaszt egy javaslatot a legördülő listából, kezdje a keresést egy, a Bing keresési API-k használatával, vagy lépjen közvetlenül a Bing keresési eredmények oldalát.

## <a name="next-steps"></a>További lépések

Annak érdekében, hogy gyorsan nekiláthasson az első kérés létrehozásának, olvassa el az [első lekérdezés létrehozását bemutató](quickstarts/csharp.md) cikket.

Ismerkedjen meg a [Bing Autosuggest API 7-es verziójának](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) referenciájával. A referencia olyan végpontok, fejlécek és lekérdezési paraméterek listáját tartalmazza, amelyekkel a javasolt lekérdezési kifejezéseket és a válaszobjektumok definícióit kérheti le.

További információ az internetes keresésről a [Bing Web Search API](../bing-web-search/search-the-web.md) használatával.

Mindenképpen olvassa el a [Bing használati és megjelenítési előírásait,](./useanddisplayrequirements.md) hogy ne szegje meg a keresési eredmények használatának szabályait.
