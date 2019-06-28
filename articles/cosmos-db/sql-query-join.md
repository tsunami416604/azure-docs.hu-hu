---
title: Az Azure Cosmos DB SQL JOIN lekérdezéseket
description: Csatlakozás SQL-szintaxis ismerje meg az Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 408ee11b318143b3128833a741e04dd68f3816ed
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342904"
---
# <a name="joins-in-azure-cosmos-db"></a>Az Azure Cosmos DB csatlakozik

Egy relációs adatbázisban illesztések több tábla a logikai corollary normalizált sémák tervezéséhez. Ezzel szemben a az SQL API-t használ-e a denormalizált data model séma nélküli elemek, amely a logikai megfelelője a *önillesztést*.

Belső illesztések egy teljes körű a részt vesz a join készlet keresztszorzatát eredményez. Az N-módon való csatlakozás eredménye egy készletét N-elem rekordokat, ahol a rekord minden egyes érték társítva az aliasnevet a JOIN beállítása a résztvevő, és ez az alias más záradékban való hivatkozással érhető el.

## <a name="syntax"></a>Szintaxis

A nyelv támogatja a szintaxis `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Ez a lekérdezés a rekordok készletét adja vissza `N` értékeket. Minden egyes rekord összes tároló-alias léptetés keresztül az adott csoportok által előállított értékkel rendelkezik. 

Nézzük meg, a FROM záradék a következő: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Lehetővé teszik az egyes forrás megadása `input_alias1, input_alias2, …, input_aliasN`. A FROM záradék az N-rekordokat tartalmazó (N értékekkel rendelkező rekordot) adja vissza. Minden egyes rekord összes tároló-alias léptetés keresztül az adott csoportok által előállított értékkel rendelkezik.  
  
**1. példa** – 2 források  
  
- Lehetővé teszik `<from_source1>` tároló-tartozni, és a set {A, B, C} képviseli.  
  
- Lehetővé teszik `<from_source2>` dokumentum hatókörű input_alias1 hivatkozik, és csoportok képviselik:  
  
    {1, 2} számára `input_alias1 = A,`  
  
    {3} a `input_alias1 = B,`  
  
    {4, 5} számára `input_alias1 = C,`  
  
- A FROM záradék `<from_source1> JOIN <from_source2>` a következő rekordok felsorolásának eredményez:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**2. példa** – 3 források  
  
- Lehetővé teszik `<from_source1>` tároló-tartozni, és a set {A, B, C} képviseli.  
  
- Lehetővé teszik `<from_source2>` kell hivatkozik a dokumentum-hatáskörű `input_alias1` és -mappáknak a csoportok tartalmazzák:  
  
    {1, 2} számára `input_alias1 = A,`  
  
    {3} a `input_alias1 = B,`  
  
    {4, 5} számára `input_alias1 = C,`  
  
- Lehetővé teszik `<from_source3>` kell hivatkozik a dokumentum-hatáskörű `input_alias2` és -mappáknak a csoportok tartalmazzák:  
  
    {100, 200-as} számára `input_alias2 = 1,`  
  
    {300} a `input_alias2 = 3,`  
  
- A FROM záradék `<from_source1> JOIN <from_source2> JOIN <from_source3>` a következő rekordok felsorolásának eredményez:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200-AS), (B, 3, 300)  
  
  > [!NOTE]
  > Hiánya más értékkel rekordokat tartalmazó `input_alias1`, `input_alias2`, amelyhez a `<from_source3>` nem adott vissza semmilyen értéket.  
  
**3. példa** – 3 források  
  
- Tudassa < from_source1 > kell a tároló hatókörű, és a set {A, B, C} képviseli.  
  
- Lehetővé teszik `<from_source1>` tároló-tartozni, és a set {A, B, C} képviseli.  
  
- < From_source2 > hivatkozó input_alias1 dokumentum hatókörű, és képviselik a készletek lehetővé teszik:  
  
    {1, 2} számára `input_alias1 = A,`  
  
    {3} a `input_alias1 = B,`  
  
    {4, 5} számára `input_alias1 = C,`  
  
- Lehetővé teszik `<from_source3>` tartozni `input_alias1` és -mappáknak a csoportok tartalmazzák:  
  
    {100, 200-as} számára `input_alias2 = A,`  
  
    {300} a `input_alias2 = C,`  
  
- A FROM záradék `<from_source1> JOIN <from_source2> JOIN <from_source3>` a következő rekordok felsorolásának eredményez:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200-AS) (A, 2, 100), (A, 2, 200-AS), C, 4, 300, (C, 5, 300)  
  
  > [!NOTE]
  > Ennek következtében több termék között `<from_source2>` és `<from_source3>` mindkét hatóköre ugyanaz, mert `<from_source1>`.  Ennek következtében a 4 (2 x 2) a érték kellene kellene B (1 x 0) értéket 0 rekordokat tartalmazó rekordok. és 2 (2 x 1) rekordokat tartalmazó c-értékkel  
  
## <a name="examples"></a>Példák

Az alábbi példák bemutatják, hogyan működik a JOIN záradékban. A következő példa eredménye nem üres, az egyes elemek forrásból keresztszorzatát óta, és egy üres készlet nem üres:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Az eredmény a következő:

```json
    [{
    }]
