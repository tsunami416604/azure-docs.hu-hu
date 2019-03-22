---
title: Mik azok a Bing Search APIs?
titleSuffix: Azure Cognitive Services
description: Ez a cikk segítségével ismerje meg a Bing Search APIs, és hogyan engedélyezheti az alkalmazások és szolgáltatások cognitive internetes keresés.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 5a883fcb3533374afbbf946281b6a4a1e9a2912e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903113"
---
# <a name="what-are-the-bing-search-apis"></a>Mik azok a Bing Search APIs?

A Bing Search APIs lehetővé teszi, hogy webes csatlakozó alkalmazásokat és szolgáltatásokat, keresse meg a weblapok, képeket, híreket, helyek és további reklámok nélkül. A Bing Search REST API-k és SDK-k használatával keresési kérelmek küldésével kaphat releváns információt és tartalmat a webes keresések. Ez a cikk segítségével ismerje meg a különböző Bing keresési API-k, és hogyan integrálhatja a kognitív keresés, az alkalmazások és szolgáltatások. Díjszabás és sebesség korlátok közötti API-k eltérőek lehetnek.

## <a name="the-bing-web-search-api"></a>A Bing webes keresési API

A [Bing Web Search API](../Bing-Web-Search/index.yml) weblapok, képek, videó, hírek és további adja vissza. A keresési lekérdezéseket küld az API-hoz bevonhat vagy kizárhat bizonyos tartalomtípusok, szűrheti.

Fontolja meg a Bing Web Search API használatát a kereséséhez kapcsolódó webes tartalom minden típusú igénylő alkalmazásokhoz. Ha az alkalmazás egy adott típusú online tartalom keres, akkor a keresési API-k az alábbi:

## <a name="content-specific-bing-search-apis"></a>Tartalom-specifikus Bing keresési API-k

Az alábbi Bing keresési API-k az internetről, például képeket, híreket, helyi üzletek és videók adott vissza.

| Bing API | Leírás |
| -- | -- |
| [Entitás keresése](../Bing-Entities-Search/index.yml) | A Bing Entity Search API-entitásokkal, amely lehet a személyek, helyek és dolgokat tartalmazó keresési eredmények adja vissza. A lekérdezéstől függően a API-t ad vissza, amelyek megfelelnek a keresési lekérdezés egy vagy több entitás. A keresési lekérdezés méltó egyéni felhasználók számára, helyi üzletek, arcrész, célhelyek és egyéb tartalmazhatnak. |
| [Képkeresés](../Bing-Image-Search/index.yml) | A Bing Image Search API lehetővé teszi a keresse meg és keresés magas színvonalú statikus és animált képeket hasonló [Bing.com/images](https://www.Bing.com/images). Pontosíthatja vagy kizárja a képek attribútum, beleértve a mérete, a színt, a licenc és a frissesség kereséseket. Keresse meg a felkapott képek, tölthet fel képeket a velük kapcsolatos információkhoz juthat, és a villámnézet megjelenítése. |
| [Hírkeresés](../Bing-News-Search/index.yml) | A Bing News Search API lehetővé teszi, hogy hasonló megtalálhatja a legfrissebb híreket [Bing.com/news](https://www.Bing.com/news). Az API több forrásból vagy tartományok hírcikkeket adja vissza. Az első népszerű cikkek, legfontosabb és hírek között kereshet. |
| [Videók keresése](../Bing-Video-Search/index.yml) | Keressen videókat a weben a Bing Video Search API segítségével. Felkapott videók, a kapcsolódó tartalom és a miniatűr villámnézeti beolvasása. |
| [Vizuális keresés](../Bing-visual-search/index.yml) | Töltsön fel egy képet, vagy egy URL-cím használatával szemléletes adatainak beolvasása, mint például a vizuálisan hasonló termékek, a képeket és a kapcsolódó keresések erre. |
 [Helyi üzleti keresése](../bing-local-business-search/index.yml) | A helyi vállalati keresési Bing-API lehetővé teszi, hogy a keresési lekérdezések alapján helyi üzletek ügyfél és a hely információkat találhat az alkalmazások. |

## <a name="the-bing-custom-search-api"></a>A Bing Custom Search API

Az egyéni keresési példányok létrehozását a [Bing Custom Search](../Bing-Custom-Search/index.yml) API lehetővé teszi, hogy hozhat létre, amely csak a tartalom és a témakörök konkrét keresési funkciókat. Például Miután megadta a tartományok, webhelyek és adott weboldalt, amely megkeresi a Bing, a Bing Custom Search fog testre szabni az eredményeket, hogy adott tartalomra. A Bing egyéni automatikus kiegészítés, kép, beépítheti és Video Search API-k további testreszabási a keresési élmény.

## <a name="additional-bing-search-apis"></a>További Bing Search APIs

Az alábbi Bing keresési API-k lehetővé teszik a keresési élmény javításához az egyéb Bing keresési API-k kombinálásával.

| API | Leírás |
| -- | -- |
| [Bing Autosuggest](../Bing-Autosuggest/index.yml) | Javíthatja az alkalmazás keresési funkciót, a Bing Autosuggest API által ajánlott keresések visszaadó valós időben.  |
| [A Bing Statistics](bing-web-stats.md) | Statisztikai Bing analytics biztosít a Bing Search APIs, az alkalmazás használja. A rendelkezésre álló elemzési néhány mennyiségére, a legjobb lekérdezési karakterláncok és a földrajzi eloszlása. |

## <a name="next-steps"></a>További lépések

* A Bing Search API [szolgáltatás díjszabását.](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* A [Bing használati és megjelenítési követelményei](./use-display-requirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.
