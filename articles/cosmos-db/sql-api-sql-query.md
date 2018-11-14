---
title: Az Azure Cosmos DB SQL-lekérdezések |} A Microsoft Docs
description: További információk az SQL-szintaxis, adatbázis-tervezésben és SQL-lekérdezések az Azure Cosmos DB. Az SQL Azure Cosmos DB-ben egy JSON-lekérdezési nyelvet is használja.
keywords: SQL-szintaxis, sql-lekérdezés, sql-lekérdezéseket, json lekérdezési nyelvet, adatbázis-tervezésben és sql-lekérdezések aggregátumfüggvények
services: cosmos-db
author: LalithaMV
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: laviswa
ms.openlocfilehash: 8799371c911f3e120cb8654bf26fa933b17e4b3c
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623408"
---
# <a name="sql-queries-in-azure-cosmos-db"></a>Az Azure Cosmos DB SQL-lekérdezések

Az Azure Cosmos DB támogatja az SQL (Structured Query Language) használatával, egy JSON lekérdezési nyelve az SQL API Cosmos-adatbázis lekérdezéséhez. A lekérdezési nyelv, az SQL API Cosmos-adatbázis tervezésekor az alábbi két célok lettek figyelembe véve:

* Helyett egy új lekérdezési nyelvre inventing, Cosmos DB támogatja az SQL-lekérdezés ismerős és a népszerű nyelvek valamelyikével. Cosmos DB SQL formális programozási modellt biztosít részletes lekérdezéseket a JSON-adatok.  

* A cosmos DB JavaScript programozási modellt használ a lekérdezési nyelv alapjaként. Az SQL API a JavaScript, kifejezés kiértékelése, valamint függvény meghívási feltörték. Így lehetővé teszi a természetes programozási modell leképezések relációs, hierarchikus navigációs JSON-dokumentumok, önkiszolgáló összekapcsolásokat, térinformatikai lekérdezéseket és hívja meg a felhasználó által definiált függvények (UDF), teljes mértékben javascriptben írt, többek között.

Ez a cikk bemutatja a Cosmos DB SQL-lekérdezések egyszerű JSON-dokumentumok használatával néhány példán keresztül. Cosmos DB SQL nyelvi szintaxissal kapcsolatos további információkért lásd: [SQL-szintaxis referenciája](sql-api-sql-query-reference.md).

## <a id="GettingStarted"></a>Ismerkedés az SQL-parancsok
Most két egyszerű JSON-dokumentumok létrehozása családok leíró, és az adatok lekérdezéséhez. Két dokumentumok beilleszt egy Cosmos-tárolóba, megkezdhetjük az adatok lekérdezéséhez. Az alábbiakban meghatározzuk az Andersen és Wakefield családhoz egyszerű JSON-dokumentumokat. Minden egyes dokumentum karakterláncok, számok, logikai értékek, tömbök, és beágyazott tulajdonságait tartalmazza.

**Dokumentum 1**  

```JSON
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
  "address": { "state": "WA", "county": "King", "city": "seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Íme egy különbség – a második dokumentum `givenName` és `familyName` helyett használhatók `firstName` és `lastName`.

**Document2**  

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
        "gender": "female", "grade": 1,
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

Most pedig próbáljuk ki néhány lekérdezéseket az ezeket az adatokat a Cosmos DB SQL lekérdező nyelve néhány fontos szempontjai ismertetése.

**Lekérdezés1**: A következő lekérdezés olyan dokumentumokat ad vissza, az id mezője megegyezik-e `AndersenFamily`. Mivel ez egy `SELECT *`, a lekérdezés kimenete a teljes JSON-dokumentumot. A lekérdezési szintaxissal kapcsolatos további tudnivalókért lásd: [SELECT utasítás](sql-api-sql-query-reference.md#select-query):

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

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
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

**Lekérdezés2** : mostantól fontolja meg egy esetet, ahol a JSON-kimenet formáznia kell. A lekérdezés visszaad egy JSON-objektumot két kijelölt mezővel, nevét és az városa, dokumentumok, amelyben város és állam azonosak. Ebben az esetben a "NY, NY" egyezés.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state
```

**Results**

```json
    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]
```

**Query3**: Ez a lekérdezés a család, amelyek azonosítója egyezik a gyermekek az összes megadott nevét adja vissza `WakefieldFamily` a tartózkodási város alapján rendezve.

```sql
    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

**Results**

```json
    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]
```

Néhány fontos szempontja a Cosmos DB SQL lekérdező nyelve a példákon keresztül láthatta, amennyiben:  

* Mivel az SQL API a JSON-értékeit működik, formázott sorok és oszlopok helyett entitások fa foglalkozik. Ezért a nyelv segítségével, tekintse meg a fa bármilyen tetszőleges mélységben csomópontok például `Node1.Node2.Node3…..Nodem`, hasonlóan ahhoz, hogy a két rész referenciája a hivatkozó relációs SQL `<table>.<column>`.

* Az SQL API a séma nélküli adatokkal dolgozik. Ezért a típus rendszer kell dinamikusan van kötve. Egyazon kifejezésre partíciónként akár különböző típusok különböző dokumentumokon kiértékelésekor. A lekérdezés eredménye egy érvényes JSON-értéket, de nem garantált, hogy egy adott séma lehet.

* A cosmos DB támogatja a szigorú JSON-dokumentumok csak. Ez azt jelenti, hogy a rendszer típusa és -kifejezések csak JSON típusú kezelésére korlátozva. Tekintse meg a [JSON-specifikáció](http://www.json.org/) további részletekért.  

* Egy Cosmos-tárolót egy sémamentes JSON-dokumentumokat tároló. A kapcsolatokat, az adatok entitások belül és azok között a tárolóban lévő dokumentumok implicit módon rögzítve lesznek a tartalmazási, és nem a primary key és az idegen kulcs kapcsolatokat. Ez az a cikkben később tárgyalt intra-dokumentum illesztéseket naprakészségét megjegyeznünk fontos elemét alkotják.

## <a id="SelectClause"></a>SELECT záradék

Minden egyes lekérdezés SELECT záradékában és választható FROM áll és a WHERE záradék ANSI SQL előírások szerint. Általában az egyes lekérdezésekhez a forrás a FROM záradékban számbavétele megtörtént. Ezután a WHERE záradékban a szűrő alkalmazása a forrás a JSON-dokumentumok részhalmazának beolvasásához. Végül a SELECT záradékban szolgál a kért JSON-értékeit a kiválasztási listán. A szintaxissal kapcsolatos további tudnivalókért lásd: [SELECT szintaxissal](sql-api-sql-query-reference.md#bk_select_query).

Az alábbi példa bemutatja egy tipikus SELECT-lekérdezésben. 

**Lekérdezés**

```sql
    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]
```

### <a name="nested-properties"></a>Beágyazott tulajdonságok
A következő példában két beágyazott tulajdonságok hogy kivetítés `f.address.state` és `f.address.city`.

**Lekérdezés**

```sql
    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "state": "WA", 
      "city": "seattle"
    }]
```

Leképezés JSON kifejezéseket is támogatja, az alábbi példában látható módon:

**Lekérdezés**

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]
```

Nézzük, szerepe `$1` itt. A `SELECT` záradék létre kell hoznia egy JSON-objektumot, és nem kulcs van megadva, mivel használjuk implicit argumentum változók neve kezdődik `$1`. Például az a lekérdezés visszaad két implicit argumentum változók, címkéjű `$1` és `$2`.

**Lekérdezés**

```sql
    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>FROM záradékban

Az a < from_specification > záradék nem kötelező, kivéve ha a forrás van szűrve, vagy később a lekérdezést az előre jelzett. A szintaxissal kapcsolatos további tudnivalókért lásd: [a szintaxis](sql-api-sql-query-reference.md#bk_from_clause). A lekérdezés, például `SELECT * FROM Families` azt jelzi, hogy a teljes családok tárolót a forrást, amelyen számbavétele. Egy legfelső szintű különleges azonosító segítségével a tároló neve helyett a tárolót képviseli.

Az alábbi lista tartalmazza a szabályokat, amelyek lekérdezésenként érvényben vannak:

* A tároló lehet aliassal, például `SELECT f.id FROM Families AS f` vagy egyszerűen csak `SELECT f.id FROM Families f`. Itt `f` aliasneve `Families`. `AS` azonosító érték egy nem kötelező kulcsszó használatával alias.  

* Egyszer aliassal, az eredeti adatforrás nem köthető. Ha például `SELECT Families.id FROM Families f` szintaktikailag érvénytelen, mert "Családok" azonosítója nem lehet feloldani folyamatban van a fióknévnek után.  

* Az összes tulajdonság, amely lehet hivatkozni kell a teljesen minősített kell lennie. Szigorú sémát megfelelést hiányában ez kényszerítve van a nem egyértelmű kötések elkerülése érdekében. Ezért `SELECT id FROM Families f` szintaktikailag óta a tulajdonság nem `id` nincs kötve.

### <a name="get-subdocuments-using-from-clause"></a>FROM záradék használatával aldokumentumok beolvasása

A forrás is részhalmaza is lehet kiválasztani. Például részfából enumerálni, a forrás adható meg az alábbi példában látható módon:

**Lekérdezés**

```sql
    SELECT * 
    FROM Families.children
