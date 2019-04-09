---
title: SQL-lekérdezések az Azure Cosmos DB-hez
description: További információk az SQL-szintaxis, adatbázis-tervezésben és SQL-lekérdezések az Azure Cosmos DB. Használ SQL-t egy Azure Cosmos DB JSON lekérdezési nyelvvel.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mjbrown
ms.openlocfilehash: 8e5c281a8a8b6c0b48f18bf247b451bf61a7e9dc
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263043"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>Az Azure Cosmos DB SQL lekérdezési példák

Azure Cosmos DB SQL API-fiókok lekérdezését elemek Structured Query Language (SQL) használatával, egy JSON-lekérdezési nyelvet támogatja. Az Azure Cosmos DB lekérdezési nyelv a a céljai a következők:

* Támogatja az SQL, ismerős és a népszerű lekérdezés helyett egy új lekérdezési nyelvre inventing nyelvek valamelyikével. SQL formális programozási modellt biztosít részletes lekérdezéseket a JSON-elemek keresztül.  

* JavaScript a programozási modellt használjuk alapját a lekérdezési nyelv. JavaScript típus system, kifejezés kiértékelése függvény meghívási jsou az SQL API gyökeréhez. Ezek gyökerek természetes programozási modell leképezések relációs, hierarchikus navigációs JSON-elemek között Önillesztések, térinformatikai lekérdezéseket, és hívja meg a felhasználó által definiált függvények (UDF), teljes mértékben javascriptben írt, funkciókat biztosítanak.

Ez a cikk végigvezeti néhány példa SQL-lekérdezéseket az egyszerű JSON-elemek. Azure Cosmos DB SQL nyelvi szintaxissal kapcsolatos további információkért lásd: [SQL-szintaxis referenciája](sql-api-query-reference.md).

## <a id="GettingStarted"></a>SQL-lekérdezések használatának első lépései

Az SQL API Cosmos DB-fiók létrehozása egy nevű tárolóban `Families`. Két egyszerű JSON-elemek létrehozása a tárolóban, és néhány egyszerű lekérdezést futtatta.

### <a name="create-json-items"></a>Hozzon létre JSON-elemek

Az alábbi kód létrehoz két egyszerű JSON-elemek családok kapcsolatban. Az Andersen és Wakefield családhoz egyszerű JSON-elemek közé tartozik a szüleinek, gyermekek és a kisállatok, cím és regisztrációs adatok. Az első elem van a karakterláncokat, számok, logikai értékek, tömbök és beágyazott tulajdonságok.


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

A második elemet használja `givenName` és `familyName` helyett `firstName` és `lastName`.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female", 
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>A JSON-elemek lekérdezése

Próbálja ki néhány lekérdezést elleni megérteni a legfontosabb szempontok az Azure Cosmos DB SQL lekérdezési nyelv a JSON-adatokat.

