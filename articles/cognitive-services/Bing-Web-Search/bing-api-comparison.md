---
title: Mik azok a Bing Search API-k?
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megismerheti a Bing Keresési API-kat, és megtudhatja, hogyan engedélyezheti a kognitív internetes kereséseket az alkalmazásokban és a szolgáltatásokban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 82b1f13562a49284059c25bcbd39a33daf949dcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74775540"
---
# <a name="what-are-the-bing-search-apis"></a>Mik azok a Bing Search API-k?

A Bing Search API-kkal olyan webes alkalmazásokat és szolgáltatásokat hozhat létre, amelyek hirdetések megjelenítése nélkül képesek webhelyek, képek, hírek, helyszínek stb. kikeresésére. Ha a Bing Search REST API-k vagy SDK-k használatával keresési kérelmeket küld, releváns információkat és tartalmakat kaphat az internetes keresésekhez. Ebből a cikkből megismerheti a különböző Bing keresési API-kat, és megtudhatja, hogyan integrálhatja a kognitív kereséseket az alkalmazásokba és a szolgáltatásokba. Az árak és a díjkorlátok API-k között változhatnak.

## <a name="the-bing-web-search-api"></a>A Bing webes keresési API

A [Bing Web Search API](../Bing-Web-Search/overview.md) weblapokat, képeket, videókat, híreket és egyebeket ad vissza. Az API-ba küldött keresési lekérdezések szűrésével bizonyos tartalomtípusokat is felvehet vagy kizárhat.

Fontolja meg a Bing Web Search API használatát olyan alkalmazásokban, amelyeknél minden típusú releváns webes tartalomra rá kell keresni. Ha az alkalmazás egy adott típusú online tartalmat keres, fontolja meg az alábbi keresési API-k egyikét:

## <a name="content-specific-bing-search-apis"></a>Tartalomspecifikus Bing-keresési API-k

A következő Bing keresési API-k adott tartalmakat adnak vissza az internetről, például képeket, híreket, helyi vállalkozásokat és videókat.

| Bing API | Leírás |
| -- | -- |
| [Entity Search](../Bing-Entities-Search/overview.md) | A Bing Entity Search API az entitásokat tartalmazó keresési eredményeket adja vissza, amelyek lehetnek személyek, helyek vagy dolgok. A lekérdezéstől függően az API egy vagy több olyan entitást ad vissza, amelyek megfelelnek a keresési lekérdezésnek. A keresési lekérdezés tartalmazhat figyelemre méltó személyeket, helyi vállalkozásokat, tereptárgyakat, úti célokat és egyebeket. |
| [Image Search](../Bing-Image-Search/overview.md) | A Bing Image Search API lehetővé teszi, hogy a [Bing.com/images-hez](https://www.Bing.com/images)hasonló, kiváló minőségű statikus és animált képeket keressen és találjon. Finomíthatja a kereséseket, hogy attribútum szerint tartalmazza vagy kizárja a képeket, beleértve a méretet, a színt, a licencet és a frissességet. Kereshet népszerű képekre, képeket is feltölthet, hogy betekintést nyerjen róluk, és megjelenítheti a miniatűr előnézeteket. |
| [News Search](../Bing-News-Search/search-the-web.md) | A Bing News Search API-val [a Bing.com/news-hez](https://www.Bing.com/news)hasonló híreket kereshet. Az API több forrásból vagy adott tartományból származó híreket ad vissza. A kategóriák között kereshet, így felkapott cikkeket, legnépszerűbb cikkeket és címsorokat kaphat. |
| [Video Search](../Bing-Video-Search/overview.md) | A Bing Video Search API lehetővé teszi a videók keresését az interneten keresztül. Népszerű videókat, kapcsolódó tartalmakat és miniatűr előnézeteket kaphat. |
| [Visual Search](../Bing-visual-search/overview.md) | Töltsön fel egy képet, vagy használjon URL-címet, hogy éleslátó információkat kapjon róla, például vizuálisan hasonló termékeket, képeket és kapcsolódó kereséseket. |
 [Helyi üzleti keresés](../bing-local-business-search/overview.md) | A Bing Helyi üzleti keresés API lehetővé teszi, hogy az alkalmazások keresési lekérdezések alapján megtalálják a helyi vállalkozásokkal kapcsolatos kapcsolattartási és helyadatokat. |

## <a name="the-bing-custom-search-api"></a>A Bing egyéni keresési API-ja

Egyéni keresési példány létrehozása a [Bing egyéni keresési](../Bing-Custom-Search/overview.md) API-val lehetővé teszi, hogy olyan keresési élményt hozzon létre, amely kizárólag a fontos tartalmakra és témakörökre összpontosít. Ha például megadja azokat a tartományokat, webhelyeket és konkrét weblapokat, amelyekben a Bing keresni fog, a Bing egyéni keresés az adott tartalomhoz igazítja az eredményeket. A Bing egyéni automatikus javaslat, a kép és a videókeresés API-k at beépítheti a keresési élmény további testreszabásához.

## <a name="additional-bing-search-apis"></a>További Bing keresési API-k

Az alábbi Bing Keresési API-k lehetővé teszik a keresési élmény javítását azáltal, hogy kombinálja őket más Bing keresési API-kkal.

| API | Leírás |
| -- | -- |
| [Bing – Automatikus kiegészítés](../Bing-Autosuggest/get-suggested-search-terms.md) | Javítsa az alkalmazás keresési élményét a Bing Autosuggest API-val a javasolt keresések valós idejű visszaküldésével.  |
| [Bing statisztikája](bing-web-stats.md) | A Bing Statistics elemzést nyújt az alkalmazás által használt Bing Search API-khoz. A rendelkezésre álló elemzések közé tartozik a hívásmennyiséget, a legfelső lekérdezési karakterláncokat és a földrajzi eloszlást. |

## <a name="next-steps"></a>További lépések

* Bing Search API [díjszabási részletei](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* A [Bing használati és megjelenítési követelményei](./use-display-requirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.