```

**Results**  

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

A fenti példában egy tömb használja forrásként, amíg egy objektumot is használható forrásként, az alábbi példában látható módon. Bármely érvényes JSON-értéket (nem a nem meghatározott), amely megtalálható a forrás számít, hogy a lekérdezés eredménye. Ha egyes termékcsaládok nem rendelkeznek egy `address.state` érték, a lekérdezési eredmény ki vannak zárva.

**Lekérdezés**

```sql
    SELECT * 
    FROM Families.address.state
```

**Results**

```json
    [
      "WA", 
      "NY"
    ]
```

## <a id="WhereClause"></a>WHERE záradék
A WHERE záradékban (**`WHERE <filter_condition>`**) megadása nem kötelező. Azt adja meg a feltételeket, amelyek a forrás által biztosított a JSON-dokumentumok meg kell felelniük ahhoz, hogy az eredmény része. Bármely JSON-dokumentum "igaz" eredmény figyelembe kell venni a megadott feltételeknek kell kiértékelni. A WHERE záradékban az index réteg használják annak érdekében, hogy a legkisebb részhalmazát forrás azt jelzi, hogy az eredmény része lehet meghatározni. A szintaxissal kapcsolatos további tudnivalókért lásd: [WHERE szintaxis](sql-api-sql-query-reference.md#bk_where_clause).

A következő lekérdezést a name tulajdonság, amelynek az értéke tartalmazó dokumentumok kérelmek `AndersenFamily`. Bármely más dokumentum, amely nem rendelkezik a name tulajdonság, vagy ha az érték nem egyezik `AndersenFamily` ki van zárva. 

**Lekérdezés**

```sql
    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]
```

Az előző példából kiderült, egy egyszerű egyenlőség lekérdezést. Az SQL API támogatja a különböző skaláris kifejezések is. A leggyakrabban használt olyan bináris és egyoperandusú kifejezés. A forrás JSON-objektumból tulajdonság hivatkozásokat akkor is érvényes kifejezések. 

A következő bináris operátorok jelenleg támogatott, és használható lekérdezések a következő példákban szemléltetett módon:  

|**Művelettípus**  |**Értékek**  |
|---------|---------|
|Aritmetikai    |   +,-,*,/,%   |
|Bitenkénti  |   , &, ^, &lt; &lt;, &gt; &gt;, &gt; &gt; &gt; (nulla ki jobbra tolást)      |
|Logikai   |   ÉS, VAGY SEM      |
|Összehasonlítás   |    =, !=, &lt;, &gt;, &lt;=, &gt;=, <>     |
|Karakterlánc  |  \|\| (fűzze össze)       |

Vessünk egy pillantást a bináris operátorok használatával néhány lekérdezést.

```sql
    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5
```

Az egyoperandusú operátorokat +,-, ~, és nem is támogatottak, és használható lekérdezések belül a következő példákban szemléltetett módon:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Bináris- és egyoperandusú operátorok mellett tulajdonság hivatkozásokat is engedélyezettek. Ha például `SELECT * FROM Families f WHERE f.isRegistered` adja vissza a JSON-dokumentum, amely tartalmazza a tulajdonság `isRegistered` ahol a tulajdonság értéke megegyezik a JSON `true` érték. Egyéb értékek (False (hamis), null, nem definiált, `<number>`, `<string>`, `<object>`, `<array>`használatához és így tovább) vezet, a dokumentum az eredményből kizárásával. 

### <a name="equality-and-comparison-operators"></a>Egyenlőség és összehasonlító operátorok
Az alábbi táblázat egyenlőségi összehasonlítás eredménye minden két JSON-típusok között az SQL API-ban.

<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>Nincs definiálva</strong>
         </td>
         <td valign="top">
            <strong>NULL</strong>
         </td>
         <td valign="top">
            <strong>Logikai érték</strong>
         </td>
         <td valign="top">
            <strong>Szám</strong>
         </td>
         <td valign="top">
            <strong>Karakterlánc</strong>
         </td>
         <td valign="top">
            <strong>Object</strong>
         </td>
         <td valign="top">
            <strong>Pole</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Nincs definiálva<strong>
         </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>NULL<strong>
         </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Logikai érték<strong>
         </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Szám<strong>
         </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Karakterlánc<strong>
         </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Object<strong>
         </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Meghatározatlan </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Pole<strong>
         </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
Meghatározatlan </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
      </tr>
   </tbody>
</table>

Más összehasonlító operátorok, mint például a >, > =,! =, <, és < =, a következő szabályok érvényesek:   

* Összehasonlítási típus között nincs megadva eredményez.  
* Két-két kezelőfelületének összehasonlítása az eredmények nincs megadva a Tárolótömböket.   

Ha a szűrő skaláris kifejezés eredménye nincs definiálva, a megfelelő dokumentum nem szerepel az eredményt, mivel nincs megadva logikailag igényhez nem "true".

## <a name="between-keyword"></a>Kulcsszó között
A BETWEEN kulcsszó használatával címtartományok érték például az ANSI SQL-lekérdezéseket express. KÖZÖTT is használhatók karakterlánc vagy szám.

Például ez a lekérdezés visszaadja az összes családi dokumentumot, amelyben az első alárendelt szintű (mindkét határokat is beleértve) 1-5 között van. 

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Ellentétben az ANSI-SQL-ben is használhatja a BETWEEN záradék a FROM záradékban, például az alábbi példában.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

A fő különbség az SQL API és az ANSI SQL BETWEEN használata között, hogy a tartomány-lekérdezéseket az tulajdonságok vegyes típusú fejezhető ki – például előfordulhat, hogy rendelkezik "osztály" (5) szám lehet a bizonyos dokumentumok és karakterláncokat másokkal ("grade4"). Ezekben az esetekben például a JavaScript, a "nem definiált" két különböző típusú eredményt, és a dokumentum összehasonlítását kimarad.

> [!NOTE]
> Lekérdezés végrehajtása gyorsabb ne felejtse el ellen bármely numerikus tulajdonságok/elérési utakat a BETWEEN záradék a szűrt indexet Tartománytípus használó indexelési szabályzat létrehozása. 

### <a name="logical-and-or-and-not-operators"></a>Logikai (AND, OR és NOT) operátorok
Logikai operátorok a logikai értékek művelethez. Ezen operátorok logikai hiteles táblázatokban az alábbi táblázatban láthatók.

**VAGY a kezelő**

| VAGY | True (Igaz) | False (Hamis) | Meghatározatlan |
| --- | --- | --- | --- |
| True (Igaz) |True (Igaz) |True (Igaz) |True (Igaz) |
| False (Hamis) |True (Igaz) |False (Hamis) |Meghatározatlan |
| Meghatározatlan |True (Igaz) |Meghatározatlan |Meghatározatlan |

**ÉS operátor**

| ÉS | True (Igaz) | False (Hamis) | Meghatározatlan |
| --- | --- | --- | --- |
| True (Igaz) |True (Igaz) |False (Hamis) |Meghatározatlan |
| False (Hamis) |False (Hamis) |False (Hamis) |False (Hamis) |
| Meghatározatlan |Meghatározatlan |False (Hamis) |Meghatározatlan |

**NOT operátor**

| NEM |  |
| --- | --- |
| True (Igaz) |False (Hamis) |
| False (Hamis) |True (Igaz) |
| Meghatározatlan |Meghatározatlan |

## <a name="in-keyword"></a>A kulcsszó

Az IN kulcsszó segítségével ellenőrizze, hogy egy megadott értéke megegyezik-e a lista bármely értéke. Például ez a lekérdezés visszaadja az összes családi dokumentumot, az azonosító az egyik "WakefieldFamily" vagy "AndersenFamily". 

```sql
    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Ez a példa összes dokumentumot visszaadja, ahol állapota a megadott értékeket.

```sql
    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="ternary--and-coalesce--operators"></a>Ternáris (?) és a kezelők Coalesce (?)

A Ternáris és Coalesce operátorok hasonló olyan népszerű programozási nyelvet, például C# és a JavaScript, a feltételes kifejezések felépítéséhez létre használható. A Ternáris (?) operátor nagyon hasznos lehet, amikor hozhat létre, amely menet közben új JSON-tulajdonságokkal. Például most már írhat a osztály szintek besorolása például a kezdő vagy haladó/speciális alább látható módon emberi olvasható formába lekérdezések.

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c
```

