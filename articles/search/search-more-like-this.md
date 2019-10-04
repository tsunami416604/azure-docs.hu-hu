---
title: moreLikeThis Azure Searchban (előzetes verzió) – Azure Search
description: A moreLikeThis (előzetes verzió) szolgáltatás előzetes dokumentációja, amely a Azure Search REST API elérhető.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d7c816c545c6647907aa9d700a4eb6ed91277465
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182294"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis Azure Search

> [!Note]
> a moreLikeThis előzetes verzióban érhető el, és nem éles használatra készült. A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.

`moreLikeThis=[key]`a a [keresési dokumentumok API](https://docs.microsoft.com/rest/api/searchservice/search-documents) lekérdezési paramétere, amely a dokumentum kulcsában megadott dokumentumhoz hasonló dokumentumokat keres. Ha keresési kérelem `moreLikeThis`történik, a rendszer egy lekérdezést hoz létre az adott dokumentumból kinyert keresési kifejezésekkel, amelyek az adott dokumentumot a legmegfelelőbb módon írják le. A rendszer ezután a generált lekérdezést használja a keresési kérelem elvégzéséhez. Alapértelmezés szerint az összes kereshető mező tartalma megtekinthető, mínusz a `searchFields` paraméterrel megadott korlátozott mezők. A `moreLikeThis` paraméter nem használható a keresési `search=[string]`paraméterrel.

Alapértelmezés szerint a rendszer a legfelső szintű kereshető mezők tartalmát veszi figyelembe. Ha inkább konkrét mezőket szeretne megadni, használhatja a `searchFields` paramétert. 

[Összetett típusban](search-howto-complex-data-types.md)nem használhatók kereshető almezők a moreLikeThis.

## <a name="examples"></a>Példák 

Az alábbi példa egy moreLikeThis-lekérdezést mutat be. A lekérdezés megkeresi azokat a dokumentumokat, amelyek leírás mezőjének legtöbbje hasonló a forrásdokumentum mezőjéhez `moreLikeThis` a paraméterben megadott módon.

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


## <a name="next-steps"></a>További lépések

A szolgáltatással való kísérletezéshez bármilyen webes tesztelési eszközt használhat.  Javasoljuk, hogy ehhez a gyakorlathoz a Poster-t használja.

> [!div class="nextstepaction"]
> [Ismerkedjen meg Azure Search REST API-kkal a Poster használatával](search-get-started-postman.md)