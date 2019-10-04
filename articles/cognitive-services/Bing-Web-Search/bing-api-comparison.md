---
title: Mi a Bing Search API-k?
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megismerheti a Bing Search API-k, valamint azt, hogy miként engedélyezheti a kognitív internetes kereséseket az alkalmazásaiban és szolgáltatásaiban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 0e9a71e1e826569930cf593a7e264020617bdc3a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883394"
---
# <a name="what-are-the-bing-search-apis"></a>Mi a Bing Search API-k?

A Bing Search API-k weblapokat, képeket, híreket, helyszíneket és egyéb hirdetéseket tartalmazó webes alkalmazásokat és szolgáltatásokat hozhat létre. Ha keresési kérelmeket küld a Bing Search REST API-k vagy SDK-k használatával, megtekintheti a webes keresésekhez kapcsolódó információkat és tartalmakat. Ebből a cikkből megismerheti a különböző Bing Search API-kat, valamint azt, hogy miként integrálhatja a kognitív kereséseket alkalmazásaiba és szolgáltatásaiba. Az árak és a díjszabási korlátok az API-k között eltérőek lehetnek.

## <a name="the-bing-web-search-api"></a>A Bing Web Search API

A [Bing Web Search API](../Bing-Web-Search/index.yml) weblapokat, képeket, videókat, híreket és egyebeket ad vissza. Az API-nak küldött keresési lekérdezések szűrésével belefoglalhat vagy kizárhat bizonyos tartalomtípusokat.

Vegye fontolóra a Bing Web Search API használatát olyan alkalmazásokban, amelyeknek szükségük lehet a releváns webes tartalmak összes típusának megkeresésére. Ha az alkalmazás egy adott típusú online tartalmat keres, vegye figyelembe az alábbi keresési API-k egyikét:

## <a name="content-specific-bing-search-apis"></a>Content-specifikus Bing keresési API-k

A következő Bing Search API-k adott tartalmat adnak vissza a webről, például képekből, hírekből, helyi vállalkozásokból és videókból.

| Bing API | Leírás |
| -- | -- |
| [Entity Search](../Bing-Entities-Search/index.yml) | A Bing Entity Search API az entitásokat tartalmazó keresési eredményeket adja vissza, amelyek lehetnek személyek, helyek vagy dolgok. A lekérdezéstől függően az API egy vagy több olyan entitást ad vissza, amely megfelel a keresési lekérdezésnek. A keresési lekérdezés tartalmazhatja a figyelemreméltó személyeket, a helyi vállalkozásokat, a tereptárgyak, a célhelyeket és egyebeket is. |
| [Image Search](../Bing-Image-Search/index.yml) | A Bing Image Search API segítségével megkeresheti és megkeresheti a [Bing.com/images](https://www.Bing.com/images)hasonló, magas színvonalú statikus és animált képeket. A keresések pontosításával belefoglalhatja vagy kizárhatja a képeket az attribútumok alapján, beleértve a méretet, a színt, a licencet és a frissességet is. Emellett megkeresheti a trendek képeit, képeket tölthet fel, és megtekintheti őket, és megjelenítheti a miniatűr előnézeteket. |
| [News Search](../Bing-News-Search/index.yml) | A Bing News Search API a [Bing.com/news](https://www.Bing.com/news)hasonló híreket talál. Az API több forrásból vagy adott tartományból származó híreket ad vissza. A kategóriák között kereshet a Kiemelt cikkek, a leggyakoribb történetek és a főcímek beszerzéséhez. |
| [Video Search](../Bing-Video-Search/index.yml) | A Bing Video Search API lehetővé teszi a videók keresését a weben keresztül. A videók, a kapcsolódó tartalmak és a miniatűr előnézetek megismerése. |
| [Visual Search](../Bing-visual-search/index.yml) | Töltsön fel egy képet, vagy használjon egy URL-címet, amellyel áttekintheti az információkat, például a hasonló termékekhez, képekhez és kapcsolódó keresésekhez. |
 [Helyi üzleti keresés](../bing-local-business-search/index.yml) | A Bing helyi üzleti keresési API lehetővé teszi, hogy alkalmazásai a keresési lekérdezések alapján megkeressék a helyi vállalkozások kapcsolattartási és tartózkodási információit. |

## <a name="the-bing-custom-search-api"></a>A Bing Custom Search API

Ha egyéni keresési példányt hoz létre a [Bing Custom Search](../Bing-Custom-Search/index.yml) API-val, olyan keresési élményt hozhat létre, amely kizárólag az Ön számára fontos tartalomra és témakörökre koncentrál. Ha például megadta a Bing által keresett tartományokat, webhelyeket és konkrét weblapokat, Bing Custom Search az eredményeket az adott tartalomra fogja szabni. A keresési élmény további testreszabásához beépítheti a Bing egyéni automatikus kiegészítést, képet és Video Search API-kat.

## <a name="additional-bing-search-apis"></a>További Bing Search API-k

A következő Bing Search API-k segítségével javíthatja a keresési élményt, ha más Bing Search API-kkal kombinálja őket.

| API | Leírás |
| -- | -- |
| [Bing Autosuggest](../Bing-Autosuggest/index.yml) | A javasolt keresések valós idejű visszaadásával javíthatja az alkalmazás keresési élményét a Bing Autosuggest API.  |
| [Bing-statisztika](bing-web-stats.md) | A Bing Statistics az alkalmazás által használt Bing Search API-k elemzését biztosítja. A rendelkezésre álló elemzések közé tartozik például a hívási kötet, a leggyakoribb lekérdezési karakterláncok és a földrajzi eloszlás. |

## <a name="next-steps"></a>További lépések

* A Bing Search [](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) API díjszabása
* A [Bing használati és megjelenítési követelményei](./use-display-requirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.
