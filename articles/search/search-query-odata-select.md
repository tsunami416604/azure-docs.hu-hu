---
title: OData-hivatkozás kiválasztása
titleSuffix: Azure Cognitive Search
description: Szintaxis és nyelvi hivatkozás az Azure Cognitive Search lekérdezések keresési eredményeiben visszaadott mezők explicit kiválasztásához.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 64f15bf3d262249cdda2760c7ddf768be2590419
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113104"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>OData $select szintaxis az Azure-ban Cognitive Search

 A [OData **$Select** paraméter](query-odata-filter-orderby-syntax.md) használatával kiválaszthatja, hogy mely mezők szerepeljenek az Azure Cognitive Search keresési eredményei között. Ez a cikk részletesen ismerteti **$Select** szintaxisát. A keresési eredmények megjelenítésével kapcsolatos további általános információkért lásd: a keresési eredmények **$Select** használata [Az Azure-ban Cognitive Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Szintaxis

A **$Select** paraméter határozza meg, hogy az egyes dokumentumok mely mezői lesznek visszaadva a lekérdezési eredményhalmaz alapján. A következő EBNF ([bővített Naur-űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a **$Select** paraméter nyelvtanát határozza meg:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

A **$Select** paraméter két formában jön:

1. Egyetlen csillag (`*`), amely azt jelzi, hogy az összes beolvasható mezőt vissza kell adni, vagy
1. A mezők elérési útjának vesszővel tagolt listája, amely azonosítja a visszaadott mezőket.

A második űrlap használatakor csak lekérhető mezőket adhat meg a listában.

Ha az almezők explicit módon való megadása nélkül listáz egy összetett mezőt, az összes beolvasható almező belekerül a lekérdezési eredményhalmazba. Tegyük fel például, hogy az index egy `Address` mezővel rendelkezik, amely `Street`, `City`és `Country` almezőket tartalmaz. Ha **$select**`Address`t ad meg, a lekérdezés eredménye mindhárom almezőt tartalmazni fogja.

## <a name="examples"></a>Példák

Adja meg az eredmények `HotelId`, `HotelName`és `Rating` legfelső szintű mezőit, valamint `Address``City` almezőjét:

    $select=HotelId, HotelName, Rating, Address/City

Egy példa eredménye a következőképpen néz ki:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

Adja meg az eredmények `HotelName` legfelső szintű mezőjét, valamint a `Address`összes almezőjét, valamint a `Rooms` gyűjtemény egyes objektumainak `Type` és `BaseRate` almezőit:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Egy példa eredménye a következőképpen néz ki:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>További lépések  

- [Keresési eredmények használata az Azure-ban Cognitive Search](search-pagination-page-layout.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése az Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
