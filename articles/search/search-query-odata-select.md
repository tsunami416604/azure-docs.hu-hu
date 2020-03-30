---
title: OData hivatkozás kiválasztása
titleSuffix: Azure Cognitive Search
description: Szintaxis és nyelvi hivatkozás az Azure Cognitive Search-lekérdezések keresési eredményeiben visszaadandó mezők explicit kiválasztásához.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113104"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>OData $select szintaxis az Azure Cognitive Search-ben

 Az [OData **$select** paraméter](query-odata-filter-orderby-syntax.md) segítségével kiválaszthatja, hogy mely mezőket szeretné felvenni a keresési eredmények be, az Azure Cognitive Search. Ez a cikk részletesen ismerteti a **$select** szintaxisát. A **$select** keresési eredmények megjelenítésekor idáig további általános tudnivalókat talál a Keresési eredmények használata az Azure Cognitive Search alkalmazásban című [témakörben.](search-pagination-page-layout.md)

## <a name="syntax"></a>Szintaxis

A **$select** paraméter határozza meg, hogy az egyes dokumentumok mely mezőit adja vissza a lekérdezés eredményhalmaza. A következő EBNF ([Extended Backus-Naur Form)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)határozza meg a **$select** paraméter nyelvtanát:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Interaktív szintaktikai diagram is elérhető:

> [!div class="nextstepaction"]
> [OData-szintaktikai diagram az Azure Cognitive Search szolgáltatáshoz](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Lásd: [OData kifejezés szintaxis referencia az Azure Cognitive Search](search-query-odata-syntax-reference.md) a teljes EBNF.

A **$select** paraméter két formában jelentkezik:

1. Egy csillag`*`( ), amely azt jelzi, hogy az összes visszakereshető mezőt vissza kell küldeni, vagy
1. A mezőelérési utak vesszővel tagolt listája, amely meghatározza, hogy mely mezőket kell visszaadni.

A második űrlap használatakor csak a listában adhatja meg a visszakereshető mezőket.

Ha egy összetett mezőt az almezők kifejezett megadása nélkül sorolunk fel, akkor a lekérdezés eredményhalmaza minden beolvasható almező szerepelni fog. Tegyük fel például, `Address` hogy `Street` `City`az `Country` indexben van egy mező a , és almezőkkel, amelyek mindegyike visszakereshető. Ha a `Address` **$select**adja meg, a lekérdezés eredménye mindhárom almezőt tartalmazza.

## <a name="examples"></a>Példák

A `HotelId`, `HotelName`és `Rating` legfelső szintű mezők felvétele az eredményekbe, valamint az `City` `Address`almező:

    $select=HotelId, HotelName, Rating, Address/City

Egy példa eredménye így nézhet ki:

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

A `HotelName` legfelső szintű mező felvétele az eredményekbe, valamint `Address`a gyűjteményben az `Type` egyes objektumok és `BaseRate` almezők összes almezőjét: `Rooms`

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Egy példa eredménye így nézhet ki:

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

- [A keresési eredmények működése az Azure Cognitive Search szolgáltatásban](search-pagination-page-layout.md)
- [Az Azure Cognitive Search OData-kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezés szintaxisának hivatkozása](search-query-odata-syntax-reference.md)
- [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
