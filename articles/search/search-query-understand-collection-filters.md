---
title: Az OData-gyűjteményszűrőinek ismertetése
titleSuffix: Azure Cognitive Search
description: Ismerje meg az OData-gyűjteményszűrők működésének mechanikáját az Azure Cognitive Search-lekérdezésekben, beleértve a gyűjtemények egyedi korlátait és viselkedését.
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
ms.openlocfilehash: f6e8ed5baef9b8594bb1fe03942e831fd8264a56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113070"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Az OData-adatgyűjtési szűrők ismertetése az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive Search gyűjteménymezőinek [szűréséhez](query-odata-filter-orderby-syntax.md) használhatja a [ `any` és `all` ](search-query-odata-collection-operators.md) az operátorokat **lambda kifejezésekkel**együtt. A Lambda-kifejezések logikai kifejezések, amelyek **tartományváltozóra**hivatkoznak. A `any` `all` és operátorok hasonlóak a ciklus a `for` legtöbb programozási nyelvek, a tartomány változó figyelembe szerepét hurok változó, és a lambda kifejezés, mint a törzs a hurok. A tartományváltozó a gyűjtemény "aktuális" értékét veszi fel a hurok iterációja során.

Legalábbis fogalmilag így működik. A valóságban az Azure Cognitive Search a szűrőket a hurkok működésétől `for` teljesen eltérő módon valósítja meg. Ideális esetben ez a különbség láthatatlan lenne az Ön számára, de bizonyos helyzetekben nem. A végeredmény az, hogy vannak szabályok, amelyeket be kell tartania a lambda kifejezések írásakor.

Ez a cikk ismerteti, hogy miért léteznek a gyűjteményszűrőkre vonatkozó szabályok, ha feltárja, hogy az Azure Cognitive Search hogyan hajtja végre ezeket a szűrőket. Ha komplex lambda kifejezésekkel rendelkező speciális szűrőket ír, hasznosnak találhatja ezt a cikket a szűrőkben lehetséges lehetőségek megértésében, és miért.

A gyűjteményszűrők revonatkozó szabályairól, példákat is beleértve, az [Azure Cognitive Search OData-gyűjteményszűrőinek hibaelhárítása című témakörben olvashat.](search-query-troubleshoot-collection-filters.md)

## <a name="why-collection-filters-are-limited"></a>Miért korlátozottak a kollekciós szűrők?

Három oka van annak, hogy miért nem minden szűrőfunkció támogatott minden típusú gyűjteményesetében:

1. Bizonyos adattípusok esetében csak bizonyos operátorok támogatottak. Például nincs értelme összehasonlítani a logikai értékeket, `true` és `false` a `lt` `gt`, és így tovább.
1. Az Azure Cognitive Search nem támogatja a `Collection(Edm.ComplexType)` **korrelált keresést** a típusú mezőkön.
1. Az Azure Cognitive Search fordított indexeket használ a szűrők végrehajtására az összes típusú adat, beleértve a gyűjteményeket.

Az első ok csak annak a következménye, hogy az OData nyelv és az EDM típusú rendszer definiálva van. Az utolsó kettőt részletesebben ismertetjük a cikk többi részében.

## <a name="correlated-versus-uncorrelated-search"></a>Korrelált és nem kapcsolódó keresés

Ha összetett objektumok gyűjteményére több szűrőfeltételt alkalmaz, a feltételek **korrelálnak,** mivel *a gyűjtemény minden objektumára*vonatkoznak. A következő szűrő például olyan szállodákat ad vissza, amelyek legalább egy deluxe szobával rendelkeznek, 100-nál kisebb árral:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Ha a szűrés nem volt *korrelált*, a fenti szűrő visszaszállhat a szállodákba, ahol az egyik szoba deluxe, egy másik szoba alapdíja pedig kevesebb, mint 100. Ennek semmi értelme, mivel a lambda kifejezés mindkét záradéka ugyanarra a `room`tartományváltozóra vonatkozik, nevezetesen a . Ez az oka annak, hogy az ilyen szűrők korrelálnak.