Az operátor például az alábbi lekérdezést a hívásokat is beágyazhatja.

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c
```

Más lekérdezési operátorokkal, ha a feltételes kifejezésben hivatkozott tulajdonságai hiányoznak a dokumentumtípus, vagy ha az összehasonlított típusok különböznek, majd ezeket a dokumentumokat ki vannak zárva a lekérdezési eredményekben.

A Coalesce (?) operátor segítségével hatékonyan ellenőrizze, a tulajdonság (más néven) van megadva) dokumentumban. Ez akkor hasznos, ha a szolgáltatásban tárolt részben strukturált lekérdezését vagy vegyes típusú adatokat. Például a lekérdezés visszaadja a "lastName" Ha jelen van, vagy a "Vezetéknév" Ha nincs jelen.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="EscapingReservedKeywords"></a>Határolójeles tulajdonság hozzáférő
Is elérheti a határolójeles tulajdonság operátorral tulajdonságok `[]`. Ha például `SELECT c.grade` és `SELECT c["grade"]` egyenértékűek. Ezt a szintaxist akkor hasznos, amikor szüksége van egy szóközt, speciális karaktereket tartalmaz vagy történik a neve megegyezik az SQL kulcsszó vagy fenntartott szó tulajdonság karaktert.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="aliasing"></a>Aliasképző

Most tekintsük kiterjesztése a fenti példában a explicit aliasképző értékek. Ez a kulcsszó aliasképző használt. Nem kötelező, miközben kivetítést, mint a második érték látható módon `NameInfo`. 

Abban az esetben, ha egy lekérdezést a két tulajdonság azonos nevű rendelkezik, alias átnevezése egyikét vagy mindkettőt a tulajdonságokat, hogy azok az előre jelzett eredmény vannak használatát kell használni.

**Lekérdezés**
```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="scalar-expressions"></a>Skaláris kifejezések
Mellett tulajdonság hivatkozik a SELECT záradékban skaláris kifejezések állandók, számtani kifejezéseket, logikai kifejezéseket és egyéb hasonló is támogatja. Ha például itt látható egy egyszerű "Hello World" lekérdezést.

**Lekérdezés**

```sql
    SELECT "Hello World"
```

**Results**

```json
    [{
      "$1": "Hello World"
    }]
```

Íme egy összetettebb példának, amely egy skaláris kifejezés.

**Lekérdezés**

```sql
    SELECT ((2 + 11 % 7)-2)/3    
```

**Results**

```json
    [{
      "$1": 1.33333
    }]
```

A következő példában a skaláris kifejezés eredménye egy logikai érték.

**Lekérdezés**

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f    
```

**Results**

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
Az SQL API egy másik fontos szolgáltatása a tömb vagy objektum-létrehozás. Az előző példában vegye figyelembe, hogy létrehozott egy új JSON-objektum. Hasonlóképpen egy szerkezetét is tömbök a következő példákban szemléltetett módon:

**Lekérdezés**

```sql
    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f    
