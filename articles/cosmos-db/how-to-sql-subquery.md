---
title: Az Azure Cosmos DB SQL segédlekérdezések
description: SQL-segédlekérdezést és az Azure Cosmos DB a gyakori alkalmazási helyzetek
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: tisande
ms.openlocfilehash: 3ba547aea9034777fe76f3c911efd2648f6184fa
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514799"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Az Azure Cosmos DB SQL segédlekérdezés példák

Segédlekérdezésben ágyazva egy másik lekérdezésével egy lekérdezést. Allekérdezés egy belső lekérdezés és a belső válassza is nevezik, és az utasítást, segédlekérdezést tartalmazó neve általában egy külső lekérdezést.

Segédlekérdezések két típusa van:

* Korrelált - Korrelált segédlekérdezésben a külső lekérdezésből származó értékek hivatkozó segédlekérdezés. A segédlekérdezés egyszer, která je zpracována a külső lekérdezés minden egyes sorára lesz kiértékelve.

* A nem Korrelált - segédlekérdezés nem A Korrelált független, külső lekérdezés segédlekérdezésben, és a saját nélkül hagyatkoznia a külső lekérdezés is végrehajtható.

> [!NOTE]
> Az Azure Cosmos DB támogatja a csak a kapcsolódó allekérdezés.

## <a name="types-of-subqueries"></a>Segédlekérdezések típusai

Segédlekérdezések további osztályozhatók sorokat és oszlopokat, amelyeket vissza száma alapján. Három különböző típusa van:
1.  **tábla**: Több sort, és több oszlopot ad vissza
2.  **Többértékű**: Több sort és a egy egyoszlopos adja vissza
3.  **Skaláris**: Egy egysoros és a egy oszlop adja vissza

> [!NOTE]
> Az Azure Cosmos DB támogatja a több értéket és skaláris segédlekérdezések

Az Azure Cosmos DB SQL-lekérdezések mindig csak egy oszlop (vagy egy egyszerű érték, vagy egy összetett dokumentumot) ad vissza. Ezért csak többértékű és skaláris segédlekérdezések a fent vonatkoznak az Azure Cosmos DB-ben. Többértékű segédlekérdezés csak használható a FROM záradékban relációs kifejezésként, amíg egy skaláris kifejezés, és a VÁLASSZA vagy a WHERE záradékban vagy egy relációs kifejezés a FROM záradékban használható skaláris segédlekérdezés.


## <a name="multi-value-subqueries"></a>Többértékű segédlekérdezések

Többértékű segédlekérdezések adja vissza azon dokumentumok, és mindig használunk a FROM záradék. Ezek szolgálnak:

* Csatlakozás kifejezések optimalizálása 
* Költséges kifejezések kiértékelése után, és többször hivatkozása

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

Ehhez a lekérdezéshez az index egyezni fog a címke neve "anyatej képlettel", 0 és 10-es és a egy kiszolgáló elemet a 1-nél nagyobb méretű közötti értéket egy makroelem elem minden olyan dokumentumot. Azonban a JOIN kifejezéshez végrehajtja az összes elem címkék tápanyagok és adagok tömbök keresztszorzatát egyes egyező dokumentumok bármely szűrő alkalmazása előtt. A WHERE záradékban a szűrő predikátuma minden < c, t, n, s > rekord érvényes lesz. Például ha egy egyező dokumentum 10 elemet a három tömbök minden, azt bővített 1 x 10 x 10 x 10 (például: 1000) rekordok. Segédlekérdezések ide használatával, segítségével szűri ki a csatlakoztatott tömbelemek, mielőtt a következő kifejezésben.

Ez a lekérdezés egyenértékű a fenti, de a segédlekérdezések használja:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Feltéve, hogy a címkék tömb csak egy elemet a szűrő megfelel, és öt elemek tápanyagok és adagok tömbök, az ILLESZTÉSI kifejezések bővített 1 x 1 x 5 x 5 = 25 elemek 1000 tétel az első lekérdezés helyett.

### <a name="evaluate-once-and-reference-many-times"></a>Egyszer, és hivatkozás sokszor kiértékelése

Segédlekérdezések segíthet a költséges kifejezéseket, például a felhasználó által definiált függvények (UDF) vagy összetett karakterlánc vagy aritmetikai kifejezésekben-lekérdezések optimalizálása. Allekérdezés együtt egy ILLESZTÉSI kifejezés használatával kiértékelni a kifejezést egyszer, de sokszor hivatkozni rá.

A következő lekérdezés az UDF GetMaxNutritionValue kétszer hajtja végre:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Íme egy azzal egyenértékű lekérdezés, amely csak egyszer fut le az UDF-ben:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Az ILLESZTÉSI kifejezésében, termékek közötti viselkedését adja meg az UDF-kifejezés nem definiált való lehetett kiértékelni. Ha, bizonyosodjon meg arról, hogy a JOIN kifejezéseket mindig egy objektumot közvetlenül az értéket, hanem a segédlekérdezés visszaadó egyetlen sor eredményez.
>

Íme egy hasonló példa, amely értéket, hanem egy objektumot ad vissza:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

A módszer nem korlátozva, felhasználói függvényeket, hanem az, hogy bármilyen potenciálisan drága kifejezésre. Például hogy eltarthat ugyanezzel a módszerrel, a függvény matematikai átlaga a:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

### <a name="mimic-join-with-external-reference-data"></a>Külső hivatkozási adatokkal való csatlakozás utánzására

Milyen gyakran kell, amely ritkán változnak, például a mérések vagy országkódok egységeinek statikus referenciaadatok. Az ilyen adatok ezért célszerű nem ismétlődő, egyes dokumentumok. A felesleges ismétlésének elkerülése érdekében a storage mentse, és tartja dokumentum mérete kisebb írási teljesítményének javítása. Az allekérdezés referenciaszámítógépnek belső illesztés szemantika a referencia-adatok gyűjteménye, itt használható.
Például érdemes lehet a referencia-adatok készletét.

| **Egység** | **Name (Név)**            | **Szorzó** | **Alapegység** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogrammos érzékenység is            | 1.00E-09       | Gram          |
| µg       | Analitikai           | 1.00E-06       | Gram          |
| mg       | Milligramm           | 1.00E-03       | Gram          |
| k        | Gram                | 1.00E+00       | Gram          |
| kg       | Kg            | 1.00E + 03       | Gram          |
| Mg       | Megagram            | 1.00E + 06       | Gram          |
| Gg       | Gigagram            | 1.00E + 09       | Gram          |
| nJ       | Nanojoule           | 1.00E-09       | Joule         |
| µJ       | Microjoule          | 1.00E-06       | Joule         |
| mJ       | Millijoule          | 1.00E-03       | Joule         |
| C        | Joule               | 1.00E+00       | Joule         |
| kJ       | Kalápocsonkénti           | 1.00E + 03       | Joule         |
| MJ       | Megajoule           | 1.00E + 06       | Joule         |
| GJ       | Gigajoule           | 1.00E + 09       | Joule         |
| CAL      | Kalória             | 1.00E+00       | kalória       |
| kcal     | Kalória             | 1.00E + 03       | kalória       |
| IU       | Nemzetközi |                |               |


A következő lekérdezés utánozza, az alábbi adatokkal való csatlakozás, hogy a kimeneti hozzáadjuk az egység nevét:

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

Egy skaláris segédlekérdezés kifejezés csak egyetlen értéket visszaadó segédlekérdezés. A segédlekérdezés skaláris kifejezés értéke a segédlekérdezés vetülete (SELECT záradékban) értékét.  Egy skaláris segédlekérdezés kifejezés használható számos helyen egy skaláris kifejezés érvénytelen. Egy skaláris allekérdezés például bármely mindkét VÁLASSZA a kifejezést, és a WHERE záradék használható.
Azonban használatával skaláris allekérdezés nem mindig segít optimalizálni. Például skaláris allekérdezés átadása argumentumként egy rendszerhez vagy a felhasználó által definiált függvények haszna nincs, a fogyasztott vagy a késés.

A segédlekérdezések skaláris további meghatározhassa:
* Egyszerű kifejezést skaláris segédlekérdezések
* Összesített skaláris segédlekérdezések

### <a name="simple-expression-scalar-subqueries"></a>Egyszerű kifejezést skaláris segédlekérdezések

Egy egyszerű kifejezést skaláris allekérdezés a SELECT záradékban, amely nem tartalmaz semmilyen összesítő kifejezések rendelkező korrelált segédlekérdezésben. Ezeket a segédlekérdezések nem optimalizálási előnyt nyújtanak, mert a fordító alakítja át őket egy nagyobb egyszerű kifejezés. Nincs kapcsolódó környezet között a belső és külső lekérdezést.

Íme néhány példa:

**1. példa**

```sql
SELECT 1 AS a, 2 AS b
```

Ez a lekérdezés sikerült kell írni, egy egyszerű kifejezést skaláris allekérdezés való használatával:

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

Ez a lekérdezés sikerült kell írni, egy egyszerű kifejezést skaláris allekérdezés való használatával:

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

Ez a lekérdezés sikerült kell írni, egy egyszerű kifejezést skaláris allekérdezés való használatával:

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

## <a name="aggregate-scalar-subqueries"></a>Összesített skaláris segédlekérdezések

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

Több aggregátumfüggvény kifejezésekkel allekérdezés:

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

A leképezési és a szűrő egy összesített segédlekérdezést tartalmazó lekérdezést:

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

Ez a lekérdezés írása több optimális úgy, hogy csatlakozzon a segédlekérdezés, és hivatkozhat a segédlekérdezés alias a egyaránt a SELECT és a WHERE záradék. Ez a lekérdezés azért hatékonyabb, mert a segédlekérdezés csak a join utasítás és nem található a leképezési és a szűrés végrehajtásához szükség lesz.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

### <a name="exists-expression"></a>EXISTS kifejezés

Az Azure Cosmos DB támogatja az EXISTS kifejezés. Ez a beépített az Azure Cosmos DB SQL API egy összesített skaláris segédlekérdezés. EXISTS egy logikai kifejezés, amely egy segédlekérdezés kifejezést, és ha a segédlekérdezés; sorokat ad vissza IGAZ értéket ad vissza Ellenkező esetben az eredmény false (hamis).
Mivel az Azure Cosmos DB SQL API nem tesz különbséget a logikai kifejezéseket és bármely más skaláris kifejezések között, mindkét VÁLASSZA EXISTS használható és a WHERE záradék. Ez nem a T-SQL, ahol egy logikai kifejezés (pl. EXISTS, között, valamint a) korlátozódik, a szűrő.

Ha az EXISTS allekérdezés nem definiált egyetlen értéket ad vissza, akkor a program a EXISTS hamis értékeli. Például vegye figyelembe a következő lekérdezést, amely a kifejezés hamis:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Azonban ha kihagyja a fenti allekérdezésben érték kulcsszó majd a lekérdezés is igaz értéked:
```sql
SELECT EXISTS (SELECT undefined) 
```

A segédlekérdezés fog idézőjelek közé kell tenni az értékek listáját egy objektum a kiválasztási listán. A kiválasztott lista nem tartalmaz értéket, ha a segédlekérdezés egyetlen értéket fogja visszaadni a(z){}"amelyhez definiálva van, és így létezik igaz értéket ad vissza.

### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>Példa: Átírása ARRAY_CONTAINS és Csatlakozás másként EXISTS

Egy gyakori alkalmazási helyzet ARRAY_CONTAINS az, hogy a dokumentum szűrés létezik-e egy elem a tömbben. Ebben az esetben, ha a címkék tömböt tartalmaz-e egy nevű elem narancssárga rendszer ellenőrzi.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Ugyanabból a lekérdezés sikerült kell írni EXISTS használatára:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Ezenkívül ARRAY_CONTAINS csak akkor tudja ellenőrizze, hogy egy érték egyenlő-e bármely elem v poli. Összetettebb szűrők tömb tulajdonságok van szükség, ha az ILLESZTÉS szükség.

Vegye figyelembe, hogy alapján szűri az egységek és nutritionValue tulajdonságok a tömbben található a következő lekérdezést: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Minden egyes a gyűjteményben a dokumentumok esetében a termékek közötti megtörténik a tömbelemek rekordsémáját. Az ILLESZTÉSI művelet lehetővé teszi a tömbön belüli tulajdonságok alapján végezhet szűrést. Ez a lekérdezés fogyasztott azonban jelentős lesz. Például ha 1000 dokumentum 100 elemet minden tömb, akkor bővített 1000 x 100 (azaz 100 000) rekordok.

Használatával `EXISTS` segíthet elkerülni a költséges termékcsaládon belüli:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

Ebben az esetben azt szűrhet a EXISTS segédlekérdezés található tömbelemek rekordsémáját. Ha egy tömbelem megegyezik a szűrőt, majd, hogy a projekt és `EXISTS` igaz értéket ad vissza.

Azt is alias EXISTS és hivatkozzon arra a leképezés:

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

### <a name="array-expression"></a>TÖMB kifejezés

A `ARRAY` kifejezés használható projekt tömbként egy lekérdezés eredményeit. Ez a kifejezés csak akkor használható a SELECT záradék a lekérdezés belül.

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

Csakúgy, mint más segédlekérdezések szűri az a `ARRAY` kifejezés is előfordulhatnak.

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