A teljes szöveges kereséshez azonban nem lehet egy adott tartományváltozóra hivatkozni. Ha a mezőalapú kereséssel a következőhöz hasonló [teljes Lucene-lekérdezést](query-lucene-syntax.md) ad ki:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

lehet, hogy a szállodák vissza, ahol egy szoba deluxe, és egy másik szoba említi "városra néző" a leírásban. Például az alábbi `Id` dokumentum `1` a volna egyezik a lekérdezés:

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

Ennek az `Rooms/Type` az oka, hogy a `Rooms/Type` teljes dokumentum ban a mező `Rooms/Description`összes elemzett kifejezésére vonatkozik, és hasonlóképpen a , ahogy az az alábbi táblázatokban látható.

Hogyan `Rooms/Type` történik a teljes szöveges keresés tárolása:

| Kifejezés`Rooms/Type` | Dokumentumazonosítók |
| --- | --- |
| Deluxe | 1, 2 |
| Standard | 1 |

Hogyan `Rooms/Description` történik a teljes szöveges keresés tárolása:

| Kifejezés`Rooms/Description` | Dokumentumazonosítók |
| --- | --- |
| Courtyard | 2 |
| city | 1 |
| Kert | 1 |
| Nagy | 1 |
| Motel | 2 |
| Szoba | 1, 2 |
| Standard | 1 |
| Suite | 1 |
| Nézd | 1 |

Tehát ellentétben a fenti szűrő, amely alapvetően `Type` azt mondja: "mérkőzés dokumentumok, `BaseRate` ahol a szoba egyenlő "Deluxe Room", `Rooms/Type` és **hogy ugyanabban** `Rooms/Description` a szobában kevesebb, mint 100", a keresési lekérdezés azt mondja: "mérkőzés dokumentumok, ahol a "deluxe", és a "városnézet". Nincs fogalma az egyes szobák, amelyek területén lehet korrelál az utóbbi esetben.

> [!NOTE]
> Ha szeretné, hogy az Azure Cognitive Search hozzáadja a korrelált keresés támogatását, kérjük, szavazzon erre a [User Voice elemre.](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)

## <a name="inverted-indexes-and-collections"></a>Fordított indexek és gyűjtemények

Lehet, hogy észrevette, hogy sokkal kevesebb korlátozás lambda kifejezések összetett gyűjtemények, `Collection(Edm.Int32)`mint `Collection(Edm.GeographyPoint)`vannak az egyszerű gyűjtemények, mint, , és így tovább. Ennek az az oka, hogy az Azure Cognitive Search az összetett gyűjteményeket aldokumentumok tényleges gyűjteményeként tárolja, míg az egyszerű gyűjtemények egyáltalán nem tárolódnak gyűjteményekként.

Vegyünk például egy szűrhető `seasons` karakterlánc-gyűjteménymezőt, például egy online kiskereskedő indexében. Az indexbe feltöltött egyes dokumentumok a következőkre néznek ki:

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

A mező `seasons` értékeit egy **fordított indexnek**nevezett struktúrában tárolják, amely valahogy így néz ki:

| Időtartam | Dokumentumazonosítók |
| --- | --- |
| Tavaszi | 1, 2 |
| Nyári | 1 |
| Esik | 1, 2 |
| Téli | 2, 3 |

Ez az adatstruktúra egy kérdésre nagy sebességgel válaszol: Mely dokumentumok jelennek meg egy adott kifejezésben? A kérdés megválaszolása inkább egyszerű egyenlőségi ellenőrzésként működik, mint egy gyűjtemény feletti hurok. Valójában ez az oka annak, hogy a `eq` karakterlánc-gyűjtemények, az Azure `any`Cognitive Search csak lehetővé teszi, mint egy összehasonlító operátor belsejében lambda kifejezés.

