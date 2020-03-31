---
title: OData nyelv – áttekintés
titleSuffix: Azure Cognitive Search
description: OData-nyelv áttekintése az Azure Cognitive Search-lekérdezések szűrőihez, kijelöléséhez és lerendeléséhez.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: f3a1be435e297ab4a9ba7f8bfbd5f3ce3451d8a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153876"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>OData-nyelv `$filter`– `$orderby`áttekintés `$select` a , és az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive Search támogatja az OData kifejezés szintaxisának egy részét **a $filter**, **$orderby**és **$select** kifejezésekhez. A szűrőkifejezések kiértékelése a lekérdezéselemzés során történik, a keresés adott mezőkre való korlátozása vagy az indexvizsgálatok során használt egyezési feltételek hozzáadása. A rendelési kifejezések et utófeldolgozási lépésként alkalmazza a rendszer az eredményhalmaz felett a visszaadott dokumentumok rendezéséhez. A select kifejezések határozzák meg, hogy mely dokumentummezők szerepelnek az eredményhalmazban. Ezeknek a kifejezéseknek a szintaxisa különbözik a **keresési** paraméterben használt [egyszerű](query-simple-syntax.md) vagy [teljes](query-lucene-syntax.md) lekérdezésszintaxistól, bár a mezőkre való hivatkozás szintaxisában van átfedés.

Ez a cikk áttekintést nyújt a szűrőkben, a rendelési és a kijelölési kifejezésekben használt OData-kifejezésnyelvről. A nyelv "alulról felfelé" kerül bemutatásra, kezdve a legalapvetőbb elemekkel, és építve rájuk. Az egyes paraméterek legfelső szintű szintaxisát egy külön cikk ismerteti:

- [$filter szintaxis](search-query-odata-filter.md)
- [$orderby szintaxis](search-query-odata-orderby.md)
- [$select szintaxis](search-query-odata-select.md)

Az OData-kifejezések az egyszerűtől a rendkívül összetettig terjednek, de közös elemek. Az Azure Cognitive Search oData-kifejezéseinek legalapvetőbb részei a következők:

- **Mezőelérési utak**, amelyek az index adott mezőire vonatkoznak.
- **Állandók**, amelyek egy bizonyos adattípus literális értékei.

