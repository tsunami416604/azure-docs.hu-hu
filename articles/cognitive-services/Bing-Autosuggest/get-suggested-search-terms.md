---
title: Mi a Bing Autosuggest?
titleSuffix: Azure Cognitive Services
description: A Bing Autosuggest API a keresési mezőben lévő részleges lekérdezési karakterlánc alapján adja vissza a javasolt lekérdezések listáját.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: a90fa0a66fb32b2a885599f09458964188353880
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448837"
---
# <a name="what-is-bing-autosuggest"></a>Mi a Bing Autosuggest?

Ha az alkalmazás lekérdezéseket küld a Bing Search api-k bármelyikének, a Bing automatikus javaslat API-jával javíthatja a felhasználók keresési élményét. A Bing Autosuggest API a keresési mezőben lévő részleges lekérdezési karakterlánc alapján adja vissza a javasolt lekérdezések listáját. A keresőmezőbe karakterek beírásakor a javaslatokat egy legördülő listában jelenítheti meg.

## <a name="bing-autosuggest-api-features"></a>A Bing Autosuggest API szolgáltatásai

| Szolgáltatás                                                                                                                                                                                 | Leírás                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Keresési kifejezések valós idejű felkínálása](concepts/get-suggestions.md) | Az Automatikus javaslat API használatával javíthatja az alkalmazásélmény ét, ha a beírás során megjeleníti a javasolt keresési kifejezéseket. |

## <a name="workflow"></a>Munkafolyamat

A Bing Autosuggest API egy RESTful webszolgáltatás, amely könnyen hívható bármely programozási nyelvről, amely http-kéréseket tud készíteni, és elemezheti a JSON-t. 

1. Hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Küldjön egy kérelmet erre az API-ra minden alkalommal, amikor egy felhasználó új karaktert ad meg az alkalmazás keresőmezőjébe.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

Ezt az API-t általában minden alkalommal meg kell hívnia, amikor a felhasználó új karaktert ad meg az alkalmazás keresőmezőjébe. Ahogy egyre több karaktert ad meg, az API relevánsabb javasolt keresési lekérdezéseket ad vissza. Például a javaslatok at the API `s` might return for a single `sail`are likely to be less relevant than ones for.

A következő példa egy legördülő keresőmezőt mutat be a Bing Autosuggest API javasolt lekérdezési kifejezéseivel.

![Autosuggest legördülő keresőmező-lista](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Amikor egy felhasználó kiválaszt egy javaslatot a legördülő listából, használhatja a bingkeresési API-k egyikével való keresés megkezdéséhez, vagy közvetlenül a Bing keresési eredménylapjára.

## <a name="next-steps"></a>További lépések

Annak érdekében, hogy gyorsan nekiláthasson az első kérés létrehozásának, olvassa el az [első lekérdezés létrehozását bemutató](quickstarts/csharp.md) cikket.

Ismerkedjen meg a [Bing Autosuggest API 7-es verziójának](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) referenciájával. A referencia olyan végpontok, fejlécek és lekérdezési paraméterek listáját tartalmazza, amelyekkel a javasolt lekérdezési kifejezéseket és a válaszobjektumok definícióit kérheti le.

Látogasson el a [Bing Search API-központ oldalára](../bing-web-search/search-the-web.md) a többi elérhető API-k felfedezéséhez.


Megtudhatja, hogy miként kereshet az interneten a [Bing Web Search API használatával,](../bing-web-search/search-the-web.md)és hogyan fedezheti fel a többi Bing Keresési[API-t.](../bing-web-search/index.yml)

Mindenképpen olvassa el a [Bing használati és megjelenítési előírásait,](./useanddisplayrequirements.md) hogy ne szegje meg a keresési eredmények használatának szabályait.
