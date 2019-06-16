---
title: OData válassza referencia – Azure Search
description: OData nyelvi dokumentáció az Azure Search-lekérdezések szintaxis jelölje ki.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 9383ae725fffac55854488ffbc6aeb161ae7e0c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079677"
---
# <a name="odata-select-syntax-in-azure-search"></a>Az Azure Search OData $select-szintaxis

 Használhatja a [OData **$select** paraméter](query-odata-filter-orderby-syntax.md) kiválasztása az Azure Search keresési eredmények szerepeltetendő mezőket. Ez a cikk ismerteti a szintaxisa **$select** részletesen. Használatával kapcsolatos további általános információt **$select** keresési eredmények szerkesztésekor lásd [dolgozni a keresési eredmények az Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Szintaxis

A **$select** paraméter meghatározza, hogy mely mezők, egyes dokumentumok rendszer adja vissza a lekérdezés eredményhalmazában. A következő EBNF ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) határozza meg a nyelvtani a **$select** paramétert:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Egy interaktív szintaxisdiagramja is érhető el:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisdiagramja](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Lásd: [OData kifejezés szintaxisának referenciája az Azure Search](search-query-odata-syntax-reference.md) a teljes EBNF számára.

A **$select** paraméter két formában érhető el:

1. Egy egyetlen csillag (`*`), amely jelzi, hogy vissza kell adni az összes lekérdezhető mezők, vagy
1. A mező elérési utak vesszővel tagolt listája, mely mezők azonosítása vissza kell adni.

A második űrlap használata esetén előfordulhat, hogy csak akkor kell meghatározni a lekérdezhető mezők listájában.

Ha egy összetett mezőt annak almező explicit módon megadása nélkül, minden lekérhető alárendelt mező szerepelni fog a lekérdezés eredményhalmazában. Tegyük fel, hogy az index tartozik egy `Address` mezőt a `Street`, `City`, és `Country` lekérdezhető az összes alárendelt mezőket. Ha megad `Address` a **$select**, a lekérdezési eredményeket mind a három alárendelt mezőben tartalmazza.

## <a name="examples"></a>Példák

Tartalmazza a `HotelId`, `HotelName`, és `Rating` az eredmények között, a legfelső szintű mezőket, valamint a `City` az optimálisnál mezőjében `Address`:

    $select=HotelId, HotelName, Rating, Address/City

Egy példa eredmény ehhez hasonló lehet:

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

Tartalmazza a `HotelName` legfelső szintű mezője az eredményeket, valamint az összes alárendelt mező `Address`, és a `Type` és `BaseRate` almező objektumok a `Rooms` gyűjtemény:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Egy példa eredmény ehhez hasonló lehet:

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

- [Hogyan használható a keresési eredmények az Azure Search szolgáltatásban](search-pagination-page-layout.md)
- [Az Azure Search OData kifejezés nyelvi – áttekintés](query-odata-filter-orderby-syntax.md)
- [Az Azure Search OData kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