```

**Results**  

```json
    [
      {
        "CityState": [
          "seattle", 
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

## <a id="ValueKeyword"></a>ÉRTÉK kulcsszó
A **érték** kulcsszó lehetővé teszi a JSON-értéket adja vissza. Ha például az alábbi lekérdezés adja vissza a skaláris `"Hello World"` helyett `{$1: "Hello World"}`.

**Lekérdezés**

```sql
    SELECT VALUE "Hello World"
```

**Results**

```json
    [
      "Hello World"
    ]
```

A következő lekérdezés nélkül JSON értékét adja vissza a `"address"` címkét az eredmények között.

**Lekérdezés**

```sql
    SELECT VALUE f.address
    FROM Families f    
```

**Results**  

```json
    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]
```

Az alábbi példa kibővíti ezt a JSON egyszerű értékeket (a levélszintű a JSON-fa) szemléltetik. 

**Lekérdezés**

```sql
    SELECT VALUE f.address.state
    FROM Families f    
```

**Results**

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="-operator"></a>* Operátor
A speciális operátor (*) támogatott, a dokumentum Project-van. Használatakor a csak tervezett mezőt kell lennie. Miközben a lekérdezésben `SELECT * FROM Families f` érvényes, `SELECT VALUE * FROM Families f ` és `SELECT *, f.id FROM Families f ` nem érvényesek.

**Lekérdezés**

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

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
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="TopKeyword"></a>TOP operátor
A felső kulcsszó egy lekérdezés által értékek számának korlátozására használható. FELSŐ az ORDER BY záradékkal együtt kell használni, amikor az eredményhalmaz korlátozódik rendezett értékek; az első N száma Ellenkező esetben azt számát adja vissza az első N eredmények nem meghatározott sorrendben legyenek. Ajánlott eljárásként olyan SELECT utasításban, mindig egy ORDER BY záradék együtt használni a TOP záradékot. Ez az egyetlen módja lehetővé teszi a kiszámítható jelzi a felső által érintett sorok. 

**Lekérdezés**

```sql
    SELECT TOP 1 * 
    FROM Families f 
```

**Results**

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
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

FELSŐ használható egy állandó értékkel (a fent látható) vagy egy változó értéke a paraméteres lekérdezések használatával. További részletekért tekintse meg az alábbi paraméteres lekérdezések.

## <a id="Aggregates"></a>Aggregátumfüggvények
Az összesítéseket is elvégezheti a `SELECT` záradékban. Aggregátumfüggvények számítás elvégzése különböző értékeket, és egyetlen értéket ad vissza. A következő lekérdezés például családi, a tárolóban lévő dokumentumok darabszámát adja vissza.

**Lekérdezés**

```sql
    SELECT COUNT(1) 
    FROM Families f 
```

**Results**

```json
    [{
        "$1": 2
    }]
```

Az összesítési skaláris értékét adja vissza a a `VALUE` kulcsszót. Például a következő lekérdezést egy egyetlen számot ad vissza értékek száma:

**Lekérdezés**

```sql
    SELECT VALUE COUNT(1) 
    FROM Families f 
```

**Results**

```json
    [ 2 ]
```

A szűrők együtt is elvégezheti összesítések. Például a következő lekérdezést a Washington állam címével dokumentumok darabszámát adja vissza.

**Lekérdezés**

```sql
    SELECT VALUE COUNT(1) 
    FROM Families f
    WHERE f.address.state = "WA" 
```

**Results**

```json
    [ 1 ]
```

Az alábbi táblázat a támogatott összesítő függvények listáját az SQL API-ban. `SUM` és `AVG` kapcsolaton keresztül a numerikus értékek mennek végbe, mivel a `COUNT`, `MIN`, és `MAX` számok, karakterláncok, logikai és nullértékek keresztül is elvégezhető. 

| Használat | Leírás |
|-------|-------------|
| MENNYISÉG | A kifejezésben található elemek számát adja vissza. |
| ÖSSZEG   | A kifejezésben található értékek összegét adja vissza. |
| MIN.   | A kifejezés minimumértékét adja vissza. |
| MAX.   | A kifejezés maximumértékét adja vissza. |
| ÁTL.   | A kifejezésben található értékek átlagát adja vissza. |

Összesíti az eredményeket egy tömb ismétlés keresztül is elvégezhető. További információkért lásd: [lekérdezések tömb iterációját](#Iteration).

> [!NOTE]
> Az Azure portal adatkezelő használatakor vegye figyelembe, hogy összesítés lekérdezések a részlegesen összesített eredményeket adhat vissza a lekérdezés lapon. Az SDK-k minden oldalain egyetlen összesített értéket állít elő. 
> 
> Kód használatával összesítési lekérdezések végrehajtásához a .NET SDK-val 1.12.0, .NET Core SDK 1.1.0-ás vagy a Java SDK 1.9.5 szüksége vagy újabb.    
>

## <a id="OrderByClause"></a>ORDER BY záradék
Hasonlóan az ANSI SQL LikJust, hozzáadhatja a választható Order By záradék lekérdezése közben. A záradékot tartalmazhat választható ASC/DESC argumentumban adja meg, amelyben az eredményeket kell kérhető sorrendjét.

Például itt látható egy lekérdezést, amely lekéri a tartózkodási város nevét sorrendje szereplő eszközcsaládokban megtalálható.

**Lekérdezés**

```sql
    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city
```

**Results**

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

És Íme sorrendjét, létrehozás dátuma, amely tárolódik, amely az alapidőpont szereplő eszközcsaládokban megtalálható, amely idő, azaz, 1970. január 1. a óta eltelt idő másodpercben.

**Lekérdezés**

```sql
    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC
```

**Results**

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

## <a id="Advanced"></a>Speciális adatbázis-tervezésben és SQL-lekérdezések

### <a id="Iteration"></a>Iteráció
Egy új szerkezet használatával lett hozzáadva a **IN** kulcsszó keresztül JSON-tömbök léptetés támogatást nyújt az SQL API-ban. A FROM forrás iteráció támogatást nyújt. Kezdjük az alábbi példában:

**Lekérdezés**

```sql
    SELECT * 
    FROM Families.children
```

**Results**  

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

Most nézzük meg egy másik lekérdezést, amely végrehajtja az iteráció gyermekek a tárolóban. Vegye figyelembe a különbség a kimeneti tömbben. Ebben a példában bontja `children` és simítja egybe az eredményeket egy egyetlen tömbbe.  

**Lekérdezés**

```sql
    SELECT * 
    FROM c IN Families.children
```

**Results**  

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

Ez további segítségével szűrhet a tömb minden egyes bejegyzés a következő példában látható módon:

**Lekérdezés**

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

**Results**  

```json
    [{
      "givenName": "Lisa"
    }]
```

Összesítés tömb iteráció eredményét feletti is elvégezheti. A következő lekérdezés például megszámlálja a gyermekek többek között az összes olyan családot.

**Lekérdezés**

```sql
    SELECT COUNT(child) 
    FROM child IN Families.children
```

**Results**  

```json
    [
      { 
        "$1": 3
      }
    ]
```

### <a id="Joins"></a>Illesztés
Több tábla csatlakozni kell egy relációs adatbázisban, fontos. A logikai corollary normalizált sémák tervezéséhez. Ezzel szemben az SQL API a sémamentes dokumentumok denormalizált adatmodell foglalkozik. Ez a logikai megfelelője a "önillesztést".

A nyelv által támogatott szintaxisa < from_source1 > Csatlakozás < from_source2 > Csatlakozás... JOIN < from_sourceN >. Átfogó, ez visszaadja egy **N**- rekordokat tartalmazó (a rekord **N** értékek). Minden egyes rekord összes tároló-alias léptetés keresztül az adott csoportok által előállított értékkel rendelkezik. Más szóval ez az egy teljes a részt vesz a join készlet keresztszorzatát.

Az alábbi példák bemutatják, hogyan működik a JOIN záradékban. A következő példában minden egyes dokumentum forrásból keresztszorzatát óta eredménye nem üres és üres üres.

**Lekérdezés**

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

**Results**  

```json
    [{
    }]
```

A következő példában a csatlakozás a dokumentum legfelső szintű között van, és a `children` subroot. Egy több termék között két JSON-objektumot. Arra, hogy gyermek tömb nem szerepel hatékony az ILLESZTÉS mivel tudjuk, hogy a gyermek tömb egyetlen legfelső szintű foglalkoznak. Ezért az eredmény tartalmazza csak két eredményt, mivel a tömböt az egyes dokumentumok keresztszorzatát poskytne pontosan csak egyetlen dokumentumot.

**Lekérdezés**

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

**Results**

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

**Lekérdezés**

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children 
```

**Results**

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

Az első lépésben vegye figyelembe, hogy a `from_source` , a **CSATLAKOZZON** záradékot egy tokenhez. Így a folyamat ebben az esetben a következő módon:  

* Bontsa ki az egyes gyermekelemet **c** a tömbben.
* A alkalmazni a legfelső szintű a dokumentum több termék **f** az egyes gyermekelemet **c** , amely az első lépésben lett egybesimított.
* Végül projektre a gyökérobjektum **f** önálló tulajdonság neve. 

Az első dokumentum (`AndersenFamily`) csak egy gyermekelemet tartalmaz, ezért az eredményhalmaz csak ez a dokumentum megfelelő egyetlen objektumot tartalmaz. A második dokumentum (`WakefieldFamily`) két gyermekeket tartalmaz. Így a több termék minden gyermek, ezáltal két objektumot, egy megfelelő Ez a dokumentum minden gyermek eredményez egy külön objektumot hoz létre. A legfelső szintű mindkét alábbi dokumentumokban szereplő mezők ugyanazok, mint egy több termékben hiányol.

A valódi segédprogramot a JOIN űrlap rekordokat tartalmazó származik a termékek közötti egy alakzat, amely egyébként nehéz a projektet. Továbbá, az alábbi példában látható módon is szűrhetőek a rekordot, hogy lehetővé teszi, hogy a felhasználó kiválasztott a rekordok felsorolásának teljes feltételfüggvényt feltétel kombinációja.

**Lekérdezés**

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

**Results**

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

Ebben a példában az előző példában természetes bővítménye, és dupla illesztést hajt végre. Így a több termék tekinthet meg a következő ily módon kvázi kóddal:

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

`AndersenFamily` egy gyermek, aki rendelkezik egy kisállat rendelkezik. Így a több termék meghajtóra lenne szükség egy sor (1\*1\*1) a család. WakefieldFamily azonban van két gyermekemmel, de csak egy gyermek "Jesse" kisállatok rendelkezik. Jesse két kisállatok azonban rendelkezik. Ezért a több termék poskytne 1\*1\*sor, 2 = 2 család.

A következő példában van egy kiegészítő szűrőt `pet`. Ez nem tartalmazza az összes a rekordokat, ahol a kisállat-név nem szerepel "Árnyékmásolat". Figyelje meg, hogy tudjuk, tömbök, a rekord elemek szűrőt származó rekordokat tartalmazó és a project közül az elemek. 

**Lekérdezés**

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

**Results**

```json
    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]
```

## <a id="JavaScriptIntegration"></a>JavaScript-integráció
A cosmos DB-alapú JavaScript-alkalmazáslogika végre közvetlenül a tárolókban tárolt eljárások és eseményindítók tekintetében a programozási modellt biztosít. Ez lehetővé teszi, hogy mindkét:

* Lehetővé teszi nagy teljesítményű tranzakciós CRUD-műveletek és a egy tárolóban, a mély integráció, közvetlenül az adatbázismotor belül JavaScript futtatókörnyezet-hez tartozó-dokumentumokon végzett lekérdezések. 
* A természetes modellezési átvitelvezérlés, változó felmerülő, és a hozzárendelés és integrációs kivételkezelési primitívek adatbázis-tranzakciók. JavaScript-integráció a Cosmos DB támogatásával kapcsolatos további részletekért tekintse meg a JavaScript kiszolgálóoldali programozhatóság dokumentációját.

### <a id="UserDefinedFunctions"></a>Felhasználó által definiált függvények (UDF-EK)
Az ebben a cikkben már definiált típusok, valamint az SQL API-t biztosít támogatást a felhasználó definiált függvények (UDF). Skaláris UDF-EK támogatottak, ahol a fejlesztők nulla vagy több argumentumot adja át, és vissza egyetlen argumentumot eredményt adja vissza. Minden egyes argumentum ellenőrzése alatt álló jogi JSON-értékeit.  

Az SQL-szintaxis terjeszteni ezen felhasználó által megadott függvények használatával egyéni alkalmazáslogika támogatása. UDF-EK SQL API-val lehet regisztrálni, és ezután lehet hivatkozni az SQL-lekérdezés részeként. Sőt az UDF-EK exquisitely tervezték, hogy a lekérdezések által. Ezt a választást maradhassanak UDF-EK nem rendelkezik a context objektumot, a többi JavaScript típusok (tárolt eljárások és eseményindítók), melynél a hozzáférést. Lekérdezések csak olvashatóként hajtható végre, mert elsődleges vagy másodlagos replikákon is működhetnek. Ezért UDF-EK tervezték, hogy a másodlagos replikákon ellentétben más JavaScript típusú futtassa.

Alább egy példát egy UDF hogyan lehet regisztrálni, Cosmos-adatbázis, kifejezetten egy dokumentumot tároló alatt van.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  
```

Az előző példában létrehoz egy UDF, amelynek a neve `REGEX_MATCH`. Két JSON-karakterlánc értéket fogad el `input` és `pattern` és ellenőrzi, hogy az első megegyezik a minta a második megadott JavaScript string.match() függvény használatával.

Az UDF leképezés a lekérdezést most használatával. UDF-EK musí mít kvalifikovanou vlastnost, a kis-és nagybetűket előtaggal "udf." Amikor meghívhatók lekérdezéseket. 

> [!NOTE]
> 3/17/2015, mielőtt a Cosmos DB támogatott UDF hívások nélkül az "udf." előtag REGEX_MATCH() kiválasztása hasonlóan. Ez a hívó minta elavult.  
> 
> 

**Lekérdezés**

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

**Results**

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

Az UDF is használható belül egy szűrőt, ahogyan az alábbi példában is minősített az "udf." előtagja:

**Lekérdezés**

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

**Results**

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

UDF-EK lényegében érvényes skaláris kifejezések és leképezések és a szűrők is használható. 

Bontsa ki az UDF-EK hatékonyságát, nézzük meg egy másik példa a feltételes logikát:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'seattle':
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
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);
```

Alul látható egy példa, amely az UDF-ben él.

**Lekérdezés**

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f    
```

**Results**

```json
     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]
```

Ahogy az előző példák mutatni, UDF-EK JavaScript nyelven hatékonyságát integrálása összetett eljárásokról, feltételes logikát a JavaScript futásidejű funkcióinak köszönhetően hozzásegítheti segítségével. Ehhez egy részletes programozható felületet biztosít az SQL API.

Az SQL API-t biztosít az argumentumok az UDF-et a forrás minden egyes dokumentum szakaszában a jelenlegi (a WHERE záradékban vagy a SELECT záradékban) feldolgozása az UDF. Az eredmény részét képezik a teljes végrehajtási folyamat zökkenőmentesen. Ha a tulajdonságok szerinti által az UDF paraméterek nem érhetők el a JSON-értékben, a paraméter minősül nincs definiálva, és ezért UDF meghívását teljesen kihagyta. Hasonlóképpen az UDF eredménye nem definiált, ha azt nem szerepel az eredményben. 

Összefoglalva UDF-EK olyan összetett üzleti logikát ehhez a lekérdezés részeként nagyszerű eszközöket.

### <a name="operator-evaluation"></a>Kiértékelési operátor
A cosmos DB, folyamatban van egy JSON-adatbázis rendelkezik rajzol a JavaScript-operátorok és az értékelés szemantika parallels. Cosmos DB megpróbálja megőrizni a JSON-támogatás tekintetében JavaScript szemantikát, amíg a művelet kiértékelése mintától, bizonyos esetekben.

Az SQL API-ellentétben a hagyományos SQL-ben a típusú értékek gyakran nem ismert mindaddig, amíg a rendszer lekéri az értékek adatbázis. Annak érdekében, hogy hatékonyan hajtsa végre a lekérdezéseket, az operátorok a legtöbb szigorú adattípus-követelményekkel rendelkeznek. 

Az SQL API végre implicit konverzió, ellentétben a JavaScript. Például egy lekérdezést, például `SELECT * FROM Person p WHERE p.Age = 21` megegyezik egy kora tulajdonságot, amelynek az értéke 21 tartalmazó dokumentumokat. Bármely dokumentum amelynek kora tulajdonsága egyezést mutat az "21", vagy más karakterlánc valószínűleg végtelen változata létezik, például "021", "21,0", "0021", "00021", stb. nem található, karakterként lesz. Ez a szakembereket a JavaScript, hol vannak a karakterlánc-értékeket implicit módon konvertálni számok (például operátor szerinti szűrése, alapján: ==). Ez a választás elengedhetetlen a hatékony indexek az SQL API-t a megfelelő. 

## <a name="parameterized-sql-queries"></a>Paraméteres SQL-lekérdezések
A cosmos DB támogatja a lekérdezések és az ismerős kifejezett paraméterekkel \@ jelöléssel. Paraméteres SQL biztosít hatékony kezelése és escape-karaktersorozat felhasználói bevitelt, SQL-injektálás az adatok véletlen kitettség megelőzése. 

Például írható olyan lekérdezés, amely a vezetéknevet és a cím állapota meg paraméterként, és különböző értékek Vezetéknév és a cím állapota felhasználói bemenet alapján hajthat végre.

```sql
    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

A kérelem ezután lehet küldeni a Cosmos DB-hez paraméteres JSON lekérdezésként például alább látható.

```sql
    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }
```

Az első argumentum lze nastavit pomocí paraméterezett lekérdezéseknél, például alább látható.

```sql
    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }
```

A paraméter értéke lehet bármely érvényes JSON (karakterlánc, szám, logikai értékek, NULL értékű, akkor is igaz, tömbök, vagy beágyazott JSON). Is Cosmos DB a séma nélküli, mivel paraméterek a rendszer nem érvényesíti bármilyen ellen.

## <a id="BuiltinFunctions"></a>Beépített függvények
A cosmos DB is támogatja a beépített funkciók számos gyakori műveletekhez, például a felhasználó által definiált függvények (UDF) lekérdezések belül használható.

| Csoport          | Műveletek                                                                                                                                          |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| Matematikai függvények  | ABS, felső határ, EXP, EMELET, LOG, LOG10, POWER, CIKLIKUS, bejelentkezési, SQRT, SZÖGLETES, csonk, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN és TAN |
| Funkciók ellenőrzése típusa | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED és IS_PRIMITIVE                                                           |
| Sztringfüggvények        | CONCAT, tartalmazza, ENDSWITH, INDEX_OF, balra, hossza, alsó, LTRIM, cserélje le, REPLIKÁLJA, fordított, jobb, RTRIM, STARTSWITH, SUBSTRING és felső       |
| Tömb funkciók         | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH és ARRAY_SLICE                                                                                         |
| Térbeli funkciók       | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID és ST_ISVALIDDETAILED                                                                           | 

Ha jelenleg használja a felhasználói függvény (UDF), amelynek beépített függvény már elérhető, használja a megfelelő beépített funkciót, és hogy gyorsabb futtatni fogja, és hatékonyabban. 

### <a name="mathematical-functions"></a>Matematikai függvények
A matematika függvényekkel hajtsa végre a számítási, amelyek argumentumként szolgálnak, és a egy numerikus értéket adja vissza a bemeneti értékek alapján. A következő támogatott beépített matematikai függvények tábláját.


| Használat | Leírás |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [[ABS (num_expr)](#bk_abs) | A megadott numerikus kifejezés (pozitív) abszolút értékét adja vissza. |
| [Felső HATÁRÁT (num_expr)](#bk_ceiling) | A legkisebb egész értéket ad vissza, nagyobb vagy egyenlő a megadott numerikus kifejezés. |
| [EMELET (num_expr)](#bk_floor) | Visszaadja a legnagyobb egész szám kisebb vagy egyenlő a megadott numerikus kifejezés. |
| [EXP (num_expr)](#bk_exp) | A megadott numerikus kifejezés hatványát adja vissza. |
| [NAPLÓ (num_expr [, Alap])](#bk_log) | A megadott numerikus kifejezés, vagy a megadott használatával logaritmus természetes alapú logaritmusát adja vissza |
| [LOG10 (num_expr)](#bk_log10) | A megadott numerikus kifejezés 10-es logaritmikus értékét adja vissza. |
| [ROUND (num_expr)](#bk_round) | Egy numerikus értéket, kerekítve a legközelebbi egész értéket ad vissza. |
| [CSONK (num_expr)](#bk_trunc) | Egy numerikus érték, csonkolva, a legközelebbi egész értéket ad vissza. |
| [SQRT (num_expr)](#bk_sqrt) | A megadott numerikus kifejezés négyzetgyökét adja vissza. |
| [SQUARE (num_expr)](#bk_square) | A megadott numerikus kifejezés négyzetét adja vissza. |
| [ENERGIAGAZDÁLKODÁSI (num_expr, num_expr)](#bk_power) | A megadott numerikus kifejezés hatékonyságát megadott értéket adja vissza. |
| [BEJELENTKEZÉS (num_expr)](#bk_sign) | A bejelentkezés (-1, 0, 1) a megadott numerikus kifejezés értékét adja vissza. |
| [ACOS (num_expr)](#bk_acos) | Adja vissza a szög radiánban, amelynek a koszinusza a megadott numerikus kifejezés; egy szám arkusz koszinusza néven is ismert. |
| [ASIN (num_expr)](#bk_asin) | Adja vissza a szög radiánban, amelynek szinusza a megadott numerikus kifejezés. Arkuszszinusz ezt is nevezik. |
| [ATAN (num_expr)](#bk_atan) | Adja vissza a szög radiánban, amelynek tangense a megadott numerikus kifejezés. Arkusztangens ezt is nevezik. |
| [ATN2 (num_expr)](#bk_atn2) | Adja vissza a szög radiánban, közötti pozitív x tengely és a ray a forrásból a pont (y, x), ahol x és y értékek a két megadott úszó kifejezésre. |
| [COS (num_expr)](#bk_cos) | A megadott kifejezést az radiánban megadott szög, trigonometriai koszinuszát adja vissza. |
| [COT (num_expr)](#bk_cot) | A megadott szög trigonometriai kotangensét adja vissza radiánban, a megadott numerikus kifejezés. |
| [FOK (num_expr)](#bk_degrees) | A megfelelő szöget adja vissza fokban a egy radiánban megadott szög. |
| [PI ()](#bk_pi) | Az állandó a PI értékét adja vissza. |
| [RADIANS (num_expr)](#bk_radians) | Adja vissza radiánban, ha egy numerikus kifejezés, fokban is meg kell adni. |
| [SIN (num_expr)](#bk_sin) | A megadott kifejezést az radiánban megadott szög, trigonometriai szinuszát adja vissza. |
| [TAN (num_expr)](#bk_tan) | A bemeneti kifejezést tangensét adja vissza a megadott kifejezésben. |

Ha például már lekérdezéseket is futtathat a következőhöz hasonló:

**Lekérdezés**

```sql
    SELECT VALUE ABS(-4)
