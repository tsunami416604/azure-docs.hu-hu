---
title: SQL JOIN-lekérdezések az Azure Cosmos DB-hez
description: Ismerje meg, hogyan csatlakozhat több táblát az Azure Cosmos DB-ben az adatok lekérdezéséhez
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871142"
---
# <a name="joins-in-azure-cosmos-db"></a>Csatlakozásaz az Azure Cosmos DB-ben

A relációs adatbázisokban a táblák közötti illesztések a normalizált sémák tervezésének logikai következménye. Ezzel szemben az SQL API a sémamentes elemek denormalizált adatmodelljét használja, amely az *önillesztés*logikai megfelelője.

A belső illesztések az illesztésben részt vevő készletek teljes kereszttermékét eredményezik. Az N-utas illesztés eredménye N-elem-tuples készlet, ahol a tuple minden egyes értéke az illesztésben részt vevő aliaskészlethez van társítva, és az aliasmás záradékokban való hivatkozásával érhető el.

## <a name="syntax"></a>Szintaxis

A nyelv támogatja a `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`szintaxist. Ez a lekérdezés értékekkel rendelkező `N` tuples-készletet ad vissza. Minden rekordot az összes tárolóalias iterálásával készített értékek a megfelelő készletek felett. 

Nézzük meg a következő FROM záradékot:`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Hagyja, hogy `input_alias1, input_alias2, …, input_aliasN`minden forrás határozza meg . Ez a FROM záradék N-tuples (n értékekkel való megbúni) halmazát adja vissza. Minden rekordot az összes tárolóalias iterálásával készített értékek a megfelelő készletek felett.  
  
**1. példa** – 2 forrás  
  
- Legyen `<from_source1>` tárolóhatókörrel, és képviselje a(z) {A, B, C} készletet.  
  
- Legyen `<from_source2>` dokumentum-hatókörű hivatkozva input_alias1, és képviseli készletek:  
  
    {1, 2}`input_alias1 = A,`  
  
    {3}a`input_alias1 = B,`  
  
    {4, 5}`input_alias1 = C,`  
  
- A FROM `<from_source1> JOIN <from_source2>` záradék a következő tuple-okat eredményezi:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Példa 2** - 3 forrás  
  
- Legyen `<from_source1>` tárolóhatókörrel, és képviselje a(z) {A, B, C} készletet.  
  
- Legyen `<from_source2>` dokumentum-hatókörű hivatkozás, `input_alias1` és képviseli készletek:  
  
    {1, 2}`input_alias1 = A,`  
  
    {3}a`input_alias1 = B,`  
  
    {4, 5}`input_alias1 = C,`  
  
- Legyen `<from_source3>` dokumentum-hatókörű hivatkozás, `input_alias2` és képviseli készletek:  
  
    {100, 200}`input_alias2 = 1,`  
  
    {300}a`input_alias2 = 3,`  
  
- A FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` záradék a következő tuple-okat eredményezi:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > A esetén a tuples `input_alias1` `input_alias2`hiánya, amelynek értékei `<from_source3>` nem adnak vissza értékeket.  
  
**3. példa** – 3 forrás  
  
- Hagyja, hogy <from_source1 tárolóhatókörrel és a(z) {A, B, C} készletre>.  
  
- Legyen `<from_source1>` tárolóhatókörrel, és képviselje a(z) {A, B, C} készletet.  
  
- Hagyja,> hogy <from_source2 dokumentumhatókörrel hivatkozva input_alias1 és a készleteket képviselje:  
  
    {1, 2}`input_alias1 = A,`  
  
    {3}a`input_alias1 = B,`  
  
    {4, 5}`input_alias1 = C,`  
  
- Legyen `<from_source3>` hatóköre, `input_alias1` és képviseli készletek:  
  
    {100, 200}`input_alias2 = A,`  
  
    {300}a`input_alias2 = C,`  
  
- A FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` záradék a következő tuple-okat eredményezi:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > Ez azt eredményezte, `<from_source2>` hogy `<from_source3>` a termékek között, `<from_source1>`és mivel mindkettő hatálya azonos .  Ez azt eredményezte, hogy 4 (2x2) tuple, amelynek A értéke, 0 buples értéke B (1x0) és 2 (2x1) tuples, amelyek C értékkel rendelkeznek.  
  
## <a name="examples"></a>Példák

A következő példák bemutatják, hogyan működik a JOIN záradék. Mielőtt futtatja ezeket a példákat, töltse fel a minta [család adatait](sql-query-getting-started.md#upload-sample-data). A következő példában az eredmény üres, mivel a forrásból származó egyes cikkek keresztterméke és egy üres készlet üres:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Az eredmény:

```json
    [{
    }]
```

A következő példában az illesztés két JSON-objektum, `id` a `children` cikkgyökér és az algyökér közötti kereszttermék. Az a `children` tény, hogy egy tömb nem hatékony az illesztés, mert `children` foglalkozik egyetlen gyökér, amely a tömb. Az eredmény csak két eredményt tartalmaz, mivel a tömböt tartalmazó cikkek keresztterméke pontosan csak egy cikket eredményez.

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

A következő példa egy hagyományosabb illesztést mutat be:

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

A JOIN záradék FROM forrása egy iterátor. Tehát az előző példában az áramlás a következő:  

1. Bontsa `c` ki a tömb minden gyermekelemét.
2. Vigyen fel egy keresztterméket `f` az elem `c` gyökerével minden olyan gyermekelemmel, amelyet az első lépés összeolvasztott.
3. Végül csak a `f` `id` gyökérobjektum tulajdonságot vetítse ki.

Az első `AndersenFamily`elem , `children` a , csak egy elemet tartalmaz, így az eredményhalmaz csak egyetlen objektumot tartalmaz. A második `WakefieldFamily`elem , `children`a , két elemet tartalmaz, `children` így a kereszttermék két objektumot hoz létre, egyet-egyet minden elemhez. Mindkét elem gyökérmezői megegyeznek, ahogy azt egy kereszttermékben elvárná.

A JOIN záradék valódi hasznossága az, hogy a kereszttermékből olyan formában, amelyet egyébként nehéz kivetíteni, a kereszttermékből származó tuple-kat képez. Az alábbi példa szűri a kombináció egy tuple, amely lehetővé teszi a felhasználó számára, hogy válasszon egy feltételt teljesüla a tuples összességében.

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

Az előző példa következő kiterjesztése kettős illesztést hajt végre. A keresztterméket a következő pszeudokódként tekintheti meg:

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

`AndersenFamily`van egy gyermeke, akinek egy háziállata van,\*\*így a kereszttermék egy sort (1 1 1) eredményez ebből a családból. `WakefieldFamily`két gyermeke van, akik közül csak az egyiknek van háziállata, de ennek a gyermeknek két háziállata van. Ennek a családnak a\*\*keresztterméke 1 1 2 = 2 sort eredményez.

A következő példában van egy `pet`további szűrő , amely kizárja az összes olyan `Shadow`rekord, ahol a kedvtelésből tartott állat neve nem . A tömbökből felveheti a tuple-okat, szűrheti a tuple bármely elemét, és kivetítheti az elemek bármilyen kombinációját.

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

## <a name="next-steps"></a>További lépések

- [Kezdetekhez](sql-query-getting-started.md)
- [Az Azure Cosmos DB .NET-mintái](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Segédlekérdezések](sql-query-subquery.md)