---
title: moreLikeThis (előzetes verzió) lekérdezési funkció
titleSuffix: Azure Cognitive Search
description: Ismerteti a moreLikeThis (előzetes verzió) szolgáltatást, amely az Azure Cognitive Search REST API előzetes verzióiban érhető el.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b7959beca8a7787a331388b77ebe4060c3675e6d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793473"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (előzetes verzió) az Azure Cognitive Search

> [!Note]
> a moreLikeThis előzetes verzióban érhető el, és nem éles használatra készült. A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.

`moreLikeThis=[key]` egy lekérdezési paraméter a [keresési dokumentumok API](https://docs.microsoft.com/rest/api/searchservice/search-documents) -ban, amely a dokumentum kulcsában megadott dokumentumhoz hasonló dokumentumokat keres. Ha `moreLikeThis`re vonatkozó keresési kérelmet küld, a rendszer egy lekérdezést hoz létre az adott dokumentumból kinyert keresési kifejezésekkel, amelyek a legmegfelelőbb dokumentumot írják le. A rendszer ezután a generált lekérdezést használja a keresési kérelem elvégzéséhez. Alapértelmezés szerint az összes kereshető mező tartalma megtekinthető, mínusz a `searchFields` paraméterrel megadott korlátozott mezők. A `moreLikeThis` paraméter nem használható a keresési paraméterrel, `search=[string]`.

Alapértelmezés szerint a rendszer a legfelső szintű kereshető mezők tartalmát veszi figyelembe. Ha inkább konkrét mezőket szeretne megadni, használhatja a `searchFields` paramétert. 

[Összetett típusban](search-howto-complex-data-types.md)nem használhatók kereshető almezők a moreLikeThis.

## <a name="examples"></a>Példák 

Az alábbi példa egy moreLikeThis-lekérdezést mutat be. A lekérdezés megkeresi azokat a dokumentumokat, amelyek leírás mezőjének legtöbbje hasonlít a forrásdokumentum mezőjéhez, ahogy azt a `moreLikeThis` paraméter adja meg.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>Következő lépések

A szolgáltatással való kísérletezéshez bármilyen webes tesztelési eszközt használhat.  Javasoljuk, hogy ehhez a gyakorlathoz a Poster-t használja.

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search REST API-k megismerése a Poster használatával](search-get-started-postman.md)