---
title: SQL JOIN-lekérdezések Azure Cosmos DB
description: Ismerje meg, hogyan CSATLAKOZHAT több táblázathoz a Azure Cosmos DBban az adatlekérdezéshez
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871142"
---
# <a name="joins-in-azure-cosmos-db"></a>Illesztések Azure Cosmos DB

Egy rokon adatbázisban a táblázatok közötti illesztések a normalizált sémák megtervezéséhez használható logikaik. Ezzel szemben az SQL API a séma nélküli elemek denormalizált adatmodelljét használja, amely egy *Önillesztés*logikai megfelelője.

A belső illesztések az illesztésben részt vevő készletek teljes termékét eredményezik. Az N-Way illesztés eredménye egy N-Element rekordok, ahol a rekordban lévő minden érték társítva van az összekapcsolásban részt vevő alias-készlettel, és a többi záradékban található aliasra hivatkozva elérhető.

## <a name="syntax"></a>Szintaxis

A nyelv támogatja a `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`szintaxist. Ez a lekérdezés `N` értékekkel rendelkező rekordok-készletet ad vissza. Az egyes rekordhalmazok értékeit az összes tároló aliasnevének megismétlésével állítjuk be a megfelelő készleteken. 

Nézzük meg a következő FROM záradékot: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Hagyja, hogy minden forrás meghatározza a `input_alias1, input_alias2, …, input_aliasN`. Ez a FROM záradék N-rekordok-készletet ad vissza (N értékkel rendelkező rekord). Az egyes rekordhalmazok értékeit az összes tároló aliasnevének megismétlésével állítjuk be a megfelelő készleteken.  
  
**1. példa** – 2 forrás  
  
- Legyen `<from_source1>` tároló hatókörű, és az {A, B, C} készletet jelöli.  
  
- Lehetővé teszi, hogy `<from_source2>` dokumentumokra szűkített viszonyítási input_alias1 és a készleteket:  
  
    {1, 2} `input_alias1 = A,`  
  
    `input_alias1 = B,` {3}  
  
    {4, 5} `input_alias1 = C,`  
  
- A FROM záradék `<from_source1> JOIN <from_source2>` a következő rekordok eredményezi:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**2. példa** – 3 forrás  
  
- Legyen `<from_source1>` tároló hatókörű, és az {A, B, C} készletet jelöli.  
  
- Lehetővé teszi, hogy `<from_source2>` dokumentumokra szűkített viszonyítási `input_alias1` és a készleteket:  
  
    {1, 2} `input_alias1 = A,`  
  
    `input_alias1 = B,` {3}  
  
    {4, 5} `input_alias1 = C,`  
  
- Lehetővé teszi, hogy `<from_source3>` dokumentumokra szűkített viszonyítási `input_alias2` és a készleteket:  
  
    {100, 200} `input_alias2 = 1,`  
  
    `input_alias2 = 3,` {300}  
  
- A FROM záradék `<from_source1> JOIN <from_source2> JOIN <from_source3>` a következő rekordok eredményezi:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > A `input_alias1``input_alias2`egyéb értékeinek rekordok hiánya, amelyek esetében a `<from_source3>` nem adott vissza értéket.  
  
**3. példa** – 3 forrás  
  
- < From_source1 > tároló hatókörű, és az {A, B, C} készletet jelöli.  
  
- Legyen `<from_source1>` tároló hatókörű, és az {A, B, C} készletet jelöli.  
  
- < From_source2 > dokumentumokra szűkíthető viszonyítási input_alias1, és beállítja a készleteket:  
  
    {1, 2} `input_alias1 = A,`  
  
    `input_alias1 = B,` {3}  
  
    {4, 5} `input_alias1 = C,`  
  
- `<from_source3>` hatókörét a `input_alias1` és a készletek jelölésére:  
  
    {100, 200} `input_alias2 = A,`  
  
    `input_alias2 = C,` {300}  
  
- A FROM záradék `<from_source1> JOIN <from_source2> JOIN <from_source3>` a következő rekordok eredményezi:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > Ennek eredményeként a `<from_source2>` és a `<from_source3>` között több termék is van, mivel mindkettő ugyanarra a `<from_source1>`ra van hatókörben.  Ez 4 (2x2) rekordok eredményezett, amelynek értéke A 0 rekordok, amelynek értéke B (1x0) és 2 (2x1) rekordok, amelynek értéke C.  
  
## <a name="examples"></a>Példák

Az alábbi példák bemutatják, hogyan működik a JOIN záradék. A példák futtatása előtt töltse fel a minta [családi adatok](sql-query-getting-started.md#upload-sample-data)feltöltését. A következő példában az eredmény üres, mivel az egyes elemek forrásból és üres készletből származó szorzata üres:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Az eredmény a következőket eredményezi:

```json
    [{
    }]
```

A következő példában az illesztés egy két JSON-objektum, az elem gyökerének `id` és a `children` algyökerének egy közti terméke. Az a tény, hogy `children` egy tömb nem lép érvénybe a csatlakozásban, mert egyetlen, a `children` tömböt tartalmazó gyökérrel foglalkozik. Az eredmény csak két eredményt tartalmaz, mivel a tömbhöz tartozó egyes elemek több mint egyetlen elemét eredményezi.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Az eredmény a következő:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Az alábbi példa egy hagyományosabb illesztést mutat be:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Az eredmény a következő:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Az JOIN záradék FROM forrása egy iteráció. Így az előző példában szereplő folyamat a következőket eredményezi:  

1. Bontsa ki a tömb minden gyermek elemét `c`.
2. Alkalmazzon egy több terméket az elem gyökerével `f` az egyes alárendelt elemekkel `c` az első lépésnél.
3. Végül a főobjektumot a `f` `id` tulajdonsággal kell megtervezni.

Az első elem, `AndersenFamily`, csak egy `children` elemet tartalmaz, így az eredményhalmaz csak egyetlen objektumot tartalmaz. A második elem, `WakefieldFamily`, két `children`tartalmaz, így a termék két objektumot állít elő, egyet az egyes `children` elemekhez. Mindkét elem legfelső szintű mezői azonosak, ugyanúgy, mint a több termék esetében.

Az JOIN záradék valódi segédprogramja egy olyan alakzat rekordok, amely egyébként nehéz a projektben. Az alábbi példa egy olyan rekord kombinációján szűri a szűrőket, amely lehetővé teszi a felhasználó számára, hogy a rekordok teljes körűen kiválassza a feltételt.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Az eredmény a következő:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Az előző példa alábbi bővítménye dupla illesztést hajt végre. A terméket a következő pszeudo-kóddal tekintheti meg:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` egy olyan gyermektel rendelkezik, amely egy kisállattal rendelkezik, így a Cross termék egy sort (1\*1\*1) eredményez ebből a családból. `WakefieldFamily` két gyermeke van, és csak az egyiknek van háziállata, de a gyermeknek két háziállata van. A családhoz tartozó termék több mint 1\*1\*2 = 2 sort eredményez.

A következő példában egy további szűrő van `pet`, amely kizárja az összes olyan rekordok, amelynél a PET-név nem `Shadow`. A tömbökből létrehozhat rekordok, szűrheti a rekord bármelyik elemét, és az elemek bármely kombinációját kialakíthatja.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Az eredmény a következő:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>Következő lépések

- [Első lépések](sql-query-getting-started.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Allekérdezéseket](sql-query-subquery.md)