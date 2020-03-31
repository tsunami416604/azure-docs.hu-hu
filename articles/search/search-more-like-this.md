---
title: moreLikeThis (előzetes verzió) lekérdezési szolgáltatás
titleSuffix: Azure Cognitive Search
description: Ismerteti a moreLikeThis (előzetes verzió) funkciót, amely az Azure Cognitive Search REST API előzetes verzióiban érhető el.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873811"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (előzetes verzió) az Azure Cognitive Search szolgáltatásban

> [!IMPORTANT] 
> Ez a funkció jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) A [REST API 2019-05-06-Preview verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs portál- vagy .NET SDK-támogatás.

`moreLikeThis=[key]`A Search Documents [API](https://docs.microsoft.com/rest/api/searchservice/search-documents) egy lekérdezési paramétere, amely a dokumentumkulcs által megadott dokumentumhoz hasonló dokumentumokat keres. Amikor keresési kérelmet `moreLikeThis`készít a segítségével, a rendszer lekérdezést hoz létre az adott dokumentumból kinyert keresési kifejezésekkel, amelyek a legjobban jellemzik a dokumentumot. A létrehozott lekérdezés ezután a keresési kérelem hez használatos. Alapértelmezés szerint a program figyelembe veszi az összes kereshető mező tartalmát, `searchFields` levonva ebből a paraméterrel megadott korlátozott mezőket. A `moreLikeThis` paraméter nem használható a `search=[string]`keresési paraméterrel, .

Alapértelmezés szerint a program figyelembe veszi az összes legfelső szintű kereshető mező tartalmát. Ha ehelyett konkrét mezőket szeretne megadni, `searchFields` használhatja a paramétert. 

[Összetett](search-howto-complex-data-types.md)típusú `MoreLikeThis` kereshető almezőkön nem használható.

## <a name="examples"></a>Példák

Az alábbi példák a hotelek rövid útmutatóból származó mintát [használják: Keresési index létrehozása az Azure Portalon.](search-get-started-portal.md)

### <a name="simple-query"></a>Egyszerű lekérdezés

A következő lekérdezés azokat a dokumentumokat keresi meg, amelyek megnevezési mezői a leginkább hasonlítanak a `moreLikeThis` forrásbizonylat paraméteráltal megadott mezőjéhez:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

Ebben a példában a kérelem a 29-eshez `HotelId` hasonló szállodákat keres.
A HTTP GET használata helyett `MoreLikeThis` a HTTP POST használatával is meghívható:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Szűrők alkalmazása

`MoreLikeThis`kombinálható más gyakori lekérdezési `$filter`paraméterekkel, például . A lekérdezés például csak olyan szállodákra korlátozható, amelyek kategóriája "Költségvetés", és ahol az értékelés magasabb, mint 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>Mezők kijelölése és az eredmények korlátozása

A `$top` választó val a `MoreLikeThis` lekérdezésben visszaadandó eredmények korlátozásának korlátozására használható. A mezők is kijelölhetők a segítségével. `$select` Itt az első három szálloda kerül kiválasztásra azonosítójukkal, nevükkel és értékelésükkel együtt: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>További lépések

Bármilyen webes tesztelő eszközt használhat a funkció val a kísérletezéshez.  Javasoljuk, hogy a Postman-t használja ehhez a gyakorlathoz.

> [!div class="nextstepaction"]
> [Fedezze fel az Azure Cognitive Search REST API-kat a Postman használatával](search-get-started-postman.md)
