---
title: Mik azok a Bing Search APIs?
titleSuffix: Azure Cognitive Services
description: Ez a cikk segítségével ismerje meg a Bing Search APIs, és hogyan engedélyezheti az alkalmazások és szolgáltatások cognitive internetes keresés.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: b2277f540b076307fe74c57068ff288860f82796
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513116"
---
# <a name="what-are-the-bing-search-apis"></a>Mik azok a Bing Search APIs?

A Bing Search APIs lehetővé teszi webes csatlakozó alkalmazásokat és szolgáltatásokat, keresse meg a weblapok, képeket, híreket, helyek és további reklámok nélkül hozhat létre. A Bing Search REST API-k és SDK-k használatával keresési kérelmek küldésével kaphat releváns információt és tartalmat a webes keresések. Ez a cikk segítségével ismerje meg a különböző Bing keresési API-k, és hogyan integrálhatja a kognitív keresés, az alkalmazások és szolgáltatások. Díjszabás és sebesség korlátok közötti API-k eltérőek lehetnek.

## <a name="the-bing-web-search-api"></a>A Bing webes keresési API

A [Bing Web Search API](../Bing-Web-Search/index.yml) weblapok, képek, videó, hírek és további adja vissza. A keresési lekérdezések küldött az API-hoz bevonhat vagy kizárhat bizonyos tartalomtípusok szűrésével.

Fontolja meg a Bing Web Search API használatát a kereséséhez kapcsolódó webes tartalom minden típusú igénylő alkalmazásokhoz. Ha az alkalmazás egy adott típusú online tartalom keres, akkor a keresési API-k az alábbi: 

## <a name="content-specific-bing-search-apis"></a>Tartalom-specifikus Bing keresési API-k

Az alábbi Bing keresési API-k például képeket, híreket, helyi üzletek és videókat a weben adott vissza.

| Bing API | Leírás |
| -- | -- | 
| [Entitás keresése](../Bing-Entities-Search/index.yml) | A Bing Entity Search API-entitásokkal, amely lehet a személyek, helyek és dolgokat tartalmazó keresési eredmények adja vissza. A lekérdezéstől függően az API-t adja vissza, amelyek megfelelnek a keresési lekérdezés, amelyek magukban foglalhatják méltó egyéni felhasználók számára, helyi üzletek, arcrész, célhelyek és további egy vagy több entitás. |
| [Képkeresés](../Bing-Image-Search/index.yml) | A Bing Image Search API lehetővé teszi a keresési és keresés magas színvonalú statikus és animált képeket hasonló [Bing.com/images](https://www.Bing.com/images). Pontosíthatja vagy kizárja a képek attribútum, beleértve a mérete, a színt, a licenc és a frissesség kereséseket. Keresse meg a felkapott képek, tölthet fel képeket a velük kapcsolatos információkhoz juthat, és a villámnézet megjelenítése. |
| [Hírkeresés](../Bing-News-Search/index.yml) | A Bing News Search API lehetővé teszi, hogy a legfrissebb híreket hasonló található [Bing.com/news](https://www.Bing.com/news). Az API több forrásból vagy tartományok hírcikkeket adja vissza. Az első népszerű cikkek, legfontosabb és hírek között kereshet.
| [Videók keresése](../Bing-Video-Search/index.yml) | A Bing Videókeresési API lehetővé teszi, hogy keressen videókat a weben. Felkapott videók, a kapcsolódó tartalom és a miniatűr villámnézeti beolvasása. |
| [Vizuális keresés](../Bing-visual-search/index.yml) | Töltsön fel egy képet, vagy egy URL-cím használatával szemléletes adatainak beolvasása, mint például a vizuálisan hasonló termékek, a képeket és a kapcsolódó keresések erre. |
 [Helyi üzleti keresése](../bing-local-business-search/index.yml) | A Bing Local Business Search API-val az alkalmazások a közeli vállalkozásokkal kapcsolatos elérhetőségi és helyinformációkat találhatnak keresési lekérdezések alapján. |

## <a name="the-bing-custom-search-api"></a>A Bing Custom Search API

Az egyéni keresési példányok létrehozását a [Bing Custom Search](../Bing-Custom-Search/index.yml) API lehetővé teszi, hogy hozzon létre egy keresési funkciót csak a tartalom és -témakörök konkrét összpontosítani. Például adjon meg, a tartományok, webhelyek és adott weboldalt, amely megkeresi a Bing, eredményeket fog kialakítani, hogy adott tartalom. A Bing egyéni automatikus kiegészítés, kép, beépítheti és Video Search API-k további testreszabási a keresési élmény.  

## <a name="additional-bing-search-apis"></a>További Bing Search APIs

Az alábbi Bing keresési API-k lehetővé teszik a keresési élmény javításához az egyéb Bing keresési API-k kombinálásával.

| API | Leírás |
| -- | -- | 
| [Bing Autosuggest](../Bing-Autosuggest/index.yml) | Javíthatja az alkalmazás keresési funkciót, a Bing Autosuggest API által ajánlott keresések visszaadó valós időben.  |
| [A Bing Statistics](bing-web-stats.md) | Statisztikai Bing analytics biztosít a Bing Search APIs, az alkalmazás használja. A rendelkezésre álló elemzési néhány mennyiségére, a legjobb lekérdezési karakterláncok és a földrajzi eloszlása. |

## <a name="next-steps"></a>További lépések

* A Bing Search API [szolgáltatás díjszabását.](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* A [Bing használati és megjelenítési követelményei](./use-display-requirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.