> [!NOTE]
> Az Azure Cognitive Search terminológiája több szempontból is eltér az [OData-szabványtól.](https://www.odata.org/documentation/) Az Azure Cognitive Search mezőjának nevezett **mezőt** az **OData-ban** tulajdonságnak nevezzük, és hasonlóképpen a **mezőútvonal** és a **tulajdonság elérési útja**esetén. Az Azure Cognitive Search **dokumentumait** tartalmazó **indexet** általánosabban hivatkoznak az OData-ban **entitásokat**tartalmazó **entitáskészletként.** Az Azure Cognitive Search terminológiája ebben a hivatkozásban használatos.

## <a name="field-paths"></a>Mezőelérési utak

A következő EBNF ([Extended Backus-Naur Form)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)határozza meg a mezőutak nyelvtanát.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Interaktív szintaktikai diagram is elérhető:

> [!div class="nextstepaction"]
> [OData-szintaktikai diagram az Azure Cognitive Search szolgáltatáshoz](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Lásd: [OData kifejezés szintaxis referencia az Azure Cognitive Search](search-query-odata-syntax-reference.md) a teljes EBNF.

A mezőelérési út egy vagy több, perjellel elválasztott **azonosítóból** áll. Minden azonosító olyan karaktersorozat, amelynek ASCII betűvel vagy aláhúzásjellel kell kezdődnie, és csak ASCII betűket, számjegyeket vagy aláhúzásjeleket kell tartalmaznia. A betűk lehetnek kis- vagy nagybetűk.

Az azonosítók hivatkozhatnak egy mező nevére vagy egy **tartományváltozóra** egy`any` `all`szűrőben lévő [gyűjteménykifejezés](search-query-odata-collection-operators.md) ( vagy ) környezetében. A tartományváltozó olyan, mint egy ciklusváltozó, amely a gyűjtemény aktuális elemét jelöli. Összetett gyűjtemények esetén ez a változó egy objektumot jelöl, ezért használhat mezőgörbéket a változó almezőire való hivatkozáshoz. Ez hasonló a pontos jelöléshez számos programozási nyelven.

A mezőútvonalak példáit az alábbi táblázat mutatja be:

| Mező elérési útja | Leírás |
| --- | --- |
| `HotelName` | Az index legfelső szintű mezőjére hivatkozik |
| `Address/City` | Az index `City` egy összetett mezőjének almezőjére hivatkozik; `Address` ebben a `Edm.ComplexType` példában szerepel a típus |
| `Rooms/Type` | Az indexben `Type` lévő összetett gyűjteménymező almezőjére hivatkozik; `Rooms` ebben a `Collection(Edm.ComplexType)` példában szerepel a típus |
| `Stores/Address/Country` | Az indexben `Country` lévő összetett `Address` gyűjteménymező almezőjének almezőjére hivatkozik; `Stores` típus, `Collection(Edm.ComplexType)` és `Address` ebben `Edm.ComplexType` a példában típus |
| `room/Type` | A `room` tartományváltozó `Type` almezőjére utal, például a szűrőkifejezésben`Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | A `store` tartományváltozó `Country` `Address` almezőjének almezőjére utal, például a szűrőkifejezésben`Stores/any(store: store/Address/Country eq 'Canada')` |

A mezőútvonal jelentése a környezettől függően eltérő lehet. A szűrőkben a mezőelérési út az aktuális dokumentum ban lévő mező *egyetlen példányának* értékére utal. Más környezetekben, például **$orderby**, **$select**vagy [a teljes Lucene szintaxisban a mezőkeresésben](query-lucene-syntax.md#bkmk_fields)a mező elérési útja magára a mezőre utal. Ez a különbség bizonyos következményekkel jár a mezőútvonalak szűrőkben való használatának módjára nézve.

Vegyük figyelembe `Address/City`a mező elérési útját . A szűrőben ez az aktuális dokumentum egyetlen városára, például a "San Francisco"-ra vonatkozik. Ezzel szemben, `Rooms/Type` utal, `Type` hogy az al-mező sok szoba (mint a "standard" az első szobában, "deluxe" a második szobában, és így tovább). Mivel `Rooms/Type` nem hivatkozik az almező `Type` *egyetlen példányára* sem, nem használható közvetlenül a szűrőben. Ehelyett a szobatípus szűréséhez [lambda kifejezést](search-query-odata-collection-operators.md) kell használni a tartományváltozóval, mint ez:

    Rooms/any(room: room/Type eq 'deluxe')

Ebben a példában `room` a tartományváltozó megjelenik a `room/Type` mező elérési útján. Így `room/Type` az aktuális szoba típusára utal az aktuális dokumentumban. Ez az `Type` almező egyetlen példánya, így közvetlenül a szűrőben használható.

### <a name="using-field-paths"></a>Mezőútvonalak használata

A mezőútvonalak az Azure Cognitive Search REST API-k számos paraméterében [használatosak.](https://docs.microsoft.com/rest/api/searchservice/) Az alábbi táblázat felsorolja az összes olyan helyet, ahol használható, valamint a használatukra vonatkozó korlátozásokat:

| API | Paraméter neve | Korlátozások |
| --- | --- | --- |
| [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) vagy [frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) | `suggesters/sourceFields` | None |
| [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) vagy [frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) | `scoringProfiles/text/weights` | Csak **kereshető** mezőkre lehet hivatkozni |
| [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) vagy [frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) | `scoringProfiles/functions/fieldName` | Csak **szűrhető** mezőkre lehet hivatkozni |
| [Keresés](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`mikor `queryType` van`full` | Csak **kereshető** mezőkre lehet hivatkozni |
| [Keresés](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Csak **átlapulható** mezőkre lehet hivatkozni |
| [Keresés](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Csak **kereshető** mezőkre lehet hivatkozni |
| [Keresés](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Csak **kereshető** mezőkre lehet hivatkozni |
| [Javaslat](https://docs.microsoft.com/rest/api/searchservice/suggestions) és [automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Csak olyan mezőkre lehet hivatkozni, amelyek egy [javaslatajánló](index-add-suggesters.md) részét képezik |
| [Keresés](https://docs.microsoft.com/rest/api/searchservice/search-documents), [Javaslat](https://docs.microsoft.com/rest/api/searchservice/suggestions)és [Automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Csak **szűrhető** mezőkre lehet hivatkozni |
| [Keresés](https://docs.microsoft.com/rest/api/searchservice/search-documents) és [javaslat](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Csak **rendezhető** mezőkre lehet hivatkozni |
| [Keresés](https://docs.microsoft.com/rest/api/searchservice/search-documents), [Javaslat](https://docs.microsoft.com/rest/api/searchservice/suggestions)és [Keresés](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Csak **visszakereshető** mezőkre lehet hivatkozni |

## <a name="constants"></a>Állandók

Az OData-ban lévő állandók egy adott [entitásadatmodell](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM) típus konstans értékei. [Lásd: Támogatott adattípusok](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) az Azure Cognitive Search támogatott típusok listáját. A gyűjteménytípusok állandói nem támogatottak.

Az alábbi táblázat példákat mutat be az Azure Cognitive Search által támogatott adattípusok állandóira:

| Adattípus | Példa állandók |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>Speciális karakterek kiszabadulása karakterlánc-állandókban

Az OData karakterlánc-állandóit egyszeres idézőjelek jelölik. Ha olyan karakterlánc-állandóval rendelkező lekérdezést kell létrehoznia, amely maga is tartalmazhat idézőjeleket, a beágyazott idézőjelek megduplázásával elkerülheti azokat.

Például egy olyan kifejezés, amelynek formázatlan aposztrófja, mint például az "Alice autója" kifejezés, az OData-ban karakterlánc-állandóként `'Alice''s car'`jelenik meg.

> [!IMPORTANT]
> Szűrők programozott módon történő kialakításakor fontos, hogy ne felejtse el kikerülni a felhasználói bevitelből származó karakterlánc-állandókat. Ez az [injektálási támadások](https://wikipedia.org/wiki/SQL_injection)lehetőségének csökkentésére van , különösen, ha szűrőket használ a [biztonsági vágás végrehajtására](search-security-trimming-for-azure-search.md).

### <a name="constants-syntax"></a>Állandók szintaxisa

A következő EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) határozza meg a nyelvtant a fenti táblázatban látható legtöbb állandó esetében. A földrajzi térinformatikai típusok nyelvtana az [Azure Cognitive Search OData térinformatikai függvényeiben](search-query-odata-geo-spatial-functions.md)található.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

Interaktív szintaktikai diagram is elérhető:

> [!div class="nextstepaction"]
> [OData-szintaktikai diagram az Azure Cognitive Search szolgáltatáshoz](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Lásd: [OData kifejezés szintaxis referencia az Azure Cognitive Search](search-query-odata-syntax-reference.md) a teljes EBNF.

## <a name="building-expressions-from-field-paths-and-constants"></a>Kifejezések létrehozása mezőgörbékből és állandókból

A mezőelérési utak és az állandók az OData-kifejezések legalapvetőbb részei, de már maguk is teljes kifejezések. Valójában az Azure Cognitive Search **$select** paramétere nem más, mint a mezőútvonalak vesszővel tagolt listája, és **$orderby** nem sokkal bonyolultabb, mint **$select.** Ha történetesen van egy `Edm.Boolean` típusú mező az indexben, akár olyan szűrőt is írhat, amely nem más, mint a mező elérési útja. Az `true` állandók `false` és szintén érvényes szűrők.

Az idő nagy részében azonban összetettebb kifejezésekre van szükség, amelyek több mezőre és állandóra hivatkoznak. Ezek a kifejezések a paramétertől függően különböző módon vannak felépítve.

A következő EBNF ([Extended Backus-Naur Form)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)határozza meg a **$filter,** **$orderby**és **$select** paraméterek nyelvtanát. Ezek egyszerűbb kifejezésekből épülnek fel, amelyek mezőútvonalakra és állandókra hivatkoznak:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Interaktív szintaktikai diagram is elérhető:

> [!div class="nextstepaction"]
> [OData-szintaktikai diagram az Azure Cognitive Search szolgáltatáshoz](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Lásd: [OData kifejezés szintaxis referencia az Azure Cognitive Search](search-query-odata-syntax-reference.md) a teljes EBNF.

A **$orderby** és **$select** paraméterek egyaránt egyszerű kifejezések vesszővel tagolt listái. A **$filter** paraméter egy logikai kifejezés, amely egyszerűbb részkifejezésekből áll. Ezeket az alkifejezéseket logikai operátorok , például [ `and`, `or`és `not` ](search-query-odata-logical-operators.md)összehasonlító operátorok , például [ `eq`, `lt`, `gt`, és így tovább](search-query-odata-comparison-operators.md), és a beszedési operátorok, például [ `any` a . `all` ](search-query-odata-collection-operators.md)

A **$filter,** **$orderby**és **$select** paramétereket részletesebben a következő cikkek ismertetik:

- [OData $filter szintaxis az Azure Cognitive Search-ben](search-query-odata-filter.md)
- [Az OData $orderby szintaxist az Azure Cognitive Search szolgáltatásban](search-query-odata-orderby.md)
- [OData $select szintaxis az Azure Cognitive Search-ben](search-query-odata-select.md)

## <a name="see-also"></a>Lásd még  

- [Sokoldalú navigáció az Azure Cognitive Search szolgáltatásban](search-faceted-navigation.md)
- [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
- [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene lekérdezés szintaxisa](query-lucene-syntax.md)
- [Egyszerű lekérdezésszintaxis az Azure Cognitive Search szolgáltatásban](query-simple-syntax.md)