```

A következő példában a join több termék között két JSON-objektumok, a cikk alapvető `id` és a `children` subroot. Az a tény, hogy `children` tömböt nem a JOIN hatékony, mert a foglalkozik, amely egyetlen legfelső szintű a `children` tömb. Az eredmény tartalmazza a csak két eredmény, mivel minden elem a tömbben keresztszorzatát poskytne pontosan csak egy elemet.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Az eredmények a következők:

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

Az alábbi példa bemutatja a hagyományosabb csatlakozzon:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Az eredmények a következők:

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

A JOIN záradékban FROM forrása egy iterátor. Így az előző példában a folyamat a következő:  

1. Bontsa ki az egyes gyermekelemet `c` a tömbben.
2. A alkalmazni a legfelső szintű elem több termék `f` az egyes gyermekelemet `c` , amely az első lépés egybesimított.
3. Végül projektre a gyökérobjektum `f` `id` önálló tulajdonság.

Az első elem `AndersenFamily`, legalább egy csak `children` elem, így az eredményhalmaz csak egyetlen objektumot tartalmaz. A második elem `WakefieldFamily`, tartalmaz két `children`, így a több terméket hoz létre a két objektum, minden `children` elemet. Mindkét ezeket az elemeket a legfelső szintű mezőket ugyanazok, mint egy több termékben hiányol.

Az ILLESZTÉSI záradék a valódi segédprogramot a több terméket, amely egyébként nehéz a projekt egy alakzat az űrlap rekordokat. Egy rekord, amely lehetővé teszi, hogy a felhasználó által a rekordok felsorolásának általános elégedett feltétel kiválasztása kombinációjának szűrők az alábbi példában.

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

Az eredmények a következők:

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

Az előző példában a következő kiterjesztés dupla illesztést hajt végre. Több termék is megtekinthet, a következő ily módon kvázi kóddal:

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

`AndersenFamily` rendelkezik egy gyermek, akik egy kisállat rendelkezik, így a több terméket egy sort eredményez (1\*1\*1) a család. `WakefieldFamily` két gyermekemmel, kisállatok, akik csak az egyik van, de a gyermek van két kisállatok. Ehhez a termékcsaládhoz tartozó több termék poskytne 1\*1\*2 = 2 sorok.

A következő példában van egy kiegészítő szűrőt `pet`, amely nem tartalmazza az összes a rekordokat, ahol a kisállat neve nincs `Shadow`. Tömbök, a rekord elemek szűrőt származó rekordokat tartalmazó hozhat létre, és a project közül az elemek.

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

Az eredmények a következők:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>További lépések

- [Bevezetés](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Segédlekérdezések](sql-query-subquery.md)