Az alábbi lekérdezés azon elemeit adja vissza. Ha a `id` egyezések mezőben `AndersenFamily`. Mivel ez egy `SELECT *` lekérdezést, a lekérdezés kimenete a teljes JSON-elem. SELECT szintaxissal kapcsolatos további információkért lásd: [SELECT utasítás](sql-api-query-reference.md#select-query). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

A lekérdezés eredményeit a következők: 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

A következő lekérdezés újraformázza a JSON-kimenet egy másik formázásához. A lekérdezés egy új JSON-projektek `Family` objektumot két kiválasztott mezőkkel `Name` és `City`, ha a cím, város ugyanaz, mint az állapot. "NY, NY" Ebben az esetben megegyezik.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

A lekérdezés eredményeit a következők:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

A következő lekérdezés a család adja vissza a megadott nevek a gyermekek amelynek `id` megegyezik `WakefieldFamily`, kategória szerint rendezett.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.grade ASC
```

Az eredmények a következők:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

Az előző példák bemutatják a Cosmos DB lekérdezési nyelv számos aspektusát:  

* Mivel az SQL API a JSON-értékeit működik, entitások fa alakú sorok és oszlopok helyett foglalkozik. Olvassa el a fa csomópontjai bármilyen tetszőleges mélységben például `Node1.Node2.Node3…..Nodem`, hasonlóan ahhoz, hogy a két részből hivatkozását `<table>.<column>` ANSI SQL-ben.

* A lekérdezési nyelv a séma nélküli adatokkal dolgozik, mert a típusa rendszer dinamikusan kell kötni. Egyazon kifejezésre sikerült eddig is számtalan előnyét eltérő konfigurációs elemeket a különböző típusú. A lekérdezés eredménye egy érvényes JSON-értéket, de nem valószínű, hogy a rögzített sémát.  

* Az Azure Cosmos DB támogatja a szigorú JSON-elemek csak. A rendszer típusa és -kifejezések korlátozódnak JSON típusú foglalkozik. További információkért lásd: a [JSON-specifikáció](https://www.json.org/).  

* A Cosmos DB-tárolók gyűjteménye sémamentes JSON-elemek. Belül és azok tároló elemek között kapcsolatok implicit módon nem az elsődleges kulcs-idegen kulcs kapcsolatokat által rögzített, tartalmazási. Ez a funkció fontos a cikkben később tárgyalt intra-cikk illesztéseket.

## <a id="SelectClause"></a>SELECT záradék

Minden egyes lekérdezés SELECT záradékában és választható FROM áll és a WHERE záradék ANSI SQL előírások szerint. Általában a forrás a FROM záradékban számbavétele megtörtént, és a WHERE záradékban szűrőt alkalmaz az adatforrás, JSON-elemek részhalmazának beolvasásához. A SELECT záradékban, majd a kért JSON-értékeket a kiválasztási listán projektek. A szintaxissal kapcsolatos további információkért lásd: [SELECT utasítás](sql-api-query-reference.md#select-query).

VÁLASSZA a következő lekérdezés például értéket ad vissza `address` a `Families` amelynek `id` megegyezik `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmények a következők:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a id="EscapingReservedKeywords"></a>Határolójeles tulajdonság hozzáférő
Tulajdonságok a határolójeles tulajdonság Operator használatával is elérheti. Ha például `SELECT c.grade` és `SELECT c["grade"]` egyenértékűek. Ez a szintaxis hasznos egy szóközt, speciális karaktereket tartalmaz, vagy a neve megegyezik egy SQL kulcsszó vagy fenntartott szó rendelkező tulajdonság karaktert.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>Beágyazott tulajdonságok

Az alábbi példa két beágyazott tulajdonságok, projektek `f.address.state` és `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmények a következők:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="json-expressions"></a>JSON-kifejezések

Leképezés a JSON-kifejezések, is támogatja, az alábbi példában látható módon:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmények a következők:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

Az előző példában a SELECT záradékban kell hozzon létre egy JSON-objektumot, és a mintául szolgáló kulcs nélkül biztosít, mivel a záradékot használ-e az implicit argumentum változó neve `$1`. A következő lekérdezés visszaadja az implicit argumentum két változót: `$1` és `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmények a következők:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="ValueKeyword"></a>ÉRTÉK kulcsszó

Az érték kulcsszó lehetővé teszi egyedül a JSON-értéket adnak vissza. Ha például az alábbi lekérdezés a skaláris kifejezést ad vissza `"Hello World"` helyett `{$1: "Hello World"}`:

```sql
    SELECT VALUE "Hello World"
```

A következő lekérdezés nélkül a JSON-értékeit adja vissza a `address` címkét:

```sql
    SELECT VALUE f.address
    FROM Families f
```

Az eredmények a következők:

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

Az alábbi példa bemutatja, hogyan állítható vissza a JSON egyszerű értékeket (a levélszintű a JSON-fa):


```sql
    SELECT VALUE f.address.state
    FROM Families f
```

Az eredmények a következők:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="aliasing"></a>Aliasképző

Így explicit módon alias értékek lekérdezésekben. Ha a lekérdezés két tulajdonság azonos nevű, használja a aliasképző egyikét vagy mindkettőt a Tulajdonságok átnevezése, így azok az előre jelzett eredmény van használatát.

Az AS-aliasképző használt kulcsszó nem kötelező használni, mint a második érték Segédben az alábbi példában látható módon `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmények a következők:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>FROM záradékban

A feladó (`FROM <from_specification>`) záradék nem kötelező, kivéve, ha a forrás van szűrve, vagy előre jelzett költségről később a lekérdezésben. A szintaxissal kapcsolatos további információkért lásd: [a szintaxis](sql-api-query-reference.md#bk_from_clause). A lekérdezés, például `SELECT * FROM Families` számba veszi át a teljes `Families` tároló. A speciális azonosító legfelső szintű is használható, ha a tároló a tároló nevének használata helyett.

A FROM záradék a következő szabályok lekérdezésenként érvényesíti:

* A tároló lehet aliassal, például `SELECT f.id FROM Families AS f` vagy egyszerűen csak `SELECT f.id FROM Families f`. Itt `f` a hivatkozási `Families`. MIVEL a egy nem kötelező kulcsszó használatával alias azonosítója.  

* Egyszer aliassal, az eredeti adatforrás neve nem köthető. Például `SELECT Families.id FROM Families f` szintaktikailag mert azonosító `Families` alias lett, és többé nem lehet feloldani.  

* Lehet, hogy az összes hivatkozott tulajdonság teljesen minősített, szigorú sémát megfelelést hiányában nem egyértelmű kötések elkerülése érdekében. Ha például `SELECT id FROM Families f` szintaktikailag mert tulajdonság `id` nincs kötve.

### <a name="get-subitems-by-using-the-from-clause"></a>A FROM záradék használatával elem beolvasása

A FROM záradék csökkentheti a forrás egy kisebb részhalmazra. Csak az egyes elemek részfájának enumerálása, a subroot válhat a forrás, az alábbi példában látható módon:

```sql
    SELECT *
    FROM Families.children
```

Az eredmények a következők:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Az előző lekérdezés egy tömb használja forrásként, de a forrásként egy objektumot is használhatja. A lekérdezés minden olyan érvényes, megadott JSON érték az eredménye, hogy a forrás figyelembe veszi. Az alábbi példa kizárja a `Families` , amely nem rendelkezik egy `address.state` értéket.

```sql
    SELECT *
    FROM Families.address.state
```

Az eredmények a következők:

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="WhereClause"></a>WHERE záradék

A választható WHERE záradék (`WHERE <filter_condition>`) feltétel(ek) megadja, hogy a forrás JSON-elemek eredmények tartalmazzák a lekérdezés meg kell felelniük. Egy JSON-elem ki kell értékelnie, hogy a megadott feltételeket `true` figyelembe kell venni az eredményt. Az index réteg használja a legkisebb részhalmazát forrás elemek, amelyek az eredmény része lehet meghatározni a WHERE záradékban. A szintaxissal kapcsolatos további információkért lásd: [WHERE szintaxis](sql-api-query-reference.md#bk_where_clause).

Az alábbi lekérdezés kérelmek elemek, amelyek tartalmaznak egy `id` tulajdonsága, amelynek az értéke `AndersenFamily`. Nem tartalmazza a minden olyan cikk, amely nem rendelkezik egy `id` tulajdonság, vagy amelynek az értéke nem egyezik `AndersenFamily`.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmények a következők:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>A WHERE záradékban skaláris kifejezések

Az előző példából kiderült, egy egyszerű egyenlőség lekérdezést. Az SQL API-t is támogatja a különböző [skaláris kifejezések](#scalar-expressions). A leggyakrabban használt olyan bináris és egyoperandusú kifejezés. A forrás JSON-objektumból tulajdonság hivatkozásokat akkor is érvényes kifejezések.

A következő támogatott bináris operátorok használhatók:  

|**Művelettípus**  | **Értékek** |
|---------|---------|
|Aritmetikai | +,-,*,/,% |
|Bitenkénti    | \|, &, ^, <<>>,, >>> (nulla ki jobbra tolást) |
|Logikai    | ÉS, VAGY SEM      |
|Összehasonlítás | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Karakterlánc     |  \|\| (fűzze össze) |

A következő lekérdezéseket a bináris operátor használható:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Is használhatja az egyoperandusú operátorokat +,-, ~, és nem a lekérdezéseket, a következő példákban szemléltetett módon:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Használhatja a tulajdonság hivatkozások a lekérdezésekben. Ha például `SELECT * FROM Families f WHERE f.isRegistered` tulajdonságot tartalmazó JSON-elemét adja vissza `isRegistered` értékkel egyenlőnek `true`. Bármely más érték például `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, vagy `<array>`, nem tartalmazza a cikk az eredményből. 

### <a name="equality-and-comparison-operators"></a>Egyenlőség és összehasonlító operátorok

Az alábbi táblázat egyenlőségi összehasonlítás eredménye minden két JSON-típusok között az SQL API-ban.

| **Op** | **Nem definiált** | **Null** | **Logikai** | **Szám** | **String** | **Objektum** | **Tömb** |
|---|---|---|---|---|---|---|---|
| **Nem definiált** | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Nem definiált |
| **Null** | Nem definiált | **OK** | Nem definiált | Meghatározatlan | Meghatározatlan | Meghatározatlan | Nem definiált |
| **Logikai** | Nem definiált | Nem definiált | **OK** | Nem definiált | Meghatározatlan | Meghatározatlan | Nem definiált |
| **Szám** | Nem definiált | Meghatározatlan | Nem definiált | **OK** | Nem definiált | Meghatározatlan | Nem definiált |
| **String** | Nem definiált | Meghatározatlan | Meghatározatlan | Nem definiált | **OK** | Nem definiált | Nem definiált |
| **Objektum** | Nem definiált | Meghatározatlan | Meghatározatlan | Meghatározatlan | Nem definiált | **OK** | Nem definiált |
| **Tömb** | Nem definiált | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Nem definiált | **OK** |

Az összehasonlító operátorok például `>`, `>=`, `!=`, `<`, és `<=`, típusában vagy a kettő közötti összehasonlítás objektumok vagy észszerűek Tárolótömböket `Undefined`.  

Ha a skaláris kifejezés eredménye `Undefined`, az elem nem található meg az eredményt, mivel `Undefined` nem egyenlő `true`.

### <a name="logical-and-or-and-not-operators"></a>Logikai (AND, OR és NOT) operátorok

Logikai operátorok a logikai értékek művelethez. Az alábbi táblázatokban logikai hiteles táblák ezen operátorok számára:

**VAGY a kezelő**

| VAGY | True (Igaz) | False (Hamis) | Meghatározatlan |
| --- | --- | --- | --- |
| True (Igaz) |True (Igaz) |True (Igaz) |True (Igaz) |
| False (Hamis) |True (Igaz) |False (Hamis) |Meghatározatlan |
| Meghatározatlan |True (Igaz) |Meghatározatlan |Nem definiált |

**ÉS operátor**

| ÉS | True (Igaz) | False (Hamis) | Meghatározatlan |
| --- | --- | --- | --- |
| True (Igaz) |True (Igaz) |False (Hamis) |Meghatározatlan |
| False (Hamis) |False (Hamis) |False (Hamis) |False (Hamis) |
| Meghatározatlan |Meghatározatlan |False (Hamis) |Nem definiált |

**NOT operátor**

| NEM |  |
| --- | --- |
| True (Igaz) |False (Hamis) |
| False (Hamis) |True (Igaz) |
| Meghatározatlan |Nem definiált |

## <a name="between-keyword"></a>Kulcsszó között

ANSI SQL, mint a BETWEEN kulcsszó használatával karakterlánc- vagy numerikus értékek tartományát lekérdezéseket express. Például a következő lekérdezést, amelyben az első alárendelt szintű 1-5, beleértve az összes elemet adja vissza.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Ellentétben az ANSI SQL-ben is használhatja a BETWEEN záradék a FROM záradék esetében az alábbi példában látható módon.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Az SQL API-t, ellentétben az ANSI SQL-lekérdezések vegyes típusú tulajdonságokhoz fejezhető. Például `grade` lehet egy szám hasonló `5` néhány elemet és a egy karakterlánc például `grade4` más. Ezekben az esetekben, JavaScript, mint a két különböző típusa közötti összehasonlítás eredménye `Undefined`, így az a cikk a rendszer kihagyta.

> [!TIP]
> Lekérdezés végrehajtása gyorsabb hozzon létre egy indexelési házirendet, amely minden olyan numerikus tulajdonságok vagy elérési utak a BETWEEN záradék szűri az index Tartománytípus használja.

## <a name="in-keyword"></a>A kulcsszó

Az IN kulcsszó használatával ellenőrizze, hogy egy megadott értéke megegyezik-e a lista bármely értéke. Például a következő lekérdezés az összes családi elemeket adja vissza, a `id` van `WakefieldFamily` vagy `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Az alábbi példa az összes elem visszaadása, ahol állapota a megadott értékeket:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="-operator"></a>* operátor

A speciális operátor *-projektek, a teljes elem-jébe. Használatakor a csak tervezett mezőt kell lennie. A lekérdezés, például `SELECT * FROM Families f` érvényes, de `SELECT VALUE * FROM Families f` és `SELECT *, f.id FROM Families f` nem érvényesek. A [ebben a cikkben elsőként lekérdezése](#query-the-json-items) használja a * operátor. 

## <a name="-and--operators"></a>? és?? Operátorok

Használhatja a Ternáris (?), és egyesítse a feltételes kifejezések, mint a programnyelvek például hozhat létre (?) operátorok C# és a JavaScript. 

Használhatja a? operátor menet közben új JSON-tulajdonságokkal létrehozására. Például a következő lekérdezés osztályozza a besorolási szintek be `elementary` vagy `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Hívások is beágyazhatja a? operátor szerinti szűrése, ahogy a következő lekérdezést: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Csakúgy, mint más lekérdezési operátorokkal a? operátor elemek nem tartalmazza, ha a hivatkozott tulajdonságai hiányoznak, vagy az összehasonlított típusok különböző.

Használja a?? operátor hatékonyan ellenőrzéséhez egy tulajdonságot egy elemet a szolgáltatásban tárolt részben strukturált vagy vegyes típusú adatok lekérdezésekor. Ha például az alábbi lekérdezés a értéket ad vissza `lastName` Ha jelen van, vagy `surname` Ha `lastName` nincs jelen.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>TOP operátor

A felső kulcsszó adja vissza az első `N` nem meghatározott sorrendben legyenek a lekérdezési eredmények száma. Az ajánlott eljárás használ a felső az ORDER BY záradékkal korlátozza az első eredményeket `N` rendezett értékek száma. Ezekkel a záradékokkal két kombinálásával az egyetlen módja lehetővé teszi a kiszámítható jelzi, amely sorok felső hatással van. 

FELSŐ is használhatja, vagy egy változó értéke a paraméteres lekérdezések használatával egy állandó értékkel, az alábbi példában látható módon. További információkért lásd: a [paraméterezett lekérdezések](#parameterized-queries) szakaszban.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Az eredmények a következők:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="OrderByClause"></a>ORDER BY záradék

ANSI SQL, mint egy nem kötelező ORDER BY záradék is megadhat, a lekérdezések. A választható ASC vagy DESC argumentum megadja, hogy lehet beolvasni az eredményeket növekvő vagy csökkenő sorrendben. ASC az alapértelmezett érték.

Ha például a következő családok növekvő sorrendben a tartózkodási város neve, amely:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Az eredmények a következők:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Az alábbi lekérdezés lekéri a család `id`s, az elem létrehozásának dátuma sorrendben. Elem `creationDate` van egy számot jelölő a *alapidőpont*, vagy 1970. január 1. másodpercek alatt óta eltelt idő.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Az eredmények a következők:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```
## <a name="scalar-expressions"></a>Skaláris kifejezések

A SELECT záradékban skaláris kifejezések állandók, számtani kifejezéseket és logikai kifejezéseket hasonlóan támogatja. A következő lekérdezés skaláris kifejezést használ:


```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Az eredmények a következők:

```json
    [{
      "$1": 1.33333
    }]
```

A következő lekérdezést a skaláris kifejezés eredménye egy logikai érték:


```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Az eredmények a következők:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="object-and-array-creation"></a>Az objektum és tömb létrehozása

Az SQL API egyik legfőbb jellemzője tömb- és létrehozása. Az előző példában létrehozott egy új JSON-objektum `AreFromSameCityState`. Tömbök, is hozhatnak létre, az alábbi példában látható módon:


```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Az eredmények a következők:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```


## <a id="Iteration"></a>Iteráció

Az SQL API egy új szerkezet az IN kulcsszó a FROM forrás hozzáadva a JSON-tömbök keresztül léptetés támogatást nyújt. Az alábbi példában:

```sql
    SELECT *
    FROM Families.children
```

Az eredmények a következők:

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

A következő lekérdezés végrehajtja az iteráció `children` a a `Families` tároló. A kimeneti tömbben eltér az előző lekérdezés. Ebben a példában bontja `children`, és simítja egybe az eredményeket egy egyetlen olyan tömböt be:  

```sql
    SELECT *
    FROM c IN Families.children
```

Az eredmények a következők:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Szűrheti a tömb minden egyes bejegyzés a további, az alábbi példában látható módon:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Az eredmények a következők:

```json
    [{
      "givenName": "Lisa"
    }]
```

Egy tömb iteráció eredményét feletti is lehet összesíteni. A következő lekérdezés például megszámlálja a gyermekek többek között az összes olyan családot:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Az eredmények a következők:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a id="Joins"></a>Illesztés

Egy relációs adatbázisban illesztések több tábla a logikai corollary normalizált sémák tervezéséhez. Ezzel szemben a az SQL API-t használ-e a denormalizált data model séma nélküli elemek, amely a logikai megfelelője a *önillesztést*.

A nyelv támogatja a szintaxis `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Ez a lekérdezés a rekordok készletét adja vissza `N` értékeket. Minden egyes rekord összes tároló-alias léptetés keresztül az adott csoportok által előállított értékkel rendelkezik. Más szóval ez a lekérdezés egy teljes a részt vesz a join készlet keresztszorzatát hajtja végre.

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

## <a id="UserDefinedFunctions"></a>Felhasználó által definiált függvények (UDF-EK)

Az SQL API támogatja a felhasználó által definiált függvények (UDF). Skaláris UDF-EK, a nulla vagy több argumentumot adja át, és a egy egyetlen argumentumot eredményt adja vissza. Az API-t minden argumentum jogi JSON-értékeit is ellenőrzi.  

Az API-t kibővíti az SQL-szintaxis az UDF-EK használatával egyéni alkalmazáslogika támogatása. Felhasználói függvények regisztrálásához az SQL API-val, és hivatkozni tudjon rájuk az SQL-lekérdezéseket. Sőt az UDF-EK exquisitely tervezték, hogy a lekérdezések, hívja meg. Maradhassanak felhasználói függvények nem rendelkezik hozzáféréssel a context objektumot, például a JavaScript más típusú, például a tárolt eljárásokkal és eseményindítókkal. Lekérdezések csak olvasható, és futtathatja vagy elsődleges vagy másodlagos replikákon. Futtassa a másodlagos replikákon UDF-EK, ellentétben más JavaScript típusú lettek kialakítva.

Az alábbi példa egy UDF-elem tárolóban regisztrálja a Cosmos DB-adatbázisban. A példa létrehoz egy UDF, amelynek a neve `REGEX_MATCH`. Két JSON karakterlánc értéket fogad el `input` és `pattern`, és ellenőrzi, hogy az első megegyezik a második megadott a minta használata JavaScript `string.match()` függvény.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Most már használja egy query projection az UDF. A kis-és nagybetűket előtaggal kell minősíti UDF-EK `udf.` azokat belül lekérdezések hívásakor.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Az eredmények a következők:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Az UDF együtt használhatja a `udf.` előtag belül egy szűrő, az alábbi példában látható módon:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Az eredmények a következők:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

Lényegében az UDF-EK érvényes skaláris kifejezés, amelyet használhat, a leképezések és a szűrők.

Bontsa ki az UDF-EK hatékonyságát, feltételes logikát az erre egy másik példát:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

Az alábbi példa az UDF-ben él:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Az eredmények a következők:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Ha a tulajdonságok szerinti által az UDF paraméterek nem érhetők el a JSON-értékben, a paraméter minősül nincs definiálva, és a rendszer kihagyta az UDF-hívás. Ehhez hasonlóan az UDF eredménye nem definiált, ha azt nem szerepel az eredményben.

Ahogy az előző példák azt mutatják, UDF-EK integrálása JavaScript nyelven hatékonyságát az SQL API. UDF-EK egy részletes programozható felületet összetett eljárásokról, feltételes logikát, beépített JavaScript futtatókörnyezet képességek segítségével. Ehhez adja meg. Az SQL API-t biztosít az argumentumok az UDF-EK egyes forrás elem az aktuális HELYÉT, vagy a SELECT záradékban feldolgozási szintre. Az eredmény zökkenőmentesen a teljes végrehajtási folyamat részét képezik. Összefoglalva UDF-EK olyan összetett üzleti logikát ehhez a lekérdezések részeként nagyszerű eszközöket.

## <a id="Aggregates"></a>Aggregátumfüggvények

Aggregátumfüggvények számítás elvégzése a SELECT záradékban szereplő értékek egy halmazát, és egyetlen értéket ad vissza. Például a következő lekérdezés belül elemek számát adja meg a `Families` tároló:

```sql
    SELECT COUNT(1)
    FROM Families f
```

Az eredmények a következők:

```json
    [{
        "$1": 2
    }]
```

Csak a skaláris érték az összesítést is visszaadható érték kulcsszó használatával. Például a következő lekérdezést egy egyetlen számot ad vissza értékek száma:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Az eredmények a következők:

```json
    [ 2 ]
```

Összesítések szűrőket is kombinálhatók. Például a következő lekérdezést a cím állapotú elemek számát küldi vissza `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Az eredmények a következők:

```json
    [ 1 ]
```

Az SQL API támogatja a következő összesítő függvények. ÖSSZEG és az átlagos numerikus értékeken működnek, és száma, minimális és maximális dolgozhat számok, karakterláncok, logikai és null értékeket.

| Függvény | Leírás |
|-------|-------------|
| COUNT | A kifejezésben található elemek számát adja vissza. |
| SUM   | A kifejezésben található értékek összegét adja vissza. |
| MIN   | A kifejezés minimumértékét adja vissza. |
| MAX   | A kifejezés maximumértékét adja vissza. |
| AVG   | A kifejezésben található értékek átlagát adja vissza. |

Egy tömb iteráció eredményét feletti is lehet összesíteni. További információkért lásd: a [iteráció](#Iteration) szakaszban.

> [!NOTE]
> Az Azure portal adatkezelőjében összesítési lekérdezések csak egy lekérdezést lap alatt előfordulhat, hogy összesítés részleges eredményeket. Az SDK összes oldalain egyetlen összesített értéket állít elő. Kód használatával összesítési lekérdezések végrehajtásához, a .NET SDK-val 1.12.0, .NET Core SDK 1.1.0-ás vagy a Java SDK 1.9.5 szüksége vagy újabb.
>

## <a id="BuiltinFunctions"></a>Beépített függvények

A cosmos DB is támogatja a beépített funkciók számos gyakori műveletekhez, például a felhasználó által definiált függvények (UDF) lekérdezések belül használhatja.

| Csoport | Műveletek |
|---------|----------|
| Matematikai függvények | ABS, FELSŐ HATÁR, EXP, EMELET, LOG, LOG10, POWER, CIKLIKUS, BEJELENTKEZÉSI, SQRT, SZÖGLETES, CSONK, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| A functions típus ellenőrzése | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Sztringfüggvények | CONCAT, TARTALMAZ, ENDSWITH, A INDEX_OF, A BAL OLDALON, A HOSSZA, A LOWER, LTRIM, CSERÉLJE LE, REPLIKÁLÁSA, FORDÍTOTT JOBB OLDALI RTRIM, STARTSWITH, FELSŐ KARAKTERLÁNCRÉSZLETET |
| Tömb funkciók | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH és ARRAY_SLICE |
| Térbeli funkciók | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Ha jelenleg használja a felhasználói függvény (UDF), amelynek beépített függvény már elérhető, a megfelelő beépített függvény nem futtatásához gyorsabb és hatékonyabb.

ANSI SQL és Cosmos DB-függvények közötti fő különbség az, Cosmos DB-függvények tervezték, hogy jól működnek a séma- és vegyes séma adatok. Például ha egy tulajdonság hiányzik, vagy egy nem numerikus értéket, például `unknown`, a rendszer kihagyta az elem helyett hibát adnak vissza.

### <a name="mathematical-functions"></a>Matematikai függvények

A matematika függvényekkel hajtsa végre a számítási, amelyek argumentumként szolgálnak, és a egy numerikus értéket adja vissza a bemeneti értékek alapján. A következő támogatott beépített matematikai függvények tábláját.

| Használat | Leírás |
|----------|--------|
| ABS (num_expr) | A megadott numerikus kifejezés (pozitív) abszolút értékét adja vissza. |
| CEILING (num_expr) | A legkisebb egész értéket ad vissza, nagyobb vagy egyenlő a megadott numerikus kifejezés. |
| FLOOR (num_expr) | Visszaadja a legnagyobb egész szám kisebb vagy egyenlő a megadott numerikus kifejezés. |
| EXP (num_expr) | A megadott numerikus kifejezés hatványát adja vissza. |
| NAPLÓ (num_expr, base) | A megadott numerikus kifejezés, vagy a megadott használatával logaritmus természetes alapú logaritmusát adja vissza. |
| LOG10 (num_expr) | A megadott numerikus kifejezés 10-es logaritmikus értékét adja vissza. |
| ROUND (num_expr) | Egy numerikus értéket, kerekítve a legközelebbi egész értéket ad vissza. |
| TRUNC (num_expr) | Egy numerikus érték, csonkolva, a legközelebbi egész értéket ad vissza. |
| SQRT (num_expr) | A megadott numerikus kifejezés négyzetgyökét adja vissza. |
| SQUARE (num_expr) | A megadott numerikus kifejezés négyzetét adja vissza. |
| POWER (num_expr, num_expr) | A megadott numerikus kifejezés hatékonyságát megadott értéket adja vissza. |
| BEJELENTKEZÉS (num_expr) | A bejelentkezés (-1, 0, 1) a megadott numerikus kifejezés értékét adja vissza. |
| ACOS (num_expr) | Adja vissza a szög radiánban, amelynek a koszinusza a megadott numerikus kifejezés; egy szám arkusz koszinusza néven is ismert. |
| ASIN (num_expr) | Adja vissza a szög radiánban, amelynek szinusza a megadott numerikus kifejezés. Ez a függvény egy szám arkusz szinusza is nevezik. |
| ATAN (num_expr) | Adja vissza a szög radiánban, amelynek tangense a megadott numerikus kifejezés. Ez a függvény egy szám arkusz tangense is nevezik. |
| ATN2 (num_expr) | Adja vissza a szög radiánban, közötti pozitív x tengely és a ray a forrásból a pont (y, x), ahol x és y értékek a két megadott úszó kifejezésre. |
| COS (num_expr) | A megadott kifejezést az radiánban megadott szög, trigonometriai koszinuszát adja vissza. |
| COT (num_expr) | A megadott szög trigonometriai kotangensét adja vissza radiánban, a megadott numerikus kifejezés. |
| DEGREES (num_expr) | A megfelelő szöget adja vissza fokban a egy radiánban megadott szög. |
| PI () | Az állandó a PI értékét adja vissza. |
| RADIANS (num_expr) | Adja vissza radiánban, ha egy numerikus kifejezés, fokban is meg kell adni. |
| SIN (num_expr) | A megadott kifejezést az radiánban megadott szög, trigonometriai szinuszát adja vissza. |
| TAN (num_expr) | A bemeneti kifejezést tangensét adja vissza a megadott kifejezésben. |

Az alábbi példához hasonlóan lekérdezéseket is futtathat:

```sql
    SELECT VALUE ABS(-4)
```

Az eredmény a következő:

```json
    [4]
```

### <a name="type-checking-functions"></a>A functions típus ellenőrzése

A típus ellenőrzése függvények ellenőrizheti egy SQL-lekérdezésben lévő kifejezés típusa. Típus ellenőrzése funkciók segítségével határozhatja meg a tulajdonságokat elemek menet közben, ha változó vagy ismeretlen. A következő típus ellenőrzése támogatott beépített függvények táblázatát:

| **Használat** | **Leírás** |
|-----------|------------|
| [IS_ARRAY (kifejezés)](sql-api-query-reference.md#bk_is_array) | Egy logikai érték, amely azt jelzi, ha az érték típusa tömböt ad vissza. |
| [IS_BOOL (kifejezés)](sql-api-query-reference.md#bk_is_bool) | Egy logikai jelezve, hogy ha az érték típusa egy logikai érték beolvasása. |
| [IS_NULL (kifejezés)](sql-api-query-reference.md#bk_is_null) | Adja vissza egy logikai érték, amely azt jelzi, ha az érték típusa null. |
| [IS_NUMBER (kifejezés)](sql-api-query-reference.md#bk_is_number) | Egy logikai érték, amely azt jelzi, ha az érték típusa egy számot ad vissza. |
| [IS_OBJECT (kifejezés)](sql-api-query-reference.md#bk_is_object) | Egy logikai érték, amely azt jelzi, ha az érték típusa egy JSON-objektumot ad vissza. |
| [IS_STRING (kifejezés)](sql-api-query-reference.md#bk_is_string) | Egy logikai érték, amely azt jelzi, ha az érték típusa egy karakterláncot ad vissza. |
| [IS_DEFINED (kifejezés)](sql-api-query-reference.md#bk_is_defined) | Jelzi, ha a tulajdonság hozzá lett rendelve egy érték logikai érték beolvasása. |
| [IS_PRIMITIVE (expr)](sql-api-query-reference.md#bk_is_primitive) | Adja vissza egy logikai érték, amely azt jelzi, ha az érték típusa egy karakterlánc, szám, logikai vagy null. |

Ezek a függvények használatával, az alábbi példához hasonlóan lekérdezéseket is futtathat:

```sql
    SELECT VALUE IS_NUMBER(-4)
```

Az eredmény a következő:

```json
    [true]
```

### <a name="string-functions"></a>Sztringfüggvények

A következő skaláris függvények végrehajtania egy műveletet a bemeneti karakterlánc-érték, és a egy karakterlánc, numerikus vagy logikai értéket adja vissza. A következő beépített karakterlánc-függvények táblázatát:

| Használat | Leírás |
| --- | --- |
| [A hossz (str_expr)](sql-api-query-reference.md#bk_length) | A megadott karakterlánc-kifejezés karakterek számát adja vissza. |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | Legalább két karakterlánc-értékek összetűzésének eredménye karakterláncként adja vissza. |
| [KARAKTERLÁNCRÉSZLET (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | Egy karakterlánc-kifejezés részét adja vissza. |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | Adja vissza egy logikai jelzi-e az első karakterlánc-kifejezés második kezdődik. |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | Adja vissza egy logikai jelzi-e az első karakterlánc-kifejezés második végződik. |
| [TARTALMAZZA (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | Visszaadja egy logikai arról a második-e az első karakterlánc-kifejezést tartalmaz. |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | A második első előfordulásának kezdőpozícióját adja vissza az első megadott karakterlánc-kifejezés vagy -1 kifejezés karakterlánc-Ha a karakterlánc nem található. |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | A megadott számú karakterből álló karakterlánc bal oldali részét adja vissza. |
| [JOBB (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | A megadott számú karaktert a karakterlánc jobb oldali részét adja vissza. |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | Egy karakterlánc-kifejezés adja vissza, miután eltávolítja a vezető üres. |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | Egy karakterlánc-kifejezés után az összes záró szóközöket csonkolására adja vissza. |
| [ALSÓ (str_expr)](sql-api-query-reference.md#bk_lower) | Egy karakterlánc-kifejezés nagybetűt adatok átalakítása kisbetűvé után adja vissza. |
| [FELSŐ (str_expr)](sql-api-query-reference.md#bk_upper) | Egy karakterlánc-kifejezés után kisbetűt adatok nagybetűssé alakításával adja vissza. |
| [Cserélje le a (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | A megadott karakterlánc értéket az összes előfordulását lecseréli egy másik karakterláncérték. |
| [REPLICATE (str_expr, num_expr)](sql-api-query-reference.md#bk_replicate) | Egy karakterláncértéket a megadott számú alkalommal ismétlődik. |
| [FORDÍTOTT (str_expr)](sql-api-query-reference.md#bk_reverse) | A karakterlánc-érték megfelelő sorrendben adja vissza. |

Ezek a függvények használatával, például a következőképpen, amely visszaadja a család lekérdezéseket is futtathat `id` nagybetűs:

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

Az eredmények a következők:

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

Vagy az összefűzés, mint ebben a példában:

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

Az eredmények a következők:

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "Seattle,WA"
    }]
```

Használhatja a szűrése a WHERE záradékban eredményez, például az alábbi példában a karakterlánc-függvények is:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

Az eredmények a következők:

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Tömb funkciók

A következő skaláris függvények végrehajtania egy műveletet a egy tömb bemeneti érték, és a egy szám, logikai vagy tömb értéket adja vissza. A következő beépített tömb függvények táblázatát:

| Használat | Leírás |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |A megadott tömb kifejezés elemek számát adja vissza. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |Egy tömb, amely az eredménye, összefűzi a két vagy több tömb értéket adja vissza. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |Jelzi, hogy a tömb tartalmazza-e a megadott érték logikai érték beolvasása. Megadhatja, ha az egyezés-e a teljes vagy részleges. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |Egy tömböt megadó kifejezést részét adja vissza. |

Tömb függvények használatával módosíthatja a tömb JSON belül. Itt például van egy lekérdezést, amely visszaadja az összes elem `id`s, ha egy, a `parents` van `Robin Wakefield`: 

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

Az eredmény a következő:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Az egyező elemeket a tömbön belüli részleges töredéket is megadhat. A következő lekérdezés megkeresi az összes elem `id`elemeket, amelyeknek `parents` együtt a `givenName` , `Robin`:

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

Az eredmény a következő:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Egy másik példa számú ARRAY_LENGTH használó `children` termékcsalád száma:

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

Az eredmények a következők:

```json
    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]
```

### <a name="spatial-functions"></a>Térbeli funkciók

A cosmos DB a következő nyissa meg a földrajzi Consortium (OGC) beépített függvények támogatja a térinformatikai lekérdezéséhez: 

| Használat | Leírás |
| --- | --- |
| ST_DISTANCE (point_expr, point_expr) | A két GeoJSON között adja vissza a távolság `Point`, `Polygon`, vagy `LineString` kifejezéseket. |
| T_WITHIN (point_expr, polygon_expr) | Visszaadja egy logikai kifejezés, amely azt jelzi-e az első GeoJSON-objektumot (`Point`, `Polygon`, vagy `LineString`) található meg a második GeoJSON-objektumot (`Point`, `Polygon`, vagy `LineString`). |
| ST_INTERSECTS (spatial_expr, spatial_expr) | Egy logikai kifejezés, jelezve, hogy a két megadott GeoJSON-objektumot ad vissza (`Point`, `Polygon`, vagy `LineString`) átfedésben. |
| ST_ISVALID | Visszaadja egy logikai érték, amely azt jelzi-e a megadott GeoJSON `Point`, `Polygon`, vagy `LineString` kifejezés érvénytelen. |
| ST_ISVALIDDETAILED | Tartalmazó logikai érték, ha JSON értéket ad vissza a megadott GeoJSON `Point`, `Polygon`, vagy `LineString` kifejezés érvénytelen, ha érvénytelen, és karakterláncként okát. |

Térbeli funkciók segítségével közelségi lekérdezéseket futtassanak a térbeli adatok. Például a következő egy lekérdezést, amely belüli 30 km-re a ST_DISTANCE beépített függvény használatával egy megadott hely összes családi elemeket adja vissza:

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

Az eredmény a következő:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

A Cosmos DB-ben térinformatikai támogatási további információkért lásd: [térinformatikai adatok az Azure Cosmos DB](geospatial.md). 

## <a name="parameterized-queries"></a>A paraméteres lekérdezések

A cosmos DB támogatja a lekérdezések az ismerős @ jelöléssel kifejezett paraméterekkel. Paraméteres SQL hatékony kezelése és a felhasználói adatbevitel escape-karaktersorozat biztosít, és megakadályozza, hogy a SQL-injektálás az adatok véletlen kapta.

Például írhat egy lekérdezést, amely a `lastName` és `address.state` meg paraméterként, és hajtsa végre, a különböző értékek `lastName` és `address.state` felhasználói bemenet alapján.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Majd elküldheti a kérelem Cosmos DB-hez egy paraméteres JSON lekérdezést a következőhöz hasonló:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Az alábbi példa egy parametrizált lekérdezés első argumentumban állítja be: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

A paraméter értéke lehet bármely érvényes JSON: karakterláncok, számok, logikai értékek, NULL értékű, akkor is igaz, tömbök, vagy beágyazott JSON. Mivel a Cosmos DB séma nélküli, paraméterek bármilyen nem ellenőrzi.

## <a id="JavaScriptIntegration"></a>JavaScript-integráció

Az Azure Cosmos DB programozási modellt biztosít a JavaScript-alapú alkalmazáslogika végre közvetlenül a tárolók, a tárolt eljárások és eseményindítók használatával. Ez a modell támogatja:

* Nagy teljesítményű tranzakciós CRUD-műveletek és a egy tárolóban, a mély integráció, a JavaScript futtatókörnyezet belül az adatbázismotor-hez tartozó elemek-lekérdezéseket.
* A természetes modellezési átvitelvezérlés, változó felmerülő, és a hozzárendelés és adatbázis-tranzakciók kivételkezelési primitívek integrációja. 

Az Azure Cosmos DB a JavaScript-integrációval kapcsolatos további információkért lásd: a [JavaScript kiszolgálóoldali API](#JavaScriptServerSideApi) szakaszban.

### <a name="operator-evaluation"></a>Kiértékelési operátor

A cosmos DB, egy JSON-adatbázis tartozóként rajzol a JavaScript-operátorok és értékelési szemantika parallels. A cosmos DB megpróbálja megőrizni a JavaScript szemantika JSON-támogatás tekintetében, de a művelet kiértékelése mintától, bizonyos esetekben.

Az SQL API-ellentétben a hagyományos SQL-ben a típusú értékek gyakran nem ismert mindaddig, amíg az API-t az adatbázisból olvassa be az értékeket. Annak érdekében, hogy hatékonyan hajtsa végre a lekérdezéseket, az operátorok a legtöbb szigorú adattípus-követelményekkel rendelkeznek.

A JavaScript, ellentétben az SQL API végre implicit konverzió. Például egy lekérdezést, például `SELECT * FROM Person p WHERE p.Age = 21` tartalmazó megegyezik egy `Age` tulajdonsága, amelynek az értéke `21`. Nem megfelelő bármely más elem, amelynek `Age` tulajdonsága egyezést mutat valószínűleg végtelen változata létezik, például a `twenty-one`, `021`, vagy `21.0`. Ezzel ellentétben, ahol a JavaScript, ahol karakterlánc-értékeket implicit módon leadott számok alapján operátor szerinti szűrése, például: `==`. Ez a viselkedés az SQL API elengedhetetlen a hatékony index egyeztetéséhez.

## <a id="ExecutingSqlQueries"></a>SQL-lekérdezésének végrehajtása

Bármilyen, HTTP/HTTPS-kérelem indítására képes nyelv meghívhat a Cosmos DB – REST API-hoz. A cosmos DB is biztosít programozási könyvtárakat, .NET, Node.js, JavaScript és Python programozási nyelveket. A REST API és tárak összes támogatják a lekérdezése SQL használatával, és a .NET SDK-t is támogatja [LINQ lekérdezés](#Linq).

Az alábbi példák bemutatják, hogyan hozzon létre egy lekérdezést, és küldje el azt egy Cosmos DB-adatbázisfiók ellen.

### <a id="RestAPI"></a>REST API-VAL

A cosmos DB egy megnyitott RESTful programozási modellt kínál a HTTP-n keresztül. Az erőforrás-modellje több erőforrást, adatbázis-fiókja alatt áll. amely egy Azure-előfizetés rendelkezések. Az adatbázis-fiókot egy készlete áll *adatbázisok*, is tartalmazhat, amelyek mindegyike több *tárolók*, amelyek viszont tartalmaznak *elemek*, UDF-EK és más erőforrástípusok. Mindegyik Cosmos DB erőforrás címmel rendelkező logikai és stabil URI segítségével. Erőforráscsoport neve egy *hírcsatorna*. 

Az alapszintű interakció ezekkel az erőforrásokkal modellje a HTTP-műveletek keresztül `GET`, `PUT`, `POST`, és `DELETE`, az a szabványos értelmezéseit. Használat `POST` hozzon létre egy új erőforrást, egy tárolt eljárás végrehajtása, vagy kiállítani egy Cosmos DB lekérdezéssel. Lekérdezések mindig csak olvasható műveletekhez, nincs mellékhatásokkal.

Az alábbi példák mutatják a `POST` egy SQL API-lekérdezés, a minta elemek esetén. A lekérdezés tartalmaz egy egyszerű szűrő a JSON `name` tulajdonság. A `x-ms-documentdb-isquery` és a Content-Type: `application/query+json` fejlécek jelölésére, hogy-e a művelet egy lekérdezést. Cserélje le `mysqlapicosmosdb.documents.azure.com:443` URI-a Cosmos DB-fiókjához.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Az eredmények a következők:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

A következő, összetettebb lekérdezés több eredmények illesztés adja vissza:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": [] 
    }
```

Az eredmények a következők: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

A lekérdezési eredmények nem fér el egyetlen lapon, ha a REST API-t adja vissza egy folytatási tokent keresztül a `x-ms-continuation-token` válaszfejléc. Ügyfelek eredmények oldalakra bontása fel a fejléc a következő eredményeket is. Eredmények száma oldalanként keresztül számát is szabályozhatja a `x-ms-max-item-count` szám fejléc. 

Ha a lekérdezés száma például egy összesítő függvényt tartalmaz, a lekérdezés lap csak egy oldalnyi találatot részlegesen összesített értéket adhat vissza. Az ügyfelek végre kell hajtania egy második szintű összesítés keresztül ezekkel az eredményekkel el a végső eredményt. Például összeg fölé a számát adja vissza az egyes lapok teljes száma.

Lekérdezések az adatok konzisztencia-szabályzat kezeléséhez használja a `x-ms-consistency-level` hasonlóan minden REST API-kérelem fejléce. Munkamenet-konzisztencia is szükséges a legújabb echo `x-ms-session-token` a lekérdezési kérelem cookie-fejlécet. A lekérdezett tároló indexelési házirendet is befolyásolhatja a lekérdezési eredmények konzisztenciáját. Az alapértelmezett indexelési házirend-beállítások tárolókhoz, az index mindig aktuális elem tartalmát, és lekérdezési eredmények megfelelnek a kiválasztott adatok konzisztencia. További információkért lásd: [Azure Cosmos DB konzisztenciaszintjeinek][consistency-levels].

Ha a beállított indexelési házirendet a tárolón a megadott lekérdezés nem támogatja, az Azure Cosmos DB kiszolgáló 400 "Hibás kérés" adja vissza. Ez a hibaüzenet a lekérdezésekhez az indexelő kifejezetten kizárva elérési úttal adja vissza. Megadhatja a `x-ms-documentdb-query-enable-scan` fejlécet, hogy a lekérdezést, hogy vizsgálatot végezzen, ha az index nem érhető el.

Megjelenik a részletes mérőszámokat a lekérdezés-végrehajtás beállításával a `x-ms-documentdb-populatequerymetrics` fejlécet `true`. További információkért lásd: [az Azure Cosmos DB SQL-lekérdezés metrikák](sql-api-query-metrics.md).

### <a id="DotNetSdk"></a>C# (.NET SDK)

A .NET SDK támogatja a LINQ- és SQL lekérdezése. Az alábbi példa bemutatja, hogyan hajthat végre a fenti szűrő lekérdezés a .NET használatával:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Az alábbi példa összehasonlítja a két tulajdonság hasonlítania az egyezés keresésekor minden elemen belül, és használja a névtelen leképezések.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

A következő példa bemutatja az illesztések, LINQ keresztül `SelectMany`.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

A .NET-ügyfél automatikusan végighalad a lekérdezési eredményeket minden oldalát a `foreach` blokkolja, az előző példában látható módon. A lekérdezési beállítások rendszerben bevezetett a [REST API-val](#RestAPI) szakaszban is rendelkezésre állnak a .NET SDK használatával a `FeedOptions` és `FeedResponse` osztályoknak a `CreateDocumentQuery` metódus. A lapok száma segítségével szabályozhatja a `MaxItemCount` beállítás.

Explicit módon is szabályozhatja, lapozási létrehozásával `IDocumentQueryable` használatával a `IQueryable` objektumot, majd olvassa el a` ResponseContinuationToken` értékeket, és átadja azokat a biztonsági másolatot `RequestContinuationToken` a `FeedOptions`. Beállíthat `EnableScanInQuery` vizsgálatok engedélyezéséhez, ha a lekérdezés a konfigurált indexelési szabályzat által nem támogatott. A particionált tárolók használhatja `PartitionKey` a lekérdezés futtatásához egy olyan partíciót, bár az Azure Cosmos DB automatikusan kinyerheti az Ez a lekérdezés szövege a. Használhat `EnableCrossPartitionQuery` több partíciót irányuló lekérdezések futtatása.

.NET minták további lekérdezésekkel, tekintse meg a [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet) a Githubon.

### <a id="JavaScriptServerSideApi"></a>JavaScript server-side API

A cosmos DB-alapú JavaScript-alkalmazáslogika végre közvetlenül a tárolók, a tárolt eljárások és eseményindítók használatával programozási modellt biztosít. A JavaScript-logika regisztrálva a tároló szintjén majd adhat ki az adatbázis-műveleteket a megadott tárolóhoz, a környezeti ACID-tranzakciókat csomagolni elemeket.

Az alábbi példa bemutatja, hogyan használható `queryDocuments` az API-t a lekérdezéseket a JavaScript-kiszolgálón belül tárolt eljárások és eseményindítók:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="Linq"></a>LINQ SQL API-hoz

LINQ .NET programozási modell, amely kifejezi az objektum adatfolyamok lekérdezések, számítások. A cosmos DB biztosítja, hogy a LINQ to felület ügyféloldali kódtár a JSON és a .NET-objektumok és a egy LINQ-lekérdezések egy részét a Cosmos DB-lekérdezésekre leképezés közötti váltás megkönnyítése.

Az alábbi ábrán a Cosmos DB használatával LINQ-lekérdezéseket támogató architektúráját mutatja be. A Cosmos DB-ügyfelet használ, létrehozhat egy `IQueryable` objektum, amely közvetlenül a Cosmos DB-lekérdezésszolgáltató lekérdezi és fordítja le a Cosmos DB-lekérdezés a LINQ-lekérdezésekre. Ezután adja át a lekérdezést a Cosmos DB-kiszolgálóra, amely lekéri a eredményhalmazt JSON formátumban. A JSON-deszerializáló egy .NET-objektumokat az ügyféloldalon adatfolyam alakítja át az eredményeket.

![A LINQ-lekérdezések használata az SQL API-t - SQL-szintaxis, JSON lekérdezési nyelvet, adatbázis-tervezésben és SQL-lekérdezéseket támogató architektúra][1]

### <a name="net-and-json-mapping"></a>.NET és a JSON-leképezés

.NET-objektumokat és JSON-elemek közötti természetes. Minden tag adatmező képez le egy JSON-objektumot, ahol a mező neve leképezhető a *kulcs* az objektumot, és az érték rekurzív módon részét képezi le a *érték* része az objektumot. A következő kód a maps a `Family` egy JSON-elem osztályból, és ekkor létrehoz egy `Family` objektum:

```csharp
    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };
```

Az előző példában az alábbi JSON-elemet hoz létre:

```json
    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female",
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller",
              "givenName": "Lisa",
              "gender": "female",
              "grade": 8
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };
```

### <a name="linq-to-sql-translation"></a>"LINQ to SQL fordítási"

A Cosmos DB-lekérdezésszolgáltató hajt végre, egy Cosmos DB SQL-lekérdezést az ajánlott beavatkozást leképezése a LINQ-lekérdezésekre. A következő leírást egy LINQ alapszintű ismeretét feltételezi.

A lekérdezés szolgáltató típusa rendszer támogatja a csak a JSON egyszerű típusok: numerikus, logikai értéket, a karakterlánc, és NULL értékű. 

A lekérdezés-szolgáltató támogatja a következő skaláris kifejezések:

- Állandó értékek, beleértve az állandó értékek, a primitív adattípusokat lekérdezés kiértékelése során.
  
- Egy objektum vagy egy tömbelem tulajdonságára index kifejezés tulajdonságtömb /. Példa:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Aritmetikai kifejezésekben, beleértve a numerikus és logikai értékek a közös aritmetikai kifejezésekben. A teljes listát lásd: a [Azure Cosmos DB SQL-specifikáció](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Karakterlánc összehasonlítási kifejezésekben, többek között egy karakterláncértéket néhány állandó karakterlánc értéket összehasonlítása.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Objektum vagy tömb létrehozása kifejezés, amely összetett érték vagy névtelen típusú objektumot, vagy ilyen objektumok egy tömbjét adja vissza. Ezek az értékek ágyazhatja be.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

### <a id="SupportedLinqOperators"></a>Támogatott LINQ-operátorokat

A LINQ szolgáltató az SQL .NET SDK-val tartalmazza az alábbi műveleteket támogatja:

- **Válassza ki**: Leképezések SQL SELECT, beleértve a objektumkonstrukciók fordítani.
- **Ahol**: Szűrők, amelyek, ahol az SQL, és támogatja a fordítási közötti `&&`, `||`, és `!` , az SQL-operátorok
- **SelectMany**: Lehetővé teszi a tömbök, az SQL JOIN záradék visszagörgetésének. Láncolt, vagy a tömb elemeinek szűréséhez kifejezések beágyazása használatával.
- **OrderBy** és **OrderByDescending**: Az ASC vagy DESC rendezési fordítani.
- **Száma**, **Sum**, **Min**, **maximális**, és **átlagos** összesítő és a aszinkron megfelelőjükre operátorok**CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, és **AverageAsync**.
- **Compareto metódus végrehajtása**: A rendszer lefordítja arra tartomány összehasonlítást. Gyakran használt karakterláncok, mivel azok még nem összehasonlítható a .NET-ben.
- **Igénybe**: A rendszer lefordítja arra az SQL felső korlátozza a lekérdezés eredményeit.
- **Matematikai függvények**: Támogatja a .NET használatával fordítási `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`, és `Truncate` a megfelelő SQL a beépített funkciók.
- **Karakterlánc-függvények**: Támogatja a .NET használatával fordítási `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`, és `TrimStart` a megfelelő SQL a beépített funkciók.
- **A tömb funkciók**: Támogatja a .NET használatával fordítási `Concat`, `Contains`, és `Count` a megfelelő SQL a beépített funkciók.
- **Térinformatikai bővítmény funkciók**: Támogatja a helyettes módszerek fordítási `Distance`, `IsValid`, `IsValidDetailed`, és `Within` a megfelelő SQL a beépített funkciók.
- **Felhasználó által definiált függvény bővítmény függvény**: Támogatja a helyettes metódus fordítási `UserDefinedFunctionProvider.Invoke` a megfelelő felhasználó által definiált függvénynek.
- **Vegyes**: Támogatja a fordítási `Coalesce` és feltételes operátorokat. Lefordítja `Contains` karakterláncot tartalmaz, ARRAY_CONTAINS vagy SQL a környezettől függően.

### <a name="sql-query-operators"></a>SQL-lekérdezés operátorok

Az alábbi példák bemutatják, hogyan a standard szintű LINQ lekérdezés operátorok némelyike lefordítja a Cosmos DB-lekérdezésekre.

#### <a name="select-operator"></a>Művelet kiválasztása

A szintaxis `input.Select(x => f(x))`, ahol `f` egy skaláris kifejezés.

**Válassza ki az operátort, 1. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Válassza ki az operátort, 2. példa:** 

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Válassza ki az operátort, 3. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

#### <a name="selectmany-operator"></a>SelectMany operátor

A szintaxis `input.SelectMany(x => f(x))`, ahol `f` egy skaláris kifejezés, amely egy tároló-típust ad vissza.

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

#### <a name="where-operator"></a>Ahol operátor

A szintaxis `input.Where(x => f(x))`, ahol `f` van egy skaláris kifejezés, amely egy logikai értéket ad vissza.

**Ahol kezelő, 1. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Ahol kezelő, a 2. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

### <a name="composite-sql-queries"></a>Összetett SQL-lekérdezések

Létrehozhatja azt az előző operátorok nagyobb teljesítményű lekérdezések kialakításához. Mivel a Cosmos DB támogatja a beágyazott tárolókat, fűzze össze, vagy beágyazni az összeállításban.

#### <a name="concatenation"></a>Összefűzés

A szintaxis `input(.|.SelectMany())(.Select()|.Where())*`. Egy összefűzött lekérdezés kezdhet egy nem kötelező `SelectMany` lekérdezést, majd több `Select` vagy `Where` operátorok.

**Összefűzés, 1. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Összefűzés, 2. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Összefűzés, 3. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Összefűzés, 4. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

#### <a name="nesting"></a>A beágyazási

A szintaxis `input.SelectMany(x=>x.Q())` ahol `Q` van egy `Select`, `SelectMany`, vagy `Where` operátor.

Egy beágyazott lekérdezésen egyes elemei a külső tároló a belső lekérdezés vonatkozik. A rendszer egyik fontos szolgáltatása, hogy a belső lekérdezés a külső tárolóban, például a önillesztés elem mezőire hivatkozhat.

**Nesting, example 1:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Nesting, example 2:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Nesting, example 3:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a id="References"></a>Hivatkozások

- [Az Azure Cosmos DB SQL-specifikáció](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [JavaScript-specifikáció](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe, Goetz. [Értékelés technikák nagy méretű adatbázisok lekérdezése](https://dl.acm.org/citation.cfm?id=152611). *Felmérések számítástechnika ACM* 25-én nem. 2 (1993).
- Graefe, G. "A lekérdezés-optimalizálási kaszkádokban keretrendszer." *IEEE-adatok Eng. BULL.* 18., nem. 3 (1995).
- Tan lu, Ooi. "A lekérdezés feldolgozása a párhuzamos relációs adatbázis-rendszerek." *IEEE számítógép társadalom nyomja meg* (1994).
- Olston, Christopher, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, and Andrew Tomkins. "A Pig Latin: Nem így külső nyelvét adatfeldolgozási." *SIGMOD* (2008).

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB bemutatása][introduction]
- [Azure Cosmos DB .NET samples](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Az Azure Cosmos DB konzisztenciaszintjeinek][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
