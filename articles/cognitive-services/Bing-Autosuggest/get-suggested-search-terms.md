---
title: Mi a Bing Autosuggest?
titleSuffix: Azure Cognitive Services
description: A Bing Autosuggest API visszaadja a javasolt lekérdezések listáját a keresőmező részleges lekérdezési karakterlánca alapján.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 014705cf628aa2d2df43d0964ff843fae09595ac
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352772"
---
# <a name="what-is-bing-autosuggest"></a>Mi a Bing Autosuggest?

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ha az alkalmazás a Bing Search API-k bármelyikére küld lekérdezéseket, a Bing Autosuggest API segítségével javíthatja a felhasználók keresési élményét. A Bing Autosuggest API visszaadja a javasolt lekérdezések listáját a keresőmező részleges lekérdezési karakterlánca alapján. Ahogy a karakterek bekerülnek a keresőmezőbe, a javaslatok a legördülő listában jelennek meg.

## <a name="bing-autosuggest-api-features"></a>Bing Autosuggest API funkciók

| Funkció                                                                                                                                                                                 | Leírás                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Keresési kifejezések valós idejű felkínálása](concepts/get-suggestions.md) | Az automatikus kiegészítési API használatával javíthatja az alkalmazásait, és megjelenítheti a javasolt keresési kifejezéseket. |

## <a name="workflow"></a>Munkafolyamat

A Bing Autosuggest API egy REST-alapú webszolgáltatás, amely bármilyen programozási nyelvről meghívható, amely HTTP-kéréseket tesz elérhetővé, és értelmezi a JSON-t.

1. Hozzon létre egy [Cognitive Services API-fiókot](../cognitive-services-apis-create-account.md), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/free/cognitive-services/).
2. Kérés küldése erre az API-ra minden alkalommal, amikor egy felhasználó új karaktert kér az alkalmazás keresési mezőjében.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

Ezt az API-t általában minden alkalommal meg kell hívnia, amikor a felhasználó új karaktert keres az alkalmazás keresési mezőjében. Ahogy egyre több karakter van megadva, az API több releváns javasolt keresési lekérdezést fog visszaadni. Előfordulhat például, hogy az API által visszaadott javaslatok `s` valószínűleg kevésbé lesznek érvényesek, mint a esetében `sail` .

A következő példa egy legördülő keresőmezőt mutat be a Bing Autosuggest API javasolt lekérdezési kifejezéssel.

![Autosuggest legördülő keresőmező-lista](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Ha a felhasználó egy javaslatot választ ki a legördülő listából, a segítségével megkezdheti a keresést a Bing Search API-k valamelyikével, vagy közvetlenül a Bing Search Results oldalára léphet.

## <a name="next-steps"></a>További lépések

Annak érdekében, hogy gyorsan nekiláthasson az első kérés létrehozásának, olvassa el az [első lekérdezés létrehozását bemutató](quickstarts/csharp.md) cikket.

Ismerkedjen meg a [Bing Autosuggest API 7-es verziójának](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) referenciájával. A referencia olyan végpontok, fejlécek és lekérdezési paraméterek listáját tartalmazza, amelyekkel a javasolt lekérdezési kifejezéseket és a válaszobjektumok definícióit kérheti le.

A többi elérhető API-k megismeréséhez látogasson el a [BING Search API hub oldalára](../bing-web-search/overview.md) .


Megtudhatja, hogyan keresheti meg a weben a [Bing Web Search API](../bing-web-search/overview.md)segítségével, és hogyan böngészhet a többi[Bing Search API-k](../bing-web-search/index.yml).

Mindenképpen olvassa el a [Bing használati és megjelenítési előírásait,](../bing-web-search/use-display-requirements.md) hogy ne szegje meg a keresési eredmények használatának szabályait.