```

**Results**

```json
    [4]
```
A Cosmos DB-függvények ANSI SQL képest közötti fő különbség a, hogy jól működnek a séma nélküli, és a vegyes adatszerkezetek tervezték. Például ha egy dokumentumot, ahol a Size tulajdonság hiányzik, vagy rendelkezik egy nem numerikus értéket, például "Ismeretlen", majd a dokumentum keresztül, kihagyva helyett hibát adnak vissza.

### <a name="type-checking-functions"></a>Funkciók ellenőrzése típusa
A típus ellenőrzése funkciók lehetővé teszik az SQL-lekérdezések belül egy kifejezés típusának ellenőrzése. Típus ellenőrzése funkciók segítségével határozható meg, hogy menet közben a dokumentumokon belül tulajdonságok típusú változó vagy ismeretlen. Íme a functions ellenőrzése támogatott beépített típusú táblázatot.

<table>
<tr>
  <td><strong>Használat</strong></td>
  <td><strong>Leírás</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (kifejezés)</a></td>
  <td>Egy logikai érték, amely azt jelzi, ha az érték típusa tömböt ad vissza.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (kifejezés)</a></td>
  <td>Egy logikai jelezve, hogy ha az érték típusa egy logikai érték beolvasása.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (kifejezés)</a></td>
  <td>Adja vissza egy logikai érték, amely azt jelzi, ha az érték típusa null.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (kifejezés)</a></td>
  <td>Egy logikai érték, amely azt jelzi, ha az érték típusa egy számot ad vissza.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (kifejezés)</a></td>
  <td>Egy logikai érték, amely azt jelzi, ha az érték típusa egy JSON-objektumot ad vissza.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (kifejezés)</a></td>
  <td>Egy logikai érték, amely azt jelzi, ha az érték típusa egy karakterláncot ad vissza.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (kifejezés)</a></td>
  <td>Jelzi, ha a tulajdonság hozzá lett rendelve egy érték logikai érték beolvasása.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (kifejezés)</a></td>
  <td>Egy logikai érték, amely azt jelzi, ha az érték típusa egy karakterlánc, szám, logikai vagy null értékű beolvasása.</td>
</tr>

</table>

Ezek a függvények használatával, mostantól lekérdezéseket is futtathat a következőhöz hasonló:

**Lekérdezés**

```sql
    SELECT VALUE IS_NUMBER(-4)
```

**Results**

```json
    [true]
```

### <a name="string-functions"></a>Sztringfüggvények
A következő skaláris függvények végrehajtania egy műveletet a bemeneti karakterlánc-érték, és a egy karakterlánc, numerikus vagy logikai értéket adja vissza. A következő beépített karakterlánc-függvények táblázatát:

| Használat | Leírás |
| --- | --- |
| [LENGTH (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length) |A megadott karakterlánc-kifejezés karakterek számát adja vissza |
| [CONCAT (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat) |Legalább két karakterlánc-értékek összetűzésének eredménye karakterláncként adja vissza. |
| [KARAKTERLÁNCRÉSZLET (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring) |Egy karakterlánc-kifejezés részét adja vissza. |
| [STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith) |Adja vissza egy logikai jelzi-e az első karakterlánc-kifejezés második kezdődik |
| [ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith) |Adja vissza egy logikai jelzi-e az első karakterlánc-kifejezés második végződik |
| [TARTALMAZZA (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains) |Visszaadja egy logikai arról a második-e az első karakterlánc-kifejezést tartalmaz. |
| [INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of) |A második első előfordulásának kezdőpozícióját adja vissza karakterlánc-kifejezés található a megadott karakterlánc első kifejezés, vagy a -1, ha a karakterlánc nem található. |
| [LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left) |A megadott számú karakterből álló karakterlánc bal oldali részét adja vissza. |
| [JOBB (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right) |A megadott számú karaktert a karakterlánc jobb oldali részét adja vissza. |
| [LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim) |Egy karakterlánc-kifejezés adja vissza, miután eltávolítja a vezető üres. |
| [RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim) |Egy karakterlánc-kifejezés után az összes záró szóközöket csonkolására adja vissza. |
| [ALSÓ (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower) |Egy karakterlánc-kifejezés nagybetűt adatok átalakítása kisbetűvé után adja vissza. |
| [FELSŐ (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper) |Egy karakterlánc-kifejezés után kisbetűt adatok nagybetűssé alakításával adja vissza. |
| [Cserélje le a (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace) |A megadott karakterlánc értéket az összes előfordulását lecseréli egy másik karakterláncérték. |
| [REPLIKÁLÁS (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/sql-api-sql-query-reference#bk_replicate) |Egy karakterláncértéket a megadott számú alkalommal ismétlődik. |
| [FORDÍTOTT (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse) |A karakterlánc-érték megfelelő sorrendben adja vissza. |

Ezek a függvények használatával, mostantól lekérdezéseket is futtathat a következőhöz hasonló. Például lépjen vissza a családnevet nagybetűs módon:

**Lekérdezés**

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

**Results**

```json
    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]
```

Vagy az összefűzés például ebben a példában:

**Lekérdezés**

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

**Results**

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]
```

Karakterlánc-függvények is szűrheti az eredményeket, mint például az alábbi példában a WHERE záradékban használhatók:

**Lekérdezés**

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

**Results**

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Tömb funkciók
A következő skaláris függvények végrehajtania egy műveletet a egy tömb bemeneti érték és a visszaadandó numerikus, logikai és a tömb értéket. A következő beépített tömb függvények táblázatát:

| Használat | Leírás |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length) |A megadott tömb kifejezés elemek számát adja vissza. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat) |Egy tömb, amely az eredménye, összefűzi a két vagy több tömb értéket adja vissza. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains) |Jelzi, hogy a tömb tartalmazza-e a megadott érték logikai érték beolvasása. Megadhatja, ha az egyezés-e a teljes vagy részleges. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice) |Egy tömböt megadó kifejezést részét adja vissza. |

Tömb funkciók segítségével kezelheti a tömb JSON belül használható. Ha például van itt egy lekérdezést, amely az összes dokumentumot visszaadja, ahol a szülők egyik "Robin Wakefield". 

**Lekérdezés**

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

**Results**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Az egyező elemeket a tömbön belüli részleges töredéket is megadhat. A következő lekérdezés az összes szülők megkeresi a `givenName` , `Robin`.

**Lekérdezés**

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

**Results**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Íme egy másik példa használó ARRAY_LENGTH beolvasni a család kiszolgálónként gyermekek száma.

**Lekérdezés**

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

**Results**

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
A cosmos DB a következő nyissa meg a földrajzi Consortium (OGC) beépített függvények támogatja a térinformatikai lekérdezéséhez. 

<table>
<tr>
  <td><strong>Használat</strong></td>
  <td><strong>Leírás</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>A két GeoJSON-pont, Polygon vagy LineString kifejezések között adja vissza a távolságot.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Egy logikai kifejezés, amely azt jelzi, hogy a második GeoJSON-objektum (pont, Polygon vagy LineString) belül van-e az első GeoJSON-objektumot (pont, Polygon vagy LineString) adja vissza.</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Egy logikai kifejezés jelzi, hogy a két megadott GeoJSON objektum (pont, Polygon vagy LineString) átfedésben adja vissza.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Jelzi, hogy a megadott GeoJSON-pont, Polygon vagy LineString kifejezés érvénytelen egy logikai értéket ad vissza.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Egy JSON-értéket tartalmazó logikai értéket, ha a megadott GeoJSON-pont, Polygon vagy LineString kifejezés érvénytelen, és ha érvénytelen értéket ad vissza, továbbá egy olyan karakterláncértéket, az oka.</td>
</tr>
</table>

Térbeli funkciók térbeli adatokon közelségi lekérdezések végrehajtásához használható. Ha például Íme egy lekérdezést, amely visszaadja az összes családi dokumentumot, amelyek 30 km-re, a ST_DISTANCE beépített függvény használatával a megadott helyen belül. 

**Lekérdezés**

