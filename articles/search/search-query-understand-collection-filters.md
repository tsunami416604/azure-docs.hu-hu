---
title: OData-fájlgyűjtési szűrők – Azure Search ismertetése
description: Tudnivalók a OData fájlgyűjtési szűrők működéséről az Azure Search-lekérdezéseket.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: 7af1b0ab95d04249d6d74e3324dbeb30eda6e1de
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079599"
---
# <a name="understanding-odata-collection-filters-in-azure-search"></a>OData-fájlgyűjtési szűrők az Azure Search ismertetése

A [szűrő](query-odata-filter-orderby-syntax.md) gyűjtemény mezők az Azure Search szolgáltatásban, használhatja a [ `any` és `all` operátorok](search-query-odata-collection-operators.md) együtt **lambda-kifejezésekkel**. Lambda-kifejezésekkel is logikai kifejezésen, amely hivatkozik a **tartomány változó**. A `any` és `all` operátorok a következők nevű egy `for` hurokban szinte bármelyik programozási nyelvével, az a tartomány változót véve a szerepkör változó, valamint a lambda kifejezés hurok törzsébe. A tartomány változót a ciklus ismétléseinek számától során a gyűjtemény "aktuális" értékét veszi fel.

Legalacsonyabb, hogyan működik a koncepciót tekintve at. A valóságban a végzett, az Azure Search szűrőket implementál nagyon eltérő úgy, hogy hogyan `for` munkahelyi hurkokat. Ideális esetben ez a különbség lenne, láthatatlan, de bizonyos helyzetekben nem. A végeredmény, hogy nincsenek-e szabályokat kell hajtsa végre a lambda-kifejezésekkel írásakor.

Ez a cikk azt ismerteti, miért a fájlgyűjtési szűrők szabályait létezik elemzésével miként az Azure Search végrehajtja ezeket a szűrőket. Ha speciális szűrők összetett lambda-kifejezésekkel szeretne írni, előfordulhat, ez a cikk a megértését, mi lehetséges a szűrők létrehozásakor hasznos, és miért.

