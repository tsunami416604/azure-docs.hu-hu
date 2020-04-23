---
title: Lekérdezési problémák elhárítása az Azure Cosmos DB használatakor
description: Ismerje meg, hogyan azonosíthatja, diagnosztizálhatja és háríthatja el az Azure Cosmos DB SQL-lekérdezési problémáit.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/20/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 4a8b61f3719a60af567d10f8839987e613babc9e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870459"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Lekérdezési problémák elhárítása az Azure Cosmos DB használatakor

Ez a cikk az Azure Cosmos DB-ben a lekérdezések hibaelhárításához ajánlott általános anamnézist ismerteti. Bár a cikkben ismertetett lépéseket nem érdemes teljes körűvédelemnek tekinteni a potenciális lekérdezési problémák kal szemben, itt a leggyakoribb teljesítménytippeket is beépítettük. Ezt a cikket kell használnia kiindulási helyként az Azure Cosmos DB core (SQL) API lassú vagy költséges lekérdezéseinek hibaelhárításához. Diagnosztikai naplók segítségével is [azonosíthatja](cosmosdb-monitor-resource-logs.md) a lassú vagy jelentős mennyiségű átviteli energiát használó lekérdezéseket.

Az Azure Cosmos DB-ben széles körben kategorizálhatja a lekérdezésoptimalizálást:

- A lekérdezés kérelemegység (RU) terhelését csökkentő optimalizálások
- Optimalizálás, amely csak csökkenti a késést

Ha csökkenti a ru-díj a lekérdezés, akkor szinte biztosan csökkenti a késést is.

Ez a cikk példákat, amelyek etetési [adatkészlet](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) használatával újra létrehozhat.

## <a name="common-sdk-issues"></a>Gyakori SDK-problémák

- A legjobb teljesítmény érdekében kövesse a [Teljesítményre vonatkozó tippeket.](performance-tips.md)
    > [!NOTE]
    > A jobb teljesítmény érdekében a Windows 64 bites gazdagép-feldolgozást javasoljuk. Az SQL SDK tartalmaz egy natív ServiceInterop.dll a lekérdezések helyi elemzéséhez és optimalizálásához. A ServiceInterop.dll csak a Windows x64 platformon támogatott. Linux és más nem támogatott platformok, ahol ServiceInterop.dll nem érhető el, egy további hálózati hívás történik az átjáróhoz az optimalizált lekérdezés lehívásához.
- Beállíthat egy `MaxItemCount` elemet a lekérdezésekhez, de nem adhat meg minimális cikkszámot.
    - A kódnak kezelnie kell minden `MaxItemCount`oldalméretet, nullától a ig.
    - Az oldalon lévő elemek száma mindig kisebb lesz `MaxItemCount`a megadottnál. Azonban `MaxItemCount` szigorúan a maximális, és nem lehet kevesebb eredményt, mint ez az összeg.
- Előfordulhat, hogy a lekérdezések üres oldalakkal rendelkeznek, még akkor is, ha egy jövőbeli oldalon eredmények jelennek meg. Ennek okai lehetnek:
    - Az SDK több hálózati hívást is kezdeményezhet.
    - A lekérdezés beolvasása hosszú időt vehet igénybe.
- Minden lekérdezés rendelkezik egy folytatási jogkivonat, amely lehetővé teszi a lekérdezés folytatását. Ügyeljen arra, hogy teljesen ürítse ki a lekérdezést. Tekintse meg az SDK-mintákat, és egy `while` ciklus használatával ürítse `FeedIterator.HasMoreResults` ki a teljes lekérdezést.

## <a name="get-query-metrics"></a>Lekérdezési mutatók begyűjtése

Ha optimalizálja a lekérdezést az Azure Cosmos DB,az első lépés mindig a lekérdezés metrikák a lekérdezési [metrikák.](profile-sql-api-query.md) Ezek a metrikák az Azure Portalon keresztül is elérhetők:

[![Lekérdezési mutatók](./media/troubleshoot-query-performance/obtain-query-metrics.png) beszerzése](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Miután lekérdezi a lekérdezési mutatókat, hasonlítsa össze a beolvasott bizonylatok számát a lekérdezés kimeneti bizonylatszámával. Ezzel az összehasonlítást a cikkben áttekintő megfelelő szakaszok azonosítására használhatja.

A beolvasott bizonylatok száma a lekérdezés betöltéséhez szükséges dokumentumok száma. A kimeneti bizonylatok száma a lekérdezés eredményéhez szükséges dokumentumok száma. Ha a beolvasott bizonylatok száma jelentősen magasabb, mint a kimeneti bizonylatok száma, a lekérdezésnek legalább egy olyan része nem tudta használni az indexet, és vizsgálatra volt szükség.

Tekintse meg a következő szakaszokat a forgatókönyv megfelelő lekérdezésoptimalizálásának megértéséhez.

### <a name="querys-ru-charge-is-too-high"></a>A lekérdezés RU-díja túl magas

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>A beolvasott bizonylatok száma jelentősen magasabb, mint a kimeneti bizonylatok száma

- [A szükséges elérési utak felvétele az indexelési házirendbe.](#include-necessary-paths-in-the-indexing-policy)

- [Ismerje meg, hogy mely rendszerfüggvények használják az indexet.](#understand-which-system-functions-use-the-index)

- [Ismerje meg, hogy mely összesített lekérdezések használják az indexet.](#understand-which-aggregate-queries-use-the-index)

- [Módosítsa azokat a lekérdezéseket, amelyek szűrővel és ORDER BY záradékkal is rendelkeznek.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

- [JOIN-kifejezések optimalizálása segédlekérdezéssel.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>A beolvasott bizonylatok száma megközelítőleg megegyezik a kimeneti bizonylatszámmal

- [Kerülje a partíciók közötti lekérdezéseket.](#avoid-cross-partition-queries)

- [Optimalizálja azolyan lekérdezéseket, amelyek több tulajdonságra vonatkozó szűrőket tartalmaznak.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Módosítsa azokat a lekérdezéseket, amelyek szűrővel és ORDER BY záradékkal is rendelkeznek.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>A lekérdezés RU-díja elfogadható, de a késés még mindig túl magas

- [Javítja a közelséget.](#improve-proximity)

- [A kiosztott átviteli-átbocsátás növelése.](#increase-provisioned-throughput)

- [Növelje a MaxConcurrency-t.](#increase-maxconcurrency)

- [Növelje a MaxBufferedItemCount értéket.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Azokat a lekérdezéseket, amelyekben a beolvasott bizonylatok száma meghaladja a kimeneti bizonylatok számát

 A beolvasott bizonylatok száma a lekérdezés betöltéséhez szükséges dokumentumok száma. A kimeneti bizonylatok száma a lekérdezés eredményéhez szükséges dokumentumok száma. Ha a beolvasott bizonylatok száma jelentősen magasabb, mint a kimeneti bizonylatok száma, a lekérdezésnek legalább egy olyan része nem tudta használni az indexet, és vizsgálatra volt szükség.

Íme egy példa a bekövetkező lekérdezésre, amelyet nem szolgált ki teljes egészében az index:

Lekérdezés:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Lekérdezési mutatók:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

A beolvasott bizonylatok száma (60 951) jelentősen magasabb, mint a kimeneti bizonylatok száma (7), ezért ez a lekérdezés a vizsgálathoz szükséges. Ebben az esetben a rendszer funkció [NAGYBETŰ()](sql-query-upper.md) nem használja az indexet.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>A szükséges elérési utak felvétele az indexelési házirendbe

Az indexelési házirendnek `WHERE` ki kell `ORDER BY` terjednie `JOIN`a záradékokban, záradékokben és a legtöbb rendszerfüggvényben található tulajdonságokra. Az indexházirendben megadott elérési útnak meg kell egyeznie (a kis- és nagybetűk megkülönböztetése) a JSON-dokumentumoktulajdonságának.

Ha egy egyszerű lekérdezést futtat a [tápérték-adathalmazon,](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) sokkal `WHERE` alacsonyabb VT-díjat figyel meg, amikor a záradékban szereplő tulajdonság indexelésre kerül:

#### <a name="original"></a>Eredeti

Lekérdezés:

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

Indexelési házirend:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Ru díj:** 409,51 VT

#### <a name="optimized"></a>Optimalizált

Frissített indexelési házirend:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Ru díj:** 2,98 VT

Az indexelési házirendhez bármikor hozzáadhat tulajdonságokat, az írás elérhetőségére vagy teljesítményére nincs hatással. Ha új tulajdonságot ad hozzá az indexhez, a tulajdonságot használó lekérdezések azonnal az új elérhető indexet fogják használni. A lekérdezés az új indexet fogja használni, amíg épül. Így a lekérdezési eredmények lehet inkonzisztens, míg az index újraépítése folyamatban van. Ha egy új tulajdonság indexelt, a csak a meglévő indexeket használó lekérdezéseket nem érinti az index újraépítése. Nyomon [követheti az indextranszformáció folyamatát.](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)

### <a name="understand-which-system-functions-use-the-index"></a>Annak ismertetése, hogy mely rendszerfüggvények használják az indexet

Ha egy kifejezés karakterlánc-értékek tartományába fordítható, használhatja az indexet. Különben nem.

Íme néhány gyakori karakterlánc-függvény, amely használhatja az indexet:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, de csak akkor, ha az első num_expr 0

Az alábbiakban felsodkán felsoghat néhány gyakori rendszerfüggvény, amelyek nem használják az indexet, és minden dokumentumot be kell tölteniük:

| **Rendszerfüggvény**                     | **Ötletek optimalizáláshoz**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Használja az Azure Search teljes szöveges keresés.                        |
| FELSŐ/ALSÓ                             | Ahelyett, hogy a rendszerfunkciót használná az adatok összehasonlításhoz való normalizálására, a behelyezéskor normalizálja a burkolatot. A lekérdezés, mint ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` lesz ```SELECT * FROM c WHERE c.name = 'BOB'```. |
| Matematikai függvények (nem aggregátumok) | Ha gyakran kell kiszámítania egy értéket a lekérdezésben, fontolja meg az érték tulajdonságként való tárolását a JSON-dokumentumban. |

------

A lekérdezés más részei továbbra is használhatják az indexet, még akkor is, ha a rendszerfüggvények nem.

### <a name="understand-which-aggregate-queries-use-the-index"></a>Annak ismertetése, hogy mely összesítő lekérdezések használják az indexet

A legtöbb esetben az Azure Cosmos DB összesített rendszerfüggvények az indexet fogja használni. Az összesítő lekérdezés szűrőitől vagy további záradékaitól függően azonban előfordulhat, hogy a lekérdezési motorra nagy számú dokumentum betöltéséhez van szükség. A lekérdezési motor általában először egyenlőségi és tartományszűrőket alkalmaz. A szűrők alkalmazása után a lekérdezési motor kiértékelheti a további szűrőket, és szükség esetén a fennmaradó dokumentumok betöltéséhez folyamodhat az összesítés kiszámításához.

Ha például ez a két mintalekérdezés van, `CONTAINS` az egyenlőségi és rendszerfüggvény-szűrővel `CONTAINS` rendelkező lekérdezés általában hatékonyabb lesz, mint egy rendszerfüggvény-szűrővel rendelkező lekérdezés. Ennek az az oka, hogy először az egyenlőségszűrőt alkalmazza a `CONTAINS` rendszer, és az indexet használja, mielőtt a drágább szűrőhöz dokumentumokat kellene betölteni.

Lekérdezés csak `CONTAINS` szűrővel - magasabb RU-töltés:

```sql
SELECT COUNT(1) FROM c WHERE CONTAINS(c.description, "spinach")
```

Lekérdezés egyenlőségi szűrővel `CONTAINS` és szűrővel - alacsonyabb RU-töltés:

```sql
SELECT AVG(c._ts) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Az alábbiakban további példákat talál az olyan összesítő lekérdezésekre, amelyek nem használják teljes mértékben az indexet:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Az indexet nem használó rendszerfüggvényekkel rendelkező lekérdezések

A megfelelő [rendszerfüggvény oldalához kell látnia,](sql-query-system-functions.md) hogy az indexet használja-e.

```sql
SELECT MAX(c._ts) FROM c WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Lekérdezések összesítése felhasználó által definiált függvényekkel (UDF-ek)

```sql
SELECT AVG(c._ts) FROM c WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>LEKÉRDEZÉSEK GROUP BY-VEL

A RU-díj `GROUP BY` a `GROUP BY` záradékban szereplő tulajdonságok számosságának növekedésével nő. Ebben a példában a lekérdezési motornak `c.foodGroup = "Sausages and Luncheon Meats"` be kell töltenie minden dokumentumot, amely megfelel a szűrőnek, így a ru díj várhatóan magas lesz.

```sql
SELECT COUNT(1) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" GROUP BY c.description
```

Ha azt tervezi, hogy gyakran futtatja ugyanazokat az összesítő lekérdezéseket, hatékonyabb lehet egy valós idejű materializált nézet létrehozása az [Azure Cosmos DB módosítási hírcsatornával,](change-feed.md) mint az egyéni lekérdezések futtatása.

### <a name="modify-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Szűrővel és ORDER BY záradékkal is rendelkező lekérdezések módosítása

Bár a szűrővel és `ORDER BY` egy záradékkal rendelkező lekérdezések általában tartományindexet használnak, hatékonyabbak lesznek, ha összetett indexből is kiszolgálhatók. Az indexelési házirend módosítása mellett az összetett index összes `ORDER BY` tulajdonságát hozzá kell adni a záradékhoz. A lekérdezés ezen módosítása biztosítja, hogy az összetett indexet használja.  A hatás figyelheti meg, ha fut egy lekérdezést a [tápérték-adatkészleten:](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)

#### <a name="original"></a>Eredeti

Lekérdezés:

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

Indexelési házirend:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Ru díj:** 44,28 VT

#### <a name="optimized"></a>Optimalizált

Frissített lekérdezés (mindkét tulajdonságot tartalmazza a `ORDER BY` záradékban):

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Frissített indexelési házirend:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Ru díj:** 8,86 VT

### <a name="optimize-join-expressions-by-using-a-subquery"></a>JOIN-kifejezések optimalizálása segédlekérdezéssel
A többértékű segédlekérdezések optimalizálhatják `JOIN` a kifejezéseket úgy, hogy minden egyes többes `WHERE` kijelölésű kifejezés után lenyomják a predikátumokat, nem pedig a záradékban lévő összes keresztillesztés után.

Fontolja meg ezt a lekérdezést:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Ru díj:** 167,62 VT

Ehhez a lekérdezéshez az index minden olyan dokumentumnak megfelel, amelynek címkéje "anyatej-helyettesítő tápszer", nutritionValue nagyobb, mint 0, és 1-nél nagyobb adaggal rendelkezik. A `JOIN` kifejezés itt elvégzi a kereszt-termék minden elem a címkék, tápanyagok, és adag tömbök minden megfelelő dokumentumot, mielőtt bármilyen szűrőt alkalmaznak. A `WHERE` záradék ezután minden `<c, t, n, s>` egyes függőre alkalmazza a szűrőállítmányt.

Ha például egy egyező dokumentum a három tömb mindegyikében 10 elemet tartalmaz, akkor 1 x 10 x 10 x 10 (azaz 1000) értékre bővül. Az itt használt segédlekérdezések segíthetnek kiszűrni az illesztett tömbelemeket, mielőtt csatlakoznának a következő kifejezéshez.

Ez a lekérdezés egyenértékű az előzővel, de allekérdezéseket használ:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Ru díj:** 22,17 VT

Tegyük fel, hogy a címkék tömbjében csak egy elem felel meg a szűrőnek, és hogy a tápanyagok és a kiszolgálótömbök öt elemből állnak. A `JOIN` kifejezések 1 x 1 x 5 x 5 = 25 elemre bővülnek, szemben az első lekérdezés 1000 elemével.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Azolyan lekérdezések, amelyekben a beolvasott bizonylatok száma megegyezik a kimeneti bizonylatszámmal

Ha a beolvasott bizonylatok száma megközelítőleg megegyezik a kimeneti bizonylatszámmal, a lekérdezésnek nem kellett sok felesleges dokumentumot beolvasnia. Sok lekérdezés esetén, például a TOP kulcsszót használó lekérdezések esetében a beolvasott dokumentumok száma 1-el meghaladhatja a kimeneti dokumentumok számát. Nem kell aggódnod emiatt.

### <a name="avoid-cross-partition-queries"></a>A partíciók közötti lekérdezések elkerülése

Az Azure Cosmos DB [particionálást](partitioning-overview.md) használ az egyes tárolók méretezéséhez a kérelemegység és az adattárolási igények növelése érdekében. Minden fizikai partíció külön és független indexszel rendelkezik. Ha a lekérdezés rendelkezik egy egyenlőségszűrő, amely megfelel a tároló partíciókulcs, akkor ellenőriznie kell csak a megfelelő partíció indexét. Ez az optimalizálás csökkenti a lekérdezés által igényelt felhasználói műveletek teljes számát.

Ha nagy számú kiépített ru-k (több mint 30 000) vagy nagy mennyiségű tárolt adatok (több mint körülbelül 100 GB), akkor valószínűleg egy elég nagy tároló takarás-terhelések jelentős csökkenéséhez.

Ha például létrehoz egy tárolót a partíciós kulcs foodGroup, a következő lekérdezések kell ellenőriznie csak egy fizikai partíció:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Ezeket a lekérdezéseket a partíciókulcs hozzáadása is optimalizálná a lekérdezésben:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Azoknak a lekérdezéseknek, amelyek tartományszűrőkkel rendelkeznek a partíciókulcson, vagy amelyek nem rendelkeznek szűrőkkel a partíciókulcson, minden fizikai partíció indexét ellenőrizniük kell az eredményekért:

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Több tulajdonságra szűrőt tartalmazó lekérdezések optimalizálása

Bár a több tulajdonságra szűrővel rendelkező lekérdezések általában tartományindexet használnak, hatékonyabbak lesznek, ha összetett indexből is kiszolgálhatók. Kis mennyiségű adat esetén ez az optimalizálás nem lesz jelentős hatással. Ez azonban nagy mennyiségű adat esetében hasznos lehet. Összetett indexenként legfeljebb egy nem egyenlőségi szűrőt optimalizálhat. Ha a lekérdezés több nem egyenlőségi szűrővel rendelkezik, válasszon egyet, amely az összetett indexet fogja használni. A többi továbbra is tartományindexeket fog használni. A nem egyenlőségszűrőt utoljára kell definiálni az összetett indexben. [További információ az összetett indexekről](index-policy.md#composite-indexes).

Íme néhány példa olyan lekérdezésekre, amelyek összetett indexszel optimalizálhatók:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Itt a megfelelő összetett index:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>A lekérdezés késését csökkentő optimalizálások

Sok esetben a RU-díj elfogadható lehet, ha a lekérdezési késés még mindig túl magas. A következő szakaszok áttekintést nyújtanak a lekérdezési késés csökkentésére vonatkozó tippekről. Ha ugyanazt a lekérdezést többször futtatja ugyanazon az adatkészleten, akkor minden alkalommal ugyanaz a RU-díj lesz. A lekérdezéskés késése azonban lekérdezés-végrehajtások között változhat.

### <a name="improve-proximity"></a>A közelség javítása

Lekérdezések, amelyek az Azure Cosmos DB-fióktól eltérő régióból futnak, nagyobb késéssel rendelkeznek, mint ha ugyanabban a régióban futnának. Ha például az asztali számítógépen futtat kódot, akkor a késés tízvagy több száz ezredmásodperccel magasabb (vagy több) lesz, mint ha a lekérdezés ugyanabból az Azure-régióból származik, mint az Azure Cosmos DB. Az [Azure Cosmos DB-ben egyszerűen terjesztheti az adatokat,](distribute-data-globally.md) így biztosíthatja, hogy az adatok közelebb kerüljenek az alkalmazáshoz.

### <a name="increase-provisioned-throughput"></a>A kiosztott átviteli-átbocsátás növelése

Az Azure Cosmos DB-ben a kiosztott átviteli igény a kérelemegységekben (RUs) mérik. Képzelje el, hogy olyan lekérdezést használ fel, amely 5 átviteli rus-t használ fel. Ha például 1000 több mint egy több mint egy e-kódot létesít, a lekérdezést másodpercenként 200-szor futtathatja. Ha megpróbálta futtatni a lekérdezést, ha nem volt elegendő átviteli kapcsolat áll rendelkezésre, az Azure Cosmos DB http 429-es hibát ad vissza. Az aktuális Core (SQL) API SDK-k bármelyike rövid várakozás után automatikusan újrapróbálkozik ezzel a lekérdezéssel. A szabályozott kérelmek hosszabb időt vesz nek igénybe, így a kiosztott átviteli teljesítmény növelése javíthatja a lekérdezés késését. Megfigyelheti a [szabályozott kérelmek teljes számát](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) az Azure Portal **Metrika** paneljén.

### <a name="increase-maxconcurrency"></a>MaxConcurrency növelése

A párhuzamos lekérdezések több partíció párhuzamos lekérdezésével működnek. De az egyes particionált gyűjteményből származó adatok at a lekérdezés hez képest sorozatosan lekéri. Tehát, ha a MaxConcurrency a partíciók száma, akkor a legjobb esélye a legeredményesebb lekérdezés, feltéve, hogy az összes többi rendszer feltételek változatlanok maradnak. Ha nem ismeri a partíciók számát, beállíthatja a MaxDegreesConcurrency (vagy MaxDegreesOfParallelism régebbi SDK-verziók) magas számra. A rendszer a minimális (partíciók száma, felhasználó által megadott bemenet) a maximális fokú párhuzamosság.

### <a name="increase-maxbuffereditemcount"></a>MaxBufferedItemCount növelése

A lekérdezések úgy vannak kialakítva, hogy az eredmények et előre lehívva, miközben az aktuális eredményköteget az ügyfél dolgozza fel. Az előzetes lekérés segít a lekérdezés általános késésének javításában. A MaxBufferedItemCount beállítás korlátozza az előre lekért eredmények számát. Ha ezt az értéket a visszaadott eredmények várható számára (vagy nagyobb számra) állítja be, a lekérdezés a legtöbb hasznot hozhatja az előzetes lekérésből. Ha ezt az értéket -1-re állítja, a rendszer automatikusan meghatározza a pufferelendő elemek számát.

## <a name="next-steps"></a>További lépések
Az alábbi cikkekben további információt talál a rról, hogyan mérheti a kérdéscsoportokat lekérdezésenként, hogyan kaphat végrehajtási statisztikákat a lekérdezések finomhangolásához stb.:

* [SQL-lekérdezés-végrehajtási metrikák begyűjtése a .NET SDK használatával](profile-sql-api-query.md)
* [A lekérdezési teljesítmény finomhangolása az Azure Cosmos DB-vel](sql-api-sql-query-metrics.md)
* [Teljesítménnyel kapcsolatos tippek .NET SDK-hoz](performance-tips.md)