```sql
    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Results**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Térinformatikai támogatási Cosmos DB-ben a további részletekért lásd: [Cosmos DB-ben a térinformatikai adatok használata](geospatial.md). Végére értünk térbeli függvények, és az SQL-szintaxis a Cosmos DB. Most vessünk egy pillantást, hogyan működik, és hogyan kommunikál a szintaxissal lekérdezéséhez LINQ megtudtuk, eddig.

## <a id="Linq"></a>LINQ SQL API-hoz
LINQ .NET programozási modell, amely kifejezi az objektumok adatfolyamok lekérdezések, számítások. A cosmos DB biztosítja, hogy a LINQ to felület ügyféloldali kódtár a JSON és a .NET-objektumok és a egy LINQ-lekérdezések egy részét a Cosmos DB-lekérdezésekre leképezés közötti váltás megkönnyítése. 

Az alábbi képen a Cosmos DB használatával LINQ-lekérdezéseket támogató architektúráját mutatja be.  A Cosmos DB-ügyfél segítségével a fejlesztők hozhat létre egy **IQueryable** objektum, amely közvetlenül a Cosmos DB lekérdezési szolgáltató, amely majd a LINQ-lekérdezésekre fordítja le a Cosmos DB-lekérdezés. A lekérdezés majd át kell a Cosmos DB kiszolgáló beolvasására az eredmények JSON formátumban. A kapott találatok közül vannak deszerializálni az ügyféloldali .NET-objektumokat a streambe.

![A LINQ-lekérdezések használata az SQL API-t - SQL-szintaxis, JSON lekérdezési nyelvet, adatbázis-tervezésben és SQL-lekérdezéseket támogató architektúra][1]

### <a name="net-and-json-mapping"></a>.NET és a JSON-leképezés
.NET-objektumokat és JSON-dokumentumok közötti természetes - minden tag adatmező le van képezve egy JSON-objektumot, ahol a mező neve "kulcs" részére, amelyben az objektum le van képezve pedig a "value" rész rekurzív módon leképezve az objektum érték részét. Vegye figyelembe az alábbi példa: A család létrehozott objektum le van képezve a JSON-dokumentum alább látható módon. És ez fordítva is igaz, a JSON-dokumentum vissza a .NET-objektumokat van leképezve.

**C#-osztály**

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

**JSON**  

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
A Cosmos DB-lekérdezésszolgáltató hajt végre, egy Cosmos DB SQL-lekérdezést az ajánlott beavatkozást leképezése a LINQ-lekérdezésekre. A következő leírást feltételezzük az olvasó rendelkezik egy LINQ-alapszintű ismerete.

Először írja be a rendszer, támogatjuk az összes JSON egyszerű típusok – numerikus típusok, logikai érték beolvasása, karakterlánc vagy null. Ezek a JSON típusok támogatottak. A következő skaláris kifejezés támogatottak.

* Állandó értékek – ezek közé tartozik a primitív adattípusokat, állandó értékek a lekérdezés kiértékelése történik az időben.
* Tekintse meg a tulajdonság egy objektum vagy egy tömbelem tulajdonságtömb/index kifejezések – ezek a kifejezések.
  
     család. ID;    Family.children[0].familyName;    Family.children[0].grade;    Family.children[n].grade; n-az-int változó
* Aritmetikai kifejezésekben – ezek közé tartozik a numerikus és logikai értékek a közös aritmetikai kifejezésekben. A teljes listát lásd az SQL-specifikáció.
  
     2 * family.children[0].grade;    x és y;
* Karakterlánc-összehasonlítási kifejezésben – ezek közé tartozik a összehasonlítása egy karakterláncértéket néhány konstans sztring értékre.  
  
     mother.familyName == "Smith";    child.givenName == s. s értéke egy karakterlánc-változóhoz
* Objektum vagy tömb létrehozása kifejezés – ezek a kifejezések visszatérési összetett érték vagy névtelen típusú objektumot, vagy ilyen objektumok egy tömbjét. Ezeket az értékeket is ágyazható be.
  
     új szülő {familyName = "János", givenName = "János"}; új {első = 1, a második = 2}; két mezővel egy anonymní typ              
     új int [] {3, child.grade, 5};

### <a id="SupportedLinqOperators"></a>Támogatott LINQ-operátorokat listája
Íme a LINQ szolgáltatója az SQL .NET SDK-val támogatott LINQ-operátorokat listáját.

* **Válassza ki**: leképezések objektumkonstrukciók többek között SQL SELECT fordítás
* **Ahol**: szűrők az SQL, fordítás, és támogatja a közötti címfordítás & &, || és! az SQL-operátorok
* **SelectMany**: lehetővé teszi a tömbök, az SQL JOIN záradék visszagörgetésének. Lánc és beágyazott tömbelemek rekordsémáját szűrés kifejezések segítségével
* **OrderBy és OrderByDescending**: a rendszer lefordítja arra az ORDER BY növekvő vagy csökkenő sorrendben
* **Száma**, **Sum**, **Min**, **maximális**, és **átlagos** összesítő és a aszinkron megfelelőjükre operátorok**CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, és **AverageAsync**.
* **Compareto metódus végrehajtása**: a rendszer lefordítja arra tartomány összehasonlítást. Általánosan használt karakterláncok, mivel azok még nem összehasonlítható a .NET-ben
* **Igénybe**: a rendszer lefordítja arra az SQL felső korlátozza a lekérdezés eredményei
* **Matematikai függvények**: támogatja a fordítást. A NET Abs, Acos, Asin, Atan, felső határa, Cos, Exp, emelet, Log, Log10, Pow, ciklikus, bejelentkezési, Sin, Sqrt, Tan, Truncate, a megfelelő SQL a beépített funkciók.
* **Karakterlánc-függvények**: támogatja a fordítást. NET a Concat, tartalmazza, EndsWith, IndexOf, Count, ToLower, TrimStart, cserélje le, fordított, TrimEnd, StartsWith, Karakterláncrészletet, a megfelelő SQL a beépített funkciók ToUpper.
* **A tömb funkciók**: támogatja a fordítást. A NET Concat, tartalmazza, és a megfelelő SQL a beépített funkciók száma.
* **Térinformatikai bővítmény függvények**: támogatja a megfelelő SQL a beépített funkciók helyettes módszerek távolság IsValid és IsValidDetailed belül a fordítást.
* **Felhasználó által definiált függvény bővítmény függvény**: a megfelelő felhasználó által definiált függvény a helyettes metódus UserDefinedFunctionProvider.Invoke fordítási támogatja.
* **Vegyes**: támogatja a coalesce, és a feltételes operátorok fordítását. Lefordítja karakterláncot tartalmaz, ARRAY_CONTAINS vagy az SQL a környezettől függően tartalmazza.

### <a name="sql-query-operators"></a>SQL-lekérdezés operátorok
Az alábbiakban néhány példa, amelyek bemutatják, hogyan lefordítani a standard szintű LINQ lekérdezés operátorok némelyike az Cosmos DB-lekérdezések le.

#### <a name="select-operator"></a>Válasszon operátort
A szintaxis `input.Select(x => f(x))`, ahol `f` egy skaláris kifejezés.

**LINQ lambda kifejezés**

    input.Select(family => family.parents[0].familyName);

**SQL** 

```sql
    SELECT VALUE f.parents[0].familyName
    FROM Families f
```

**LINQ lambda kifejezés**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

```sql
    SELECT VALUE f.children[0].grade + c
    FROM Families f 
```


**LINQ lambda kifejezés**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

```sql
    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f
```


#### <a name="selectmany-operator"></a>SelectMany operátor
A szintaxis `input.SelectMany(x => f(x))`, ahol `f` van egy skaláris kifejezés, amely egy gyűjtemény típust ad vissza.

**LINQ lambda kifejezés**

    input.SelectMany(family => family.children);

**SQL** 

```sql
    SELECT VALUE child
    FROM child IN Families.children
```

#### <a name="where-operator"></a>Ahol operátor
A szintaxis `input.Where(x => f(x))`, ahol `f` van egy skaláris kifejezés, amely egy logikai értéket ad vissza.

**LINQ lambda kifejezés**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 
```

**LINQ lambda kifejezés**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3
```

### <a name="composite-sql-queries"></a>Összetett SQL-lekérdezések
A fenti operátorok kell összeállítani, nagyobb teljesítményű lekérdezések kialakításához. Mivel a Cosmos DB támogatja a beágyazott gyűjtemények, az összeállítás összefűzött, vagy a beágyazott.

#### <a name="concatenation"></a>Összefűzés
A szintaxis `input(.|.SelectMany())(.Select()|.Where())*`. Egy összefűzött lekérdezés kezdhet egy nem kötelező `SelectMany` lekérdezés több követ `Select` vagy `Where` operátorok.

**LINQ lambda kifejezés**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
```

**LINQ lambda kifejezés**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

```sql
    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3
```


**LINQ lambda kifejezés**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);

**SQL** 

```sql
    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)
```

**LINQ lambda kifejezés**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

```sql
    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"
```


#### <a name="nesting"></a>A beágyazási
A szintaxis `input.SelectMany(x=>x.Q())` ahol Q a egy `Select`, `SelectMany`, vagy `Where` operátor.

Egy beágyazott lekérdezésen a belső lekérdezés és a külső gyűjtemény összes eleme érvényes. Egyik fontos szolgáltatása, hogy a belső lekérdezés hivatkozhatnak a mezőket, például a külső gyűjtemény elemeinek Önillesztések.

**LINQ lambda kifejezés**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

```sql
    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents
```

**LINQ lambda kifejezés**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));

**SQL** 

