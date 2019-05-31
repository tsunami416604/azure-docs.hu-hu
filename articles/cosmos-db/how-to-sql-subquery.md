---
title: Az Azure Cosmos DB SQL segédlekérdezések
description: SQL-segédlekérdezést és az Azure Cosmos DB a gyakori alkalmazási helyzetek
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: tisande
ms.openlocfilehash: 68465f4ca195930ce08bb579e68d0227e9c18dd6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242845"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Az Azure Cosmos DB SQL segédlekérdezés példák

Segédlekérdezésben ágyazva egy másik lekérdezésével egy lekérdezést. Egy belső lekérdezés vagy a belső válassza ki az allekérdezés is nevezik. Az utasítást, segédlekérdezést tartalmazó neve általában egy külső lekérdezést.

Ez a cikk bemutatja az SQL-segédlekérdezést és az Azure Cosmos DB a gyakori alkalmazási helyzetek.

## <a name="types-of-subqueries"></a>Segédlekérdezések típusai

Segédlekérdezések két fő típusa van:

* **Korrelált**: A külső lekérdezésből származó értékek hivatkozó segédlekérdezés. A segédlekérdezés egyszer, amely feldolgozza a külső lekérdezés minden egyes sorára lesz kiértékelve.
* **Nem korrelált**: A külső lekérdezés független segédlekérdezés. A saját anélkül, hogy az a külső lekérdezés futtatható.

> [!NOTE]
> Az Azure Cosmos DB támogatja a csak a kapcsolódó allekérdezés.

Segédlekérdezések további osztályozhatók sorokat és oszlopokat, amelyeket vissza száma alapján. Három feltételtípus érhető el:
* **tábla**: Több sort, és több oszlopot ad vissza.
* **Többértékű**: Több sort és a egy egyoszlopos adja vissza.
* **Skaláris**: Egyetlen sor és a egy egyoszlopos adja vissza.

Az Azure Cosmos DB SQL-lekérdezések mindig csak egy oszlop (vagy egy egyszerű érték, vagy egy összetett dokumentumot) ad vissza. Ezért csak a többértékű és skaláris segédlekérdezések vonatkoznak az Azure Cosmos DB-ben. Többértékű segédlekérdezés csak a FROM záradékban használható relációs kifejezésként. Egy skaláris kifejezés, és a VÁLASSZA vagy a WHERE záradékban vagy a FROM záradékban relációs kifejezésként használható skaláris segédlekérdezés.


## <a name="multi-value-subqueries"></a>Többértékű segédlekérdezések

Többértékű segédlekérdezések adja vissza azon dokumentumok, és mindig használunk a FROM záradék. A használhatók:

* Optimalizálás ILLESZTÉSI kifejezésében. 
* Költséges kifejezések kiértékelése után, és többször hivatkozik.

### <a name="optimize-join-expressions"></a>Csatlakozás kifejezések optimalizálása

Többértékű segédlekérdezések optimalizálhatja ILLESZTÉSI kifejezésében predikátumok küldésével, minden egyes kiválasztása – több kifejezés után, nem pedig után minden cross-összekapcsolások a WHERE záradékban.

Vegye figyelembe a következő lekérdezést:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Ehhez a lekérdezéshez az index egyezni fog minden olyan dokumentumot, amely rendelkezik egy címkét a neve "anyatej képlettel." Ez nem egy 0 és 10 közötti értéket makroelem elem és a egy kiszolgáló elemet a 1-nél nagyobb méretű. A JOIN kifejezéshez címkék tápanyagok és adagok tömbök minden elem keresztszorzatát elvégzi az egyes egyező dokumentumok, bármely szűrő alkalmazása előtt. 

A WHERE záradékban a szűrő predikátuma minden < c, t, n, s > rekord érvényes lesz. Például ha egy egyező dokumentum 10 elemet a három tömbök minden, azt bővített 1 x 10 x 10 x 10 (azaz 1000) rekordok. Itt a segédlekérdezések használata segíthet szűri ki a csatlakoztatott tömbelemek, mielőtt a következő kifejezésben.

Ez a lekérdezés megegyezik az előző egy, de a segédlekérdezések használja:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Tegyük fel, hogy a címkék tömb csak egy elemet a szűrő megfelel, és tartoznak tápanyagok és a adagok tömbök öt elemek. Az ILLESZTÉSI kifejezések majd bővített 1 x 1 x 5 x 5 = 25 elemek 1000 tétel az első lekérdezés helyett.

### <a name="evaluate-once-and-reference-many-times"></a>Egyszer, és hivatkozás sokszor kiértékelése

Segédlekérdezések segítségével, például a felhasználó által definiált függvények (UDF), az összetett karakterláncokat vagy a számtani kifejezéseket költséges kifejezésekkel-lekérdezések optimalizálása. Allekérdezés együtt egy ILLESZTÉSI kifejezés használatával kiértékelni a kifejezést egyszer, de sokszor hivatkozni rá.

A következő lekérdezést futtat az UDF `GetMaxNutritionValue` kétszer:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Itt látható egy azonos lekérdezés, amely az UDF csak egyszer fut:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Vegye figyelembe az ILLESZTÉSI kifejezésében termékcsaládon belüli viselkedését. Ha nem definiált az UDF-kifejezés kiértékelése is biztosítania kell, hogy a JOIN kifejezéseket mindig egy objektumot közvetlenül az értéket, hanem a segédlekérdezés visszaadó egyetlen sor eredményez.
>

Íme egy hasonló példa, amely értéket, hanem egy objektumot ad vissza:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

A módszer nem korlátozódik el az UDF-EK. Bármely potenciálisan drága kifejezés vonatkozik. Ugyanezzel a módszerrel a matematikai függvénnyel Vegyünk példának `avg`:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

### <a name="mimic-join-with-external-reference-data"></a>Külső hivatkozási adatokkal való csatlakozás utánzására

Gyakran szüksége lehet, amely ritkán változnak, például a mérési vagy országkódok egységeinek statikus referenciaadatok. Jobb, ha nincs ilyen adatok minden egyes dokumentum esetében ismétlődő. A felesleges ismétlésének elkerülése érdekében a storage mentse, és tartja a dokumentum mérete kisebb írási teljesítményének javítása. Használhatja a segédlekérdezés referenciaszámítógépnek belső illesztés szemantika a referencia-adatok gyűjteménye.

Például érdemes lehet a referencia-adatok készletét:

| **Egység** | **Name (Név)**            | **Szorzó** | **Alapegység** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogrammos érzékenység is            | 1.00E-09       | Gram          |
| µg       | Analitikai           | 1.00E-06       | Gram          |
| mg       | Milligramm           | 1.00E-03       | Gram          |
| g        | Gram                | 1.00E+00       | Gram          |
| kg       | Kg            | 1.00E + 03       | Gram          |
| Mg       | Megagram            | 1.00E + 06       | Gram          |
| Gg       | Gigagram            | 1.00E + 09       | Gram          |
| nJ       | Nanojoule           | 1.00E-09       | Joule         |
| µJ       | Microjoule          | 1.00E-06       | Joule         |
| mJ       | Millijoule          | 1.00E-03       | Joule         |
| J        | Joule               | 1.00E+00       | Joule         |
| kJ       | Kalápocsonkénti           | 1.00E + 03       | Joule         |
| MJ       | Megajoule           | 1.00E + 06       | Joule         |
| GJ       | Gigajoule           | 1.00E + 09       | Joule         |
| CAL      | Kalória             | 1.00E+00       | Kalória       |
| kcal     | Kalória             | 1.00E + 03       | Kalória       |
| IU       | Nemzetközi |                |               |


A következő lekérdezés utánozza, az alábbi adatokkal való csatlakozás, hogy az egység nevét hozzá a kimenetet:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>A segédlekérdezések skaláris

Egy skaláris segédlekérdezés kifejezés csak egyetlen értéket visszaadó segédlekérdezés. A segédlekérdezés skaláris kifejezés értéke a segédlekérdezés vetülete (SELECT záradékban) értékét.  Használhat egy segédlekérdezés skaláris kifejezés sok helyen, ahol egy skaláris kifejezés érvénytelen. Például minden olyan mindkét VÁLASSZA a kifejezést, és a WHERE záradék skaláris allekérdezés is használhatja.

Használatával skaláris allekérdezés nem mindig segít optimalizálni, azonban. Például skaláris allekérdezés átadása argumentumként egy rendszerhez vagy a felhasználó által definiált függvények haszna nincs, az erőforrás-használat egység (RU) vagy a késés.

A segédlekérdezések skaláris további meghatározhassa:
* Egyszerű kifejezést skaláris segédlekérdezések
* Összesített skaláris segédlekérdezések

### <a name="simple-expression-scalar-subqueries"></a>Egyszerű kifejezést skaláris segédlekérdezések

Egy egyszerű kifejezést skaláris segédlekérdezésben, amely rendelkezik a SELECT záradékban, amely nem tartalmaz semmilyen összesítő kifejezések kapcsolódó allekérdezés. Ezeket a segédlekérdezések nem optimalizálási előnyt nyújtanak, mert a fordító alakítja át őket egy nagyobb egyszerű kifejezés. Nincs kapcsolódó környezet a belső és külső lekérdezések között.

Íme néhány példa:

**1. példa**

```sql
SELECT 1 AS a, 2 AS b
```

Ez a lekérdezés egy egyszerű kifejezést skaláris allekérdezés használatával is újraírási:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Mindkét lekérdezést Ez a kimenet előállításához:

```json
[
  { "a": 1, "b": 2 }
]
```

**2. példa**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Ez a lekérdezés egy egyszerű kifejezést skaláris allekérdezés használatával is újraírási:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Lekérdezés kimenete:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**3. példa**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Ez a lekérdezés egy egyszerű kifejezést skaláris allekérdezés használatával is újraírási:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Lekérdezés kimenete:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Összesített skaláris segédlekérdezések

Egy összesített skaláris segédlekérdezésben, amely rendelkezik a leképezés vagy egyetlen értéket visszaadó szűrő aggregátumfüggvényt segédlekérdezésben.

**1. példa:**

Itt látható egy összesítő függvény egyetlen kifejezéssel a leképezés a segédlekérdezés:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Lekérdezés kimenete:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**2. példa**

A következő segédlekérdezés több aggregátumfüggvény kifejezésekkel:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Lekérdezés kimenete:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**3. példa**

Itt látható egy összesítő segédlekérdezés a leképezési és a szűrő-lekérdezés:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Lekérdezés kimenete:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Ez a lekérdezés írása több optimális úgy, hogy csatlakozzon a segédlekérdezés, és hivatkozhat a segédlekérdezés alias a egyaránt a SELECT és a WHERE záradék. Ez a lekérdezés hatékonyabb, mert szüksége lesz a segédlekérdezés csak a join utasítás belül, és nem a leképezési és a szűrő a végrehajtásához.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

#### <a name="exists-expression"></a>EXISTS kifejezés

Az Azure Cosmos DB támogatja az EXISTS kifejezés. Ez a beépített az Azure Cosmos DB SQL API egy összesített skaláris segédlekérdezés. EXISTS egy logikai kifejezés, amely egy segédlekérdezés kifejezést, és ha a segédlekérdezés azokat a sorokat ad vissza IGAZ értéket ad vissza. Ellenkező esetben az eredmény false (hamis).

Mivel az Azure Cosmos DB SQL API nem tesz különbséget a logikai kifejezésen, és bármely más skaláris kifejezések között, és mindkét VÁLASSZA az EXISTS és a WHERE záradék is használhatja. Ez nem a T-SQL, ahol egy logikai kifejezés (például EXISTS, között, valamint a) korlátozódik, a szűrő.

Az EXISTS allekérdezés nincs definiálva, létezik-e egyetlen értéket ad vissza. Ha hamis értékre fogja kiértékelni. Például vegye figyelembe a következő lekérdezést, amely a kifejezés hamis:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Ha az érték kulcsszó, az előző allekérdezés nincs megadva, a lekérdezés is igaz értéked:
```sql
SELECT EXISTS (SELECT undefined) 
```

A segédlekérdezés értékek listája lesz tegye az objektum a kijelölt listához. A kiválasztott lista nem tartalmaz értéket, ha a segédlekérdezés egyetlen értéket fogja visszaadni a(z){}". Ez az érték van meghatározva, hogy a EXISTS igaz értéket ad vissza.

#### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>Példa: Átírása ARRAY_CONTAINS és Csatlakozás másként EXISTS

Egy gyakori alkalmazási helyzet ARRAY_CONTAINS az, hogy a dokumentum szűrés létezik-e egy elem a tömbben. Ebben az esetben mi vagyunk ellenőrzése, ha a címkék tömböt tartalmaz-e a "narancs" nevű elem

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

EXISTS használandó ugyanabból a lekérdezés is újraírási:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Ezenkívül ARRAY_CONTAINS csak azt tudja ellenőrizni egy érték egyenlő-e bármely elem egy tömbben. Ha összetettebb szűrőket a tömb tulajdonságok van szüksége, használja az ILLESZTÉSI.

Vegye figyelembe a következő lekérdezést, amely szűri az egységek alapján és `nutritionValue` a tömbben található tulajdonságok: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Minden egyes a gyűjteményben a dokumentumok esetében a termékek közötti megtörténik a tömbelemek rekordsémáját. Az ILLESZTÉSI művelet lehetővé teszi a tömbön belüli tulajdonságok alapján végezhet szűrést. Ez a lekérdezés fogyasztott azonban jelentős lesz. Például ha 1000 dokumentum 100 elemet minden tömb, akkor bővített 1000 x 100 (azaz 100 000) rekordok.

EXISTS használata segíthet elkerülni a költséges termékcsaládon belüli:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

Ebben az esetben a EXISTS segédlekérdezés található tömbelemek szűrésre. Ha egy tömbelem megegyezik a szűrőt, majd projektre, és EXISTS igaz értéket ad vissza.

Is alias EXISTS és hivatkozzon arra a leképezés:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Lekérdezés kimenete:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

#### <a name="array-expression"></a>TÖMB kifejezés

A TÖMB kifejezés használatával projekt tömbként egy lekérdezés eredményeit. Ez a kifejezés a lekérdezés csak a SELECT záradékban belül is használhatja.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Lekérdezés kimenete:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Csakúgy, mint más segédlekérdezések a TÖMBÖT megadó kifejezést a szűrőkkel is előfordulhatnak.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Lekérdezés kimenete:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Tömb kifejezéseket is visszatérhet a FROM záradék a segédlekérdezésekben után.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Lekérdezés kimenete:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>További lépések

- [SQL-lekérdezés példák](how-to-sql-query.md)
- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dokumentumadatok modellezése](modeling-data.md)
