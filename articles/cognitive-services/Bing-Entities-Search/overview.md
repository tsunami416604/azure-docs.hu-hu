---
title: Mi az a Bing Entity Search API?
titlesuffix: Azure Cognitive Services
description: A Bing Entity Search API használatával csomagolja ki, majd keresse meg a vállalatok és a helyek a keresési lekérdezések.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: overview
ms.date: 02/01/2019
ms.author: scottwhi
ms.openlocfilehash: 957a104b8fea7274763f16320cc094ede1f94567
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55871837"
---
# <a name="what-is-bing-entity-search-api"></a>Mi az a Bing Entity Search API?

A Bing Entity Search API egy keresési lekérdezést küld a Bingnek, majd entitásokat és helyeket tartalmazó találatokat kap vissza. A helytalálatok lehetnek éttermek, szállodák vagy egyéb helyi vállalkozások. A Bing helyeket ad vissza, ha a lekérdezés a helyi vállalkozás nevét adja meg, vagy egy vállalkozástípus iránt érdeklődik (például „éttermek a közelben”). A Bing entitásokat ad vissza, ha a lekérdezés közismert személyeket, helyeket (látnivalókat, államokat, országokat stb.) vagy dolgokat ad meg.

|Funkció  |Leírás  |
|---------|---------|
|[Valós idejű keresési javaslatok](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Adja meg a keresési javaslatok, mint egy legördülő lista a felhasználók által beírt jelenhet meg.       | 
| [Entitás Egyértelműsítő](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Több entitás több lehetséges jelentését lekérdezések beolvasása. |
| [Helyek keresése](concepts/search-for-entities.md#find-places) | Keresse meg és információkat ad vissza a helyi üzleteket és entitások  |

## <a name="workflow"></a>Munkafolyamat

A Bing Entity Search API egy olyan webes RESTful szolgáltatás, így könnyen hívása minden programozási nyelvet, amely HTTP-kérelmeket és JSON elemzése. Használhatja a szolgáltatást vagy a REST API-t, vagy az SDK használatával.

1. Hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Egy érvényes keresési lekérdezéssel, az API-kérés küldése.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

* Próbálja ki a [interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) a Bing Entity Search API számára. 
* Gyors használatbavétele az első kérelem, próbálkozzon egy [rövid](quickstarts/csharp.md).
* A [Bing Entity Search API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) szakasz hivatkozhat.
* A [Bing használati és megjelenítési követelményei](./use-display-requirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.
