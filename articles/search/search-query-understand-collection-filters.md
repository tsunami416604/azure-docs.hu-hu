---
title: A OData-gyűjtési szűrők ismertetése – Azure Search
description: Annak ismertetése, hogyan működnek a OData-gyűjtési szűrők Azure Search lekérdezésekben.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 5c3a0205f5a9ac5115e78f1bc11f70b2c50a9714
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647414"
---
# <a name="understanding-odata-collection-filters-in-azure-search"></a>A OData-gyűjtési szűrők ismertetése a Azure Searchban

A [](query-odata-filter-orderby-syntax.md) Azure Search gyűjtemény mezőinek szűréséhez a [ `any` és `all` ](search-query-odata-collection-operators.md) a operátort **lambda kifejezésekkel**együtt használhatja. A lambda kifejezés olyan logikai kifejezés, amely egy **tartomány változóra**hivatkozik. A `any` `for` és `all` a kezelők a legtöbb programozási nyelvben a hurokhoz hasonlóak, és a tartomány változó a Loop változó szerepét veszi át, a lambda kifejezést pedig a hurok törzsének. A Range változó a művelet "aktuális" értékét veszi át a hurok ismétlése során.

Ezek közül legalább a fogalmi módon működik. A valóságban a Azure Search nagyon eltérő módon `for` valósítja meg a szűrőket a hurkok működéséhez. Ideális esetben ez a különbség láthatatlan lesz, de bizonyos helyzetekben nem. Ennek végeredménye az, hogy a lambda kifejezések írásakor követni kell a szükséges szabályokat.

Ez a cikk azt ismerteti, hogy a Azure Search hogyan hajtja végre ezeket a szűrőket a begyűjtési szűrők szabályainak vizsgálatával. Ha összetett lambda kifejezésekkel rendelkező speciális szűrőket ír, akkor ez a cikk hasznos lehet a szűrőkben rejlő lehetőségek megismerésének kialakításában és a miért.