```sql
    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"
```


**LINQ lambda kifejezés**

    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

```sql
    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName
```

## <a id="ExecutingSqlQueries"></a>SQL-lekérdezések végrehajtása
A cosmos DB erőforrásokat, amelyek bármilyen, HTTP/HTTPS-kérelem indítására képes nyelv meghívhat REST API-n keresztül tesz elérhetővé. Ezenfelül a Cosmos DB programozási könyvtárakat, mint például a .NET, Node.js, JavaScript és Python számos népszerű nyelvhez biztosít. A REST API és a különböző kódtárak támogatja a lekérdezése SQL használatával. A .NET SDK támogatja a LINQ lekérdezés mellett az SQL.

Az alábbi példák bemutatják, hogyan hozzon létre egy lekérdezést, és küldje el azt egy Cosmos-fiók ellen.

### <a id="RestAPI"></a>REST API-VAL
A cosmos DB egy megnyitott RESTful programozási modellt kínál a HTTP-n keresztül. Cosmos-fiókok bővítheti Azure-előfizetéssel. A Cosmos DB erőforrás-modellje több erőforrást, amelyek mindegyike a megcímezhető logikai és stabil URI segítségével Cosmos-fiókja alatt áll. Erőforráscsoport ebben a dokumentumban hírcsatorna nevezzük. Cosmos-fiók áll adatbázisai, mindegyik több tárolót, mely a-kapcsolja mindegyike tartalmaz, a dokumentumok, felhasználói függvényeket és más erőforrástípusok.

Az alapszintű interakció ezekkel az erőforrásokkal modellje a HTTP-műveletek keresztül GET, PUT, POST és DELETE a standard szintű tolmácsolási szolgáltatással. A POST művelet egy új erőforrást, egy tárolt eljárás végrehajtása vagy egy Cosmos DB lekérdezéssel kiadására használatos. Lekérdezések mindig csak olvasható műveletekhez, nincs mellékhatásokkal.

Az alábbi példák bemutatják egy SQL API-lekérdezés ellen a két minta dokumentumot tartalmazó tároló eddig már áttekinthette bejegyzés. A lekérdezés egy egyszerű szűrő rendelkezik a JSON-name tulajdonság. Vegye figyelembe a használatát a `x-ms-documentdb-isquery` és a Content-Type: `application/query+json` fejlécek jelölésére, hogy-e a művelet egy lekérdezést.

**Kérés**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }


**Results**

```
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

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
                "city":"seattle"
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

A második példa bemutatja egy összetettebb lekérdezés, amely a join több eredményt adja vissza.

**Kérés**

    POST https://<REST URI>/docs HTTP/1.1
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


**Results**

```
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

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

Ha egy lekérdezés eredményeit nem fér el egyetlen oldalnyi találatot belül, akkor a REST API-t adja vissza egy folytatási tokent keresztül a `x-ms-continuation-token` válaszfejléc. Ügyfelek eredmények oldalakra bontása azzal a fejléc a következő eredményeket is. Eredmények száma oldalanként számát is szabályozható a `x-ms-max-item-count` szám fejléc. Ha például egy összesítő függvényt a megadott lekérdezés `COUNT`, akkor a lekérdezés lap egy részlegesen összesített értéket adhat vissza az eredmények oldalát. Az ügyfelek ezekkel az eredményekkel, például a végső eredményt, a számát adja vissza a teljes száma az egyes lapok keresztül összeg fölé kell végrehajtania egy második szintű összesítést.

Lekérdezések az adatok konzisztencia-szabályzat kezeléséhez használja a `x-ms-consistency-level` például minden REST API-kérelem fejléce. A munkamenet-konzisztencia érdekében fontos, hogy a legújabb is echo `x-ms-session-token` a lekérdezési kérelem Cookie-fejlécet. A lekérdezett tároló indexelési házirendet is befolyásolhatja a lekérdezési eredmények konzisztenciáját. Az alapértelmezett indexelési házirend-beállítások, for containers szolgáltatásban az index mindig aktuális a dokumentum tartalma és lekérdezési eredmények megfelelnek a kiválasztott adatok konzisztencia. Ha az indexelési házirendet Lusta van enyhe, lekérdezések elavult eredményeket adhat vissza. További információkért lásd: [Cosmos DB Konzisztenciaszintjeinek][consistency-levels].

Ha a beállított indexelési házirendet a tárolón a megadott lekérdezés nem támogatja, a Cosmos DB kiszolgáló 400 "Hibás kérés" adja vissza. Ez a tartomány-lekérdezéseket az elérési utak kivonata (egyenlőség) kereséseket, valamint az indexelő kifejezetten kizárva elérési utak a konfigurált adja vissza. A `x-ms-documentdb-query-enable-scan` fejléc adható meg, hogy a lekérdezést, hogy vizsgálatot végezzen, ha az index nem érhető el.

Megjelenik a részletes mérőszámokat a lekérdezés-végrehajtás beállításával `x-ms-documentdb-populatequerymetrics` fejlécet `True`. További információkért lásd: [a Cosmos DB SQL-lekérdezés metrikák](sql-api-sql-query-metrics.md).

### <a id="DotNetSdk"></a>C# (.NET) SDK
A .NET SDK támogatja a LINQ- és SQL lekérdezése. Az alábbi példa bemutatja, hogyan jelent meg a jelen dokumentum korábbi az egyszerű szűrő-lekérdezés végrehajtása.

    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Ez a példa összehasonlítja a két tulajdonság hasonlítania az egyezés keresésekor belül minden egyes dokumentum és névtelen leképezések használja. 

    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


A következő minta bemutatja az illesztések, LINQ SelectMany keresztül.

    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



A .NET-ügyfél automatikusan végighalad a lekérdezési eredmények jelennek meg a foreach egységekben minden oldalára. A lekérdezési beállítások ismertetése a REST API-szakaszban is megtalálhatók a .NET SDK használatával a `FeedOptions` és `FeedResponse` osztályok a CreateDocumentQuery metódusban. A lapok száma használatával lehet irányítani a `MaxItemCount` beállítás. 

Explicit módon is szabályozhatja, lapozási létrehozásával `IDocumentQueryable` használatával a `IQueryable` objektumot, majd olvassa el a` ResponseContinuationToken` értékeket, és átadja azokat a biztonsági másolatot `RequestContinuationToken` a `FeedOptions`. `EnableScanInQuery` beállítható a vizsgálatok engedélyezéséhez, ha a lekérdezés a konfigurált indexelési szabályzat által nem támogatott. A particionált tárolók használhatja `PartitionKey` a lekérdezés futtatásához egy partíció (bár a Cosmos DB automatikusan kinyerheti az Ez a lekérdezés szöveg), és `EnableCrossPartitionQuery` előfordulhat, hogy kell futtatni több partíció-lekérdezések futtatásához. 

Tekintse meg [Cosmos DB .NET-minták](https://github.com/Azure/azure-documentdb-net) további mintákat tartalmazó lekérdezések számára. 

### <a id="JavaScriptServerSideApi"></a>JavaScript server-side API
A cosmos DB a JavaScript-alapú alkalmazáslogika végrehajtása a tárolókban tárolt eljárások és eseményindítók használatával közvetlenül a programozási modellt biztosít. A JavaScript-logika regisztrált tároló szintjén majd bocsát ki a műveletek a dokumentumokon a megadott tároló adatbázis-műveleteket. Ezek a műveletek burkolja környezeti ACID-tranzakciókat.

Az alábbi példa bemutatja, hogyan használható a queryDocuments az API a JavaScript-kiszolgálón, hogy a lekérdezések belül tárolt eljárásokkal és eseményindítókkal.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a id="References"></a>Hivatkozások
1. [Az Azure Cosmos DB bemutatása][introduction]
2. [Az Azure Cosmos DB SQL-specifikáció](https://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-documentdb-net)
4. [Az Azure Cosmos DB Konzisztenciaszintjeinek][consistency-levels]
5. ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON-BAN [http://json.org/](http://json.org/)
7. JavaScript-specifikáció [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [http://msdn.microsoft.com/library/bb308959.aspx](https://msdn.microsoft.com/library/bb308959.aspx) 
9. Értékelés technikák nagy méretű adatbázisok lekérdezése [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Párhuzamos relációs adatbázis-rendszerek, nyomja meg a számítógép IEEE Egyesületre, 1994 fel
11. Lu, Ooi, Tan, párhuzamos relációs adatbázis-rendszerek, nyomja meg a számítógép IEEE Egyesületre, 1994 fel.
12. Christopher Olston, Benjamin Reed Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: a Pig Latin: egy nem így idegen nyelvű adatfeldolgozási vagy SIGMOD 2008.
13. G. Graefe. A lekérdezés optimalizálásához kaszkádokban keretében. IEEE-adatok Eng. Bull., 18(3): 1995.

[1]: ./media/sql-api-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