Mi a szabályokat a fájlgyűjtési szűrők vannak, beleértve a példákat lásd: információ [hibaelhárítási OData fájlgyűjtési szűrők az Azure Search](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Miért érdemes a fájlgyűjtési szűrők korlátozva

Nincsenek három alapul szolgáló ok, amiért, miért nem minden szűrő funkció gyűjtemények minden alkalmazástípus esetében támogatott:

1. Csak bizonyos operátor bizonyos adattípusok használata támogatott. Például azt értelmetlen a logikai értékek összehasonlítására `true` és `false` használatával `lt`, `gt`, és így tovább.
1. Az Azure Search nem támogatja a **korrelált keresési** típusú mezők `Collection(Edm.ComplexType)`.
1. Azure Search az indexek adatok, beleértve a gyűjteményeket minden típusú szűrők végrehajtásához fordított.

Az első oka csak következménye EDM típus rendszer és az OData-nyelv definiálásának módját. Az utolsó két mutatjuk be részletesebben Ez a cikk további részében található.

## <a name="correlated-versus-uncorrelated-search"></a>Korrelált és predikátumban keresése

Több szűrőfeltételeket keresztül összetett objektumok gyűjteményét alkalmazásakor a feltételek vannak **korrelált** érvényesek, mivel *a gyűjtemény minden egyes objektumában*. Például az alábbi szűrők, amelyek 100-nál kevesebb arány legalább egy deluxe elegendő hellyel rendelkezik hotels adja vissza:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Ha szűrést volt *predikátumban*, a fenti szűrő előfordulhat, hogy adja vissza, ahol egy hely deluxe, és egy másik hely rendelkezik egy "Hotels" értékelje 100-nál kisebb. Amely nincs értelme megtiltani, mivel a lambda kifejezésnek mindkét záradékában nevezetesen vonatkoznak az ugyanazon tartomány változó `room`. Ezért az ilyen szűrők közötti kapcsolatot.

Azonban a teljes szöveges keresés, nincs lehetőség egy adott címtartomány változóra hivatkozik. Fielded keresési kiadására használatakor egy [teljes Lucene lekérdezési](query-lucene-syntax.md) erre, például:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

a leírás kaphat hotels vissza ahol egy szoba deluxe és a egy másik hely "város view" említi. Ha például a dokumentum alábbi `Id` , `1` megfelel a lekérdezés:

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

Az az oka, hogy `Rooms/Type` hivatkozik az elemzett feltételeket a `Rooms/Type` mezőt, a teljes dokumentumot, és ehhez hasonlóan a `Rooms/Description`, ahogyan az alábbi táblázatokban.

Hogyan `Rooms/Type` teljes szöveges keresés nem történik:

| A kifejezés `Rooms/Type` | Dokumentumazonosító |
| --- | --- |
| deluxe | 1, 2 |
| standard | 1 |

Hogyan `Rooms/Description` teljes szöveges keresés nem történik:

| A kifejezés `Rooms/Description` | Dokumentumazonosító |
| --- | --- |
| udvar | 2 |
| city | 1 |
| kertben | 1 |
| Nagy méretű | 1 |
| amelyben | 2 |
| Hely | 1, 2 |
| standard | 1 |
| Suite | 1 |
| megtekintés | 1 |

Így ellentétben a fenti szűrő, amely alapvetően szerint a "dokumentumokat, ahol a szoba rendelkezik `Type` "Deluxe Room"egyenlő és **ugyanabban a helyiségben** rendelkezik `BaseRate` 100-nál kevesebb", a keresési lekérdezés szerint "egyezés dokumentumok where `Rooms/Type`"deluxe" szól, és `Rooms/Description` "város nézet" a kifejezés tartozik. És nincs, amelynek mezői összekapcsolhatók legyenek az utóbbi esetben az egyes helyiségekben.

> [!NOTE]
> Ha szeretné látni támogatása hozzáadva az Azure Search korrelált keresésre, kérjük szavazzon [User Voice elem](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Fordított indexeket és a gyűjtemények

Talán észrevette, hogy nincsenek-e összetett gyűjtemények keresztül lambda-kifejezésekkel sokkal kevesebb korlátozások számánál egyszerű gyűjtemények hasonló `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`, és így tovább. Ennek az oka az Azure Search dokumentumgyűjteményként tényleges alárendelt, tárolja összetett gyűjtemények, amíg nem minden egyszerű gyűjtemények tárolt gyűjtemények.

Például, nézze meg például a gyűjtemény szűrhető karakterlánc típusú `seasons` egy online kereskedő az indexben. Ez az index feltöltött néhány dokumentumot ehhez hasonló lehet:

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

Értékét a `seasons` mező szerkezetet vannak tárolva egy **index fordított**, következőhöz hasonlóan néz ki:

| Kifejezés | Dokumentumazonosító |
| --- | --- |
| Spring | 1, 2 |
| nyár | 1 |
| esik a rendelkezésre állás | 1, 2 |
| winter | 2, 3 |

Ezt az adatstruktúrát célja nagy sebességű egy kérdésre válaszolnia: A dokumentumok jelenik meg egy adott időszak? A kérdés megválaszolása működik, több mint egy egyszerű egyenlőségének ellenőrzésén egy hurkot, mint egy gyűjtemény elemein. Sőt, ez a lehetőség miért karakterlánc gyűjtemények esetében, az Azure Search csak lehetővé teszi, hogy `eq` mint belül a lambda kifejezésnek egy összehasonlító operátor `any`.

Az egyenlőség kialakítását, ezután megnézzük, hogyan lehetséges úgy, hogy az ugyanazon tartomány változó több egyenlőség ellenőrzésére `or`. Köszönhetően algebra működik és [mennyiségi leírók elosztó tulajdonságát](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Ez a kifejezés:

    seasons/any(s: s eq 'winter' or s eq 'fall')

a következő azonos:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

a két minden `any` az alkifejezések hatékonyan hajtható végre az fordított index használatát. Ezenkívül köszönhetően a [mennyiségi leírók negálás joga](https://en.wikipedia.org/wiki/Existential_quantification#Negation), ez a kifejezés:

    seasons/all(s: s ne 'winter' and s ne 'fall')

a következő azonos:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

éppen ezért lehetséges használandó `all` a `ne` és `and`.

> [!NOTE]
> Bár a részleteket az túlmutat a jelen dokumentum, ezen ugyanezek az elvek kiterjeszteni [metszet és távolság tesztek a gyűjtemények térinformatikai pontok](search-query-odata-geo-spatial-functions.md) is. Ez az okokat, `any`:
>
> - `geo.intersects` nem lehet negated
> - `geo.distance` össze kell hasonlítani használatával `lt` vagy `le`
> - kifejezések kell kombinálni `or`, nem `and`
>
> A fordított szabályokat a alkalmazni `all`.

Többféle kifejezések engedélyezettek, ha szűrést az adatokat, hogy a `lt`, `gt`, `le`, és `ge` operátorok, mint például `Collection(Edm.Int32)` például. Pontosabban, használhatja `and` , valamint `or` a `any`, mindaddig, amíg az alapul szolgáló összehasonlítási kifejezésekben vannak egyesítve **összehasonlítások tartomány** használatával `and`, amelyeket majd további jellel `or`. Ez a struktúra logikai kifejezésen, nevezzük [diszjunkt normál űrlapot (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), más néven az "Equal ORs". Ezzel szemben, lambda-kifejezésekkel a `all` ezeket az adatokat típusokat kell [Conjunctive normál űrlapot (használható a cnf Paraméterrel)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), más néven az "Equal, or"használata. Az Azure Search szolgáltatással ilyen tartományt összehasonlítások, mert azt végre őket használatával fordított indexek hatékonyan, ugyanúgy, mint a végezhet gyors kifejezés keresési karakterláncokat.

Összefoglalva az alábbiakban a szabályok költségcsökkenést eredményezzen az engedélyezett a lambda kifejezésnek:

- Belül `any`, *pozitív ellenőrzések* mindig engedélyezettek, egyenlő, mint például tartomány-összehasonlítások, `geo.intersects`, vagy `geo.distance` képest `lt` vagy `le` (Képzelje úgy, mint például a "egyezés" egyenlőség esetén, a distance ellenőrzése).
- Belül `any`, `or` mindig engedélyezve van. Használhat `and` adattípusokat is express időtartam ellenőrzése, és csak akkor, ha csak az Equal ORs (DNF) használja.
- Belül `all`, a szabályok vannak fordított irányú – csak *ellenőrzések negatív* engedélyezettek, használhatja `and` mindig, és használhatja `or` csak a tartomány ellenőrzi, Equal, ORs (használható cnf Paraméterrel) fejezzük ki.

A gyakorlatban ezek a szűrők legtöbbször használó mégis Ön típusú. Továbbra is érdemes tudni, mi lehetséges azonban határain.

Milyen típusú szűrők engedélyezettek, és amelyek nem adott példákért lásd: [hogyan írhat érvényes fájlgyűjtési szűrők](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>További lépések  

- [Hibaelhárítás az Azure Search szolgáltatásban az OData-fájlgyűjtési szűrők](search-query-troubleshoot-collection-filters.md)
- [Szűrők az Azure Search szolgáltatásban](search-filters.md)
- [Az Azure Search OData kifejezés nyelvi – áttekintés](query-odata-filter-orderby-syntax.md)
- [Az Azure Search OData kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