További információ a begyűjtési szűrők szabályairól, beleértve a példákat: [OData-gyűjtési szűrők hibaelhárítása Azure Search](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>A gyűjtési szűrők korlátozása

Három alapvető oka van annak, hogy az összes szűrési funkció nem támogatott az összes típusú gyűjtemény esetében:

1. Bizonyos adattípusok esetében csak bizonyos operátorok támogatottak. Így például nem érdemes összehasonlítani a `true` logikai értékeket, `lt`és `false` `gt`a,, és így tovább.
1. A Azure Search nem támogatja a **korrelált keresést** a következő `Collection(Edm.ComplexType)`típusú mezőkben:.
1. A Azure Search invertált indexeket használ a szűrők végrehajtásához az összes típusú adathoz, beleértve a gyűjteményeket is.

Az első ok a OData nyelv és a EDM típusának meghatározása. Az utolsó kettőt részletesebben ismertetjük a cikk további részében.

## <a name="correlated-versus-uncorrelated-search"></a>Korrelált és nem korrelált keresés

Ha összetett objektumok gyűjteményéhez több szűrési feltételt alkalmaz, a feltételek **korrelálnak** , mivel azok *a gyűjtemény egyes objektumaira*vonatkoznak. A következő szűrő például olyan szállodákat ad vissza, amelyek legalább egy deluxe szobával rendelkeznek, 100-nál kisebb sebességgel:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Ha a szűrésnem korrelált, a fenti szűrő olyan szállodákat ad vissza, amelyekben egy szoba Deluxe, egy másik helyiség pedig 100-nál kisebb alapértékkel rendelkezik. Ez nem lenne értelme, mivel a lambda kifejezés mindkét záradéka ugyanarra a tartományra vonatkozik, azaz `room`. Ez az oka, hogy az ilyen szűrők korrelálnak.

A teljes szöveges kereséshez azonban nincs mód arra, hogy egy adott tartomány változóra hivatkozzon. Ha a mezőhöz tartozó kereséssel [teljes Lucene-lekérdezést](query-lucene-syntax.md) szeretne kiadni:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

Előfordulhat, hogy a rendszer visszaküldi a szállodát, ahol az egyik szoba Deluxe, a leírásban pedig egy másik szoba a "City View" kifejezésre hivatkozik. Az alábbi `Id` `1` dokumentum például megfelel a lekérdezésnek:

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

Ennek az az oka `Rooms/Type` , hogy a teljes `Rooms/Description`dokumentumban a `Rooms/Type` mező összes elemzett feltételére hivatkozik, és Hasonlóképpen, az alábbi táblázatokban látható módon.

`Rooms/Type` A teljes szöveges keresés tárolása:

| Kifejezés a`Rooms/Type` | Dokumentumok azonosítói |
| --- | --- |
| deluxe | 1, 2 |
| standard | 1 |

`Rooms/Description` A teljes szöveges keresés tárolása:

| Kifejezés a`Rooms/Description` | Dokumentumok azonosítói |
| --- | --- |
| Courtyard | 2 |
| city | 1 |
| kertészkedni | 1 |
| nagy | 1 |
| Motel | 2 |
| szoba | 1, 2 |
| standard | 1 |
| Suite | 1 |
| megtekintés | 1 |

Tehát a fenti szűrővel ellentétben, ami alapvetően azt mondja, hogy "a dokumentumok `Type` egyeztetése, ha egy szoba a" Deluxe szoba " `BaseRate` értékkel rendelkezik, és **ugyanazon a helyen** kevesebb mint 100", `Rooms/Type` a keresési lekérdezés azt írja le, hogy "megfelel a" Deluxe "kifejezéssel rendelkező dokumentumoknak "és `Rooms/Description` a" City View "kifejezéssel rendelkezik. Az egyes helyiségek nem rendelkeznek olyan fogalmakkal, amelyek mezői összekapcsolhatók az utóbbi esetben.

> [!NOTE]
> Ha szeretné megtekinteni az Azure Searchhoz hozzáadott korrelált keresés támogatását, kérjük, szavazzon [erre a felhasználói](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)hangelemre.

## <a name="inverted-indexes-and-collections"></a>Invertált indexek és gyűjtemények

Előfordulhat, hogy észrevette, hogy az összetett gyűjteményeknél jóval kevesebb korlátozás vonatkozik a lambda kifejezésekre, mint például a `Collection(Edm.Int32)`( `Collection(Edm.GeographyPoint)`z), és hasonló egyszerű gyűjtemények esetében. Ennek az az oka, hogy Azure Search a komplex gyűjteményeket az aldokumentumok tényleges gyűjteményének tárolja, míg az egyszerű gyűjtemények egyáltalán nem gyűjteményként vannak tárolva.

Tegyük fel például, hogy egy szűrhető karakterlánc `seasons` -gyűjtési mező, például egy online kiskereskedő indexe. Az indexbe feltöltött egyes dokumentumok így néznek ki:

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

A `seasons` mező értékét egy **fordított index**nevű struktúrában tárolja a rendszer, amely a következőhöz hasonlóan néz ki:

| Kifejezés | Dokumentumok azonosítói |
| --- | --- |
| Spring | 1, 2 |
| nyári | 1 |
| esik | 1, 2 |
| téli | 2, 3 |

Ezt az adatszerkezetet úgy tervezték, hogy nagy sebességgel válaszoljon egy kérdésre: Mely dokumentumokban jelenik meg egy adott kifejezés? A kérdés megválaszolásához hasonlóan működik egy egyszerű egyenlőségi vizsgálat, mint egy gyűjteményben. Valójában ez azért van így, mert a karakterlánc-gyűjtemények esetében a `eq` Azure Search csak a lambda `any`kifejezésben engedélyezi az összehasonlítást.

Az egyenlőségből való kiépítés után a következő lépésben megvizsgáljuk, hogyan lehet egyszerre több egyenlő ellenőrzést egyesíteni ugyanazon a `or`tartományon belüli változóban. Az algebra és [a mennyiségileg](https://en.wikipedia.org/wiki/Existential_quantification#Negation)kiterjesztő tulajdonságának köszönhetően működik. Ez a kifejezés:

    seasons/any(s: s eq 'winter' or s eq 'fall')

a következő azonos:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

a két `any` alkifejezést pedig hatékonyan lehet végrehajtani a fordított index használatával. Emellett [a mennyiségi](https://en.wikipedia.org/wiki/Existential_quantification#Negation)felmondás törvényének köszönhetően a következő kifejezés:

    seasons/all(s: s ne 'winter' and s ne 'fall')

a következő azonos:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

Ezért lehetséges a és `all` `ne` `and`a használata.

> [!NOTE]
> Bár a részletek ezen dokumentum hatókörén kívül esnek, ugyanazok az alapelvek a [földrajzi térbeli pontok gyűjteményeit](search-query-odata-geo-spatial-functions.md) is kiterjesztik a távolságra és a metszeti tesztekre. Ezért `any`:
>
> - `geo.intersects`nem lehet megfosztani
> - `geo.distance`össze kell hasonlítani `lt` a vagy a használatával`le`
> - a `or`kifejezéseket kombinálni kell, nem`and`
>
> A Converse szabályok érvényesek `all`.

A `lt` `Collection(Edm.Int32)` (z),, és `ge` operátorokat támogató `gt` `le`adattípusok, például például a-gyűjtemények esetében a kifejezések szélesebb választéka engedélyezett. `or` `and` `any`Pontosabban használhatja a -tésa-tis,feltéve,hogyazalapulszolgálóösszehasonlítókifejezésektartományhozviszonyítvavannakösszehasonlítvaahasználatával,amelyekezutántovábbkombinálhatókahasználatával.`or` `and` A logikai kifejezések ezen struktúrájának neve [diszjunkt kötelezőségi Normal Form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), más néven "Örs of and". Ezzel szemben az `all` ehhez az adattípusokhoz tartozó lambda kifejezéseknek a [Conjunctive normál formában (cnf)](https://en.wikipedia.org/wiki/Conjunctive_normal_form)kell lenniük, más néven a "Örs and". Azure Search lehetővé teszi az ilyen címtartomány-összehasonlítást, mert a lefordított indexekkel hatékonyan hajthatja végre őket, ugyanúgy, mint a karakterláncok gyors kifejezésének kereséséhez.

Összefoglalva: Itt láthatók a lambda kifejezésben engedélyezett, a következőre vonatkozó szabályok:

- `geo.intersects` `geo.distance` `lt` `le` A pozitív ellenőrzések mindig megengedettek, például az egyenlőség, a tartomány-összehasonlítás, vagy a (z) vagy a ("közelség" `any` távolság).
- A szolgáltatáson belül `any`mindig engedélyezett. `or` Csak olyan adattípusokhoz használható `and` , amelyek expressz tartomány-ellenőrzéseket használhatnak, és csak akkor, ha a and (DNF)-t használja.
- A-ben a szabályok fordítottak, csak a *negatív ellenőrzéseket* lehet használni, a mindig `and` használható, és csak a and-ben kifejezett tartomány-ellenőrzéseknél lehet használni. `or` `all`

A gyakorlatban ezek azok a szűrők típusai, amelyeknek a legvalószínűbb, hogy amúgy is használni fogjuk. Továbbra is hasznos lehet, ha tisztában van azzal, hogy mi is lehetséges.

Adott példákat, amelyek esetében engedélyezett a szűrők típusa, és amelyek nem léteznek, tekintse meg az [érvényes gyűjteményi szűrők írását](search-query-troubleshoot-collection-filters.md#bkmk_examples)ismertető témakört.

## <a name="next-steps"></a>További lépések  

- [OData-gyűjtési szűrők hibaelhárítása Azure Search](search-query-troubleshoot-collection-filters.md)
- [Szűrők a Azure Searchban](search-filters.md)
- [A OData kifejezés nyelvének áttekintése Azure Search](query-odata-filter-orderby-syntax.md)
- [Azure Search OData-kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