Kiépítése az egyenlőség, a következő fogjuk nézni, hogyan lehet kombinálni több egyenlőség `or`ellenőrzések ugyanazon a tartomány változó . Úgy működik, köszönhetően algebra és [az elosztó tulajdonsága quantifiers](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Ez a kifejezés:

    seasons/any(s: s eq 'winter' or s eq 'fall')

egyenértékű a következőkkal:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

és a két `any` részkifejezés mindegyike hatékonyan hajtható végre a fordított index használatával. Is, hála [a negation törvény quantifiers](https://en.wikipedia.org/wiki/Existential_quantification#Negation), ez a kifejezés:

    seasons/all(s: s ne 'winter' and s ne 'fall')

egyenértékű a következőkkal:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

ezért lehetséges a használatával és `all` `and` `ne` a alkalmazásával.

> [!NOTE]
> Bár a részletek túlmutatnak e dokumentum hatókörén, ugyanezek az elvek kiterjednek a [távolságra és a tértéri pontok gyűjteményének metszéspontjaira](search-query-odata-geo-spatial-functions.md) is. Ez az oka annak, hogy: `any`
>
> - `geo.intersects`nem lehet cáfolni
> - `geo.distance`össze kell `lt` hasonlítani a`le`
> - a kifejezéseket a `or``and`
>
> Az ezzel szemben `all`érvényes szabályok ra vonatkoznak.

A kifejezések szélesebb `lt`skálája engedélyezett a , `gt`a , `le`és `ge` operátorokat támogató adattípusok `Collection(Edm.Int32)` gyűjteményeinek szűrésekénte, például. Pontosabban használhatja, `and` `or` valamint a `any`, mindaddig, amíg az alapul szolgáló összehasonlító kifejezések kombinálva `or`tartomány **összehasonlítások** segítségével `and`, amelyeket aztán tovább kombinálva . A logikai kifejezések ezen szerkezetét [disjunctive normal form (DNF) (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form)néven nevezik, más néven "AND-k csak-k". Ezzel szemben az ilyen adattípusokhoz tartozó `all` lambda kifejezéseknek [kötőhelyes normál (CNF) (KNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form)formátumban kell lenniük, más néven "legkülső régiók AND-i". Az Azure Cognitive Search lehetővé teszi az ilyen tartomány-összehasonlításokat, mert hatékonyan végrehajthatja azokat fordított indexek használatával, csakúgy, mint a karakterláncok gyors kifejezéskeresését.

Összefoglalva, itt vannak a szabályok a hüvelykujj, amit megengedett a lambda kifejezés:

- `any`Belül, *a pozitív ellenőrzések* mindig megengedettek, mint `geo.intersects`például az egyenlőség, a tartomány összehasonlítása, vagy `geo.distance` a "közelségre" hasonlít, `lt` `le` mint az egyenlőségre, amikor a távolság ellenőrzéséről van szó).
- `any`Belül, `or` mindig megengedett. Csak olyan `and` adattípusokhoz használható, amelyek képesek tartományellenőrzéseket kifejezni, és csak akkor, ha and-ek (DNF) legkülső adatait használja.
- Belül `all`a szabályok sztornírozva vannak - csak *negatív ellenőrzések* engedélyezettek, mindig használhatja, `and` és csak a legkülső régiók AND-jeként (CNF) kifejezett tartományellenőrzésekhez használható. `or`

A gyakorlatban ezek azok a szűrők, amelyeket a legnagyobb valószínűséggel használ. Ez még mindig hasznos, hogy megértsék a határait, hogy mi lehetséges mégis.

A szűrők típusainak használatára vonatkozó konkrét példákat az [Érvényes gyűjteményszűrők írása (Útmutató az érvényes gyűjteményszűrők írása) (Útmutató az érvényes gyűjteményszűrők írása) témakörben talál.](search-query-troubleshoot-collection-filters.md#bkmk_examples)

## <a name="next-steps"></a>További lépések  

- [OData-gyűjteményszűrők hibaelhárítása az Azure Cognitive Search szolgáltatásban](search-query-troubleshoot-collection-filters.md)
- [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
- [Az Azure Cognitive Search OData-kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezés szintaxisának hivatkozása](search-query-odata-syntax-reference.md)
- [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
