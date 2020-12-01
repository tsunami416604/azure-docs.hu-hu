---
title: Mi a Bing Entity Search API?
titleSuffix: Azure Cognitive Services
description: További információ a Bing Entity Search APIről, valamint a keresési lekérdezésekből származó entitások és helyek kinyeréséről és kereséséről.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 2a3d971ce9a4f89555eb3ffa489f8b19172a4b83
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351469"
---
# <a name="what-is-bing-entity-search-api"></a>Mi az Bing Entity Search API?

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing Entity Search API egy keresési lekérdezést küld a Bingnek, majd entitásokat és helyeket tartalmazó találatokat kap vissza. A helytalálatok lehetnek éttermek, szállodák vagy egyéb helyi vállalkozások. A Bing helyeket ad vissza, ha a lekérdezés a helyi vállalkozás nevét adja meg, vagy egy vállalkozástípus iránt érdeklődik (például „éttermek a közelben”). A Bing visszaadja az entitásokat, ha a lekérdezés jól ismert személyeket, helyeket (turisztikai látványosságokat, államokat, országokat/régiókat stb.) vagy dolgokat ad meg.

|Funkció  |Leírás  |
|---------|---------|
|[Valós idejű keresési javaslatok](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Olyan keresési javaslatokat adhat meg, amelyek legördülő listaként jeleníthetők meg a felhasználók típusaként.       | 
| [Alapentitás](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Több entitás beolvasása több lehetséges jelentéssel rendelkező lekérdezésekhez. |
| [Helyek keresése](concepts/search-for-entities.md#find-places) | Információk keresése és visszaküldése a helyi vállalkozások és entitások számára  |

## <a name="workflow"></a>Munkafolyamat

A Bing Entity Search API egy REST-alapú webszolgáltatás, így könnyen hívható bármely programozási nyelvről, amely HTTP-kéréseket tesz elérhetővé, és értelmezi a JSON-t. A szolgáltatás a REST API vagy az SDK segítségével használható.

1. Hozzon létre egy [Cognitive Services API-fiókot](../cognitive-services-apis-create-account.md), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/free/cognitive-services/).
2. Küldjön egy kérést az API-ra egy érvényes keresési lekérdezéssel.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

* Próbálja ki a Bing Entity Search API [interaktív bemutatóját](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) . 
* Az első kérés gyors megkezdéséhez próbáljon [ki egy rövid](quickstarts/csharp.md)útmutatót.
* A [Bing Entity Search API v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) -hivatkozás szakasza.
* A [Bing használati és megjelenítési követelményei](../bing-web-search/use-display-requirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.
* A többi elérhető API-k megismeréséhez látogasson el a [BING Search API hub oldalára](../bing-web-search/overview.md) .