---
title: SQL-allekérdezések az Azure Cosmos DB-hez
description: Ismerje meg az SQL-allekérdezéseket, azok gyakori használati eseteit és különböző típusú allekérdezéseit az Azure Cosmos DB-ben
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 42d9e8b190747a3ffaf0e46ea1eddda33d09bb24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870564"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Példák az Azure Cosmos DB SQL-segédlekérdezésére

Az allekérdezés egy másik lekérdezésbe ágyazott lekérdezés. Az allekérdezést belső lekérdezésnek vagy belső kijelölésnek is nevezik. A segédlekérdezést tartalmazó utasítást általában külső lekérdezésnek nevezik.

Ez a cikk az SQL-allekérdezéseket és azok gyakori használati eseteit ismerteti az Azure Cosmos DB-ben. A dokumentumban lévő összes mintalekérdezés futtatható az Azure [Cosmos DB Query Playground](https://www.documentdb.com/sql/demo)előzetesen betöltött tápérték-adatkészletén.

## <a name="types-of-subqueries"></a>Allekérdezések típusai

Az allekérdezéseknek két fő típusa van:

* **Korrelált**: A külső lekérdezés értékeit hivatkozó segédlekérdezés. A külső lekérdezés minden egyes sorában a rendszer egyszer kiértékeli a segédlekérdezést.
* **Nem korrelált**: A külső lekérdezéstől független segédlekérdezés. A külső lekérdezésre való támaszkodás nélkül is futtatható.

> [!NOTE]
> Az Azure Cosmos DB csak korrelált allekérdezéseket támogat.

A segédlekérdezések tovább sorolhatók a visszaadott sorok és oszlopok száma alapján. Három típusa van:
* **Táblázat**: Több sort és több oszlopot ad eredményül.
* **Többértékű**: Több sort és egy oszlopot ad eredményül.
* **Skaláris**: Egyetlen sort és egyetlen oszlopot ad eredményül.

Sql-lekérdezések az Azure Cosmos DB mindig egyetlen oszlopot ad vissza (egyszerű érték vagy összetett dokumentum). Ezért csak többértékű és skaláris allekérdezések alkalmazhatók az Azure Cosmos DB-ben. A többértékű segédlekérdezést csak relációs kifejezésként használhatjuk a FROM záradékban. A skaláris segédlekérdezést skaláris kifejezésként használhatja a SELECT vagy a WHERE záradékban, vagy relációs kifejezésként a FROM záradékban.

## <a name="multi-value-subqueries"></a>Többértékű allekérdezések

A többértékű allekérdezések dokumentumok halmazát adják vissza, és mindig a FROM záradékban használatosak. Ezeket használják:

* JOIN-kifejezések optimalizálása. 
* Drága kifejezések egyszer történő kiértékelése és többszöri hivatkozás.

## <a name="optimize-join-expressions"></a>JOIN-kifejezések optimalizálása

A többértékű segédlekérdezések optimalizálhatják a JOIN-kifejezéseket úgy, hogy minden egyes többes kijelölésű kifejezés után lenyomják a predikátumokat, nem pedig a WHERE záradékban lévő összes keresztillesztés után.

Tekintse meg a következő lekérdezést:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Ehhez a lekérdezéshez az index minden olyan dokumentumnak megfelel, amely "anyatej-helyettesítő tápszer" nevű címkével rendelkezik. Ez egy 0 és 10 közötti értékű tápanyagelem, és egy 1-nél nagyobb értékű adag. A JOIN kifejezés itt fogja végrehajtani a kereszt-termék minden elem a címkék, tápanyagok, és adag tömbök minden megfelelő dokumentumot, mielőtt bármilyen szűrőt alkalmaznak. 

A WHERE záradék ezután minden <c, t, n, s> a tuple-ra alkalmazza a szűrőállítmányt. Ha például egy egyező dokumentumban a három tömb mindegyikében 10 elem szerepel, akkor 1 x 10 x 10 x 10 (azaz 1000) értékre bővül. Az itt lévő segédlekérdezések használata segíthet az illesztett tömbelemek kiszűrésében, mielőtt csatlakozna a következő kifejezéshez.

Ez a lekérdezés egyenértékű az előzővel, de allekérdezéseket használ:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Tegyük fel, hogy a címkék tömbjében csak egy elem felel meg a szűrőnek, és öt elem van mind a tápanyagok, mind a adagtömbök számára. A JOIN kifejezések ezután 1 x 1 x 5 x 5 = 25 elemre bővülnek, szemben az első lekérdezés 1000 elemével.

## <a name="evaluate-once-and-reference-many-times"></a>Értékelje ki egyszer, és hivatkozzon többször

A segédlekérdezések segítségével optimalizálhatja a lekérdezéseket drága kifejezésekkel, például a felhasználó által definiált függvényekkel (UDF), összetett karakterláncokkal vagy aritmetikai kifejezésekkel. A JOIN kifejezéssel együtt segédlekérdezéssel egyszer kiértékelhető, de többször is hivatkozhatunk rá.

A következő lekérdezés kétszer `GetMaxNutritionValue` futtatja az UDF-et:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Az udf-et csak egyszer futtató egyenértékű lekérdezés:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Ne feledje a JOIN-kifejezések termékközi viselkedését. Ha az UDF-kifejezés kiértékelhető, győződjön meg arról, hogy a JOIN kifejezés mindig egyetlen sort hoz létre úgy, hogy közvetlenül az érték helyett az allekérdezésből ad vissza egy objektumot.
>

Íme egy hasonló példa, amely egy objektumot ad vissza érték helyett:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

A megközelítés nem korlátozódik az UDF-ekre. Minden potenciálisan drága kifejezésre vonatkozik. A matematikai függvényrel például ugyanezt `avg`a megközelítést használhatja:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Illesztés utánzása külső referenciaadatokkal

Előfordulhat, hogy gyakran olyan statikus adatokra kell hivatkoznia, amelyek ritkán változnak, például mértékegységekre vagy országkódokra. Jobb, ha nem kettőzik ezeket az adatokat az egyes dokumentumokhoz. A párhuzamosságok elkerülése a tárolón tárolható, és a dokumentum méretének kisebb tartásával javíthatja az írási teljesítményt. Segédlekérdezéssel referenciaadatok gyűjteményével utánozhatja a belső illesztés szemantikáját.

Vegyük például a referenciaadatok ezen halmazát:

| **Egység** | **Név**            | **Szorzó** | **Alapegység** |
| -------- | ------------------- | -------------- | ------------- |
| Ng       | Nanogramm            | 1.00E-09       | Gramm          |
| µg       | Mikrogramm           | 1.00E-06       | Gramm          |
| Mg       | Milligramm           | 1.00E-03       | Gramm          |
| g        | Gramm                | 1.00E+00       | Gramm          |
| Kg       | Kilogramm            | 1.00E+03       | Gramm          |
| Mg       | Megagram            | 1.00E+06       | Gramm          |
| Gg       | Gigagramm            | 1.00E+09       | Gramm          |
| Nj       | Nanojoule között           | 1.00E-09       | Joule között         |
| μJ       | Mikrojoule          | 1.00E-06       | Joule között         |
| Mj       | Millijoule között          | 1.00E-03       | Joule között         |
| J        | Joule között               | 1.00E+00       | Joule között         |
| Kj       | Kilojoule között           | 1.00E+03       | Joule között         |
| Mj       | Megajoule között           | 1.00E+06       | Joule között         |
| Gj       | Gigajoule között           | 1.00E+09       | Joule között         |
| Cal      | Kalória             | 1.00E+00       | Kalória       |
| Kcal     | Kalória             | 1.00E+03       | Kalória       |
| Ne       | Nemzetközi egységek |                |               |


A következő lekérdezés utánozza az adatokhoz való csatlakozást, így az egység nevét hozzáadja a kimenethez:

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

## <a name="scalar-subqueries"></a>Skaláris allekérdezések

A skaláris segédlekérdezési kifejezés olyan segédlekérdezés, amely egyetlen értéket ad eredményül. A skaláris segédlekérdezési kifejezés értéke a segédlekérdezés vetületének (SELECT záradék) értéke.  Skaláris segédlekérdezési kifejezést számos olyan helyen használhat, ahol a skaláris kifejezés érvényes. Használhat például skaláris segédlekérdezést a SELECT és a WHERE záradék bármely kifejezésében.

A skaláris segédlekérdezés használata azonban nem mindig segít az optimalizálásban. Ha például egy skaláris segédlekérdezést argumentumként ad át egy rendszernek vagy a felhasználó által definiált függvényeknek, az nem biztosít előnyt az erőforrásegység (RU) felhasználásában vagy késésében.

A skaláris allekérdezések tovább sorolhatók:
* Egyszerű kifejezésű skaláris allekérdezések
* Skaláris allekérdezések összesítése

## <a name="simple-expression-scalar-subqueries"></a>Egyszerű kifejezésű skaláris allekérdezések

Az egyszerű kifejezésű skaláris segédlekérdezés olyan korrelált segédlekérdezés, amelynek SELECT záradéka nem tartalmaz összesítő kifejezéseket. Ezek az allekérdezések nem biztosítanak optimalizálási előnyöket, mivel a fordító egyetlen nagyobb egyszerű kifejezéssé alakítja őket. Nincs korrelált környezet a belső és a külső lekérdezések között.

Íme néhány példa:

**1. példa**

```sql
SELECT 1 AS a, 2 AS b
```

Ezt a lekérdezést egyszerű kifejezésű skaláris segédlekérdezéssel átírhatja a következő helyre:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Mindkét lekérdezés ezt a kimenetet eredményezi:

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

Ezt a lekérdezést egyszerű kifejezésű skaláris segédlekérdezéssel átírhatja a következő helyre:

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

Ezt a lekérdezést egyszerű kifejezésű skaláris segédlekérdezéssel átírhatja a következő helyre:

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

### <a name="aggregate-scalar-subqueries"></a>Skaláris allekérdezések összesítése

Az összesített skaláris segédlekérdezés olyan segédlekérdezés, amelynek vetületében vagy szűrőjében olyan összesítő függvény szerepel, amely egyetlen értéket ad eredményül.

**1. példa:**

Az alábbiakban egyetlen összesítő függvénykifejezést mutató segédlekérdezést olvashat a vetületében:

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

Az alábbiakban több összesítő függvénykifejezést tartalmazó segédlekérdezést olvashat:

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

Az alábbiakban összesítő segédlekérdezést is bekövetkező lekérdezést olvashat a vetületben és a szűrőben:

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

A lekérdezés írásának optimálisabb módja, ha csatlakozik a segédlekérdezéshez, és a SEGÉDaliasra hivatkozik mind a SELECT, mind a WHERE záradékban. Ez a lekérdezés hatékonyabb, mivel a segédlekérdezést csak az illesztési utasításon belül kell végrehajtani, a vetületben és a szűrőben nem.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>EXISTS kifejezés

Az Azure Cosmos DB támogatja az EXISTS kifejezéseket. Ez egy az Azure Cosmos DB SQL API-ba épített összesített skaláris segédlekérdezés. Az EXISTS egy logikai kifejezés, amely szubquery-kifejezést vesz fel, és igaz értéket ad vissza, ha a segédlekérdezés sorokat ad vissza. Ellenkező esetben hamis értéket ad vissza.

Mivel az Azure Cosmos DB SQL API nem tesz különbséget a logikai kifejezések és bármely más skaláris kifejezések között, a SELECT és a WHERE záradékok ban is használhatja az EXISTS parancsot. Ez nem olyan, mint a T-SQL, ahol a logikai kifejezés (például EXISTS, BETWEEN és IN) a szűrőre korlátozódik.

Ha az EXISTS segédlekérdezés egyetlen meg határozatlan értéket ad vissza, akkor az EXISTS értéke hamis. Vegyük például a következő lekérdezést, amely hamis eredményt ad ki:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Ha az előző segédlekérdezés VALUE kulcsa kimarad, a lekérdezés értéke igaz:
```sql
SELECT EXISTS (SELECT undefined) 
```

A segédlekérdezés egy objektum kijelölt listájának értéklistáját fogja betegyezíteni. Ha a kijelölt lista nem tartalmaz értékeket, a{}segédlekérdezés az "' ('' értéknek ad vissza . Ez az érték definiálva van, így az EXISTS értéke igaz.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Példa: ARRAY_CONTAINS és csatlakozás újraírása existsként

A ARRAY_CONTAINS gyakori felhasználási esete a dokumentum szűrése egy tömbben lévő elem létezése alapján. Ebben az esetben ellenőrizzük, hogy a címkék tömbje tartalmaz-e "narancssárga" nevű elemet.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Ugyanazt a lekérdezést átírhatja az EXISTS használatára:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Ezenkívül ARRAY_CONTAINS csak azt tudja ellenőrizni, hogy egy érték megegyezik-e a tömb bármely elemével. Ha összetettebb szűrőkre van szüksége a tömbtulajdonságokhoz, használja a JOIN parancsot.

Vegye figyelembe a következő lekérdezést, `nutritionValue` amely a tömb egységei és tulajdonságai alapján szűr: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

A gyűjtemény minden egyes dokumentuma esetében a kereszttermék a tömbelemekkel együtt történik. Ez a JOIN művelet lehetővé teszi a tömbön belüli tulajdonságok szűrését. Azonban ez a lekérdezés RU-felhasználás jelentős lesz. Ha például 1000 dokumentumnak 100 elem van minden tömbben, akkor 1000 x 100 (azaz 100 000) tuples lesz.

Az EXISTS használata segíthet elkerülni ezt a drága termékközi terméket:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

Ebben az esetben az EXISTS allekérdezéstömbelemeire szűr. Ha egy tömbelem megegyezik a szűrővel, akkor kivetíti, és a EXISTS értéke igaz.

Azt is alias létezik, és hivatkozhat rá a vetület:

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

## <a name="array-expression"></a>TÖMB kifejezés

A TÖMB kifejezéssel a lekérdezés eredményeit tömbként vetítheti ki. Ezt a kifejezést csak a lekérdezés SELECT záradékában használhatja.

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

Más allekérdezésekhez is a ARRAY kifejezéssel rendelkező szűrők is lehetségesek.

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

A tömbkifejezések az allekérdezésekben a FROM záradék után is következhetnek.

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

- [Az Azure Cosmos DB .NET-mintái](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dokumentumadatok modellezése](modeling-data.md)
