---
title: Lekérdezési problémák elhárítása Azure Cosmos DB használatakor
description: Ismerje meg, hogyan azonosíthatja, diagnosztizálhatja és elháríthatja a Azure Cosmos DB SQL-lekérdezéssel kapcsolatos problémákat.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 01/14/2020
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: f016902f6cf7e0238dadb97d816f4590caec112e
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109344"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Lekérdezési problémák elhárítása Azure Cosmos DB használatakor

Ez a cikk részletesen ismerteti a Azure Cosmos DB-lekérdezések hibaelhárításának általános javasolt megközelítését. Az ebben a dokumentumban ismertetett lépések nem tekinthetők a lehetséges lekérdezési problémákra vonatkozó "catch all" utasításnak, ezért a leggyakoribb teljesítménnyel kapcsolatos tippek is elérhetők. Ezt a dokumentumot kiindulópontként kell használni a Azure Cosmos DB Core (SQL) API-ban lévő lassú vagy költséges lekérdezések hibaelhárításához. [Diagnosztikai naplókat](cosmosdb-monitor-resource-logs.md) is használhat a lassú vagy jelentős mennyiségű átviteli sebességű lekérdezések azonosítására.

A lekérdezési optimalizálásokat széles körben kategorizálhatja Azure Cosmos DBban: optimalizálások, amelyek csökkentik a lekérdezési egység (RU) díját és optimalizálását, ami csak csökkenti a késést. Ha csökkenti egy lekérdezés RU-díját, szinte biztosan csökkenti a késést is.

Ez a dokumentum olyan példákat fog használni, amelyeket a [táplálkozási](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) adatkészlet használatával lehet újból létrehozni.

### <a name="obtaining-query-metrics"></a>Lekérdezési metrikák beszerzése:

A lekérdezés Azure Cosmos DBban való optimalizálásakor az első lépés mindig a lekérdezési [metrikák beszerzése](profile-sql-api-query.md) . Ezek a Azure Portalon keresztül is elérhetők, ahogy az alábbiakon is látható:

[![lekérdezési metrikák beszerzése](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

A lekérdezési mérőszámok beszerzése után hasonlítsa össze a beolvasott dokumentumok darabszámát a lekérdezés kimeneti dokumentumainak számával. Ezzel az összehasonlítással azonosíthatja a vonatkozó részeket az alábbi hivatkozáshoz.

A beolvasott dokumentumok száma érték a lekérdezéshez szükséges dokumentumok számát adja meg. A kimeneti dokumentumok száma érték a lekérdezés eredményeihez szükséges dokumentumok számát adja meg. Ha a beolvasott dokumentumok száma jelentősen meghaladja a kimeneti dokumentumok számát, akkor a lekérdezésnek legalább egy része nem tudta kihasználni az indexet, és nem szükséges a vizsgálathoz.

Az alábbi szakasz az adott forgatókönyvhöz kapcsolódó lekérdezési optimalizálások megismeréséhez nyújt segítséget:

### <a name="querys-ru-charge-is-too-high"></a>A lekérdezés RU-díja túl magas

#### <a name="retrieved-document-count-is-significantly-greater-than-output-document-count"></a>A lekért dokumentumok száma lényegesen nagyobb, mint a kimeneti dokumentumok száma

- [A szükséges elérési utak belefoglalása az indexelési házirendbe](#include-necessary-paths-in-the-indexing-policy)

- [Az indexet használó rendszerfunkciók ismertetése](#understand-which-system-functions-utilize-the-index)

- [Lekérdezés szűrővel és ORDER BY záradékkal](#queries-with-both-a-filter-and-an-order-by-clause)

- [ILLESZTÉSi kifejezések optimalizálása segédlekérdezés használatával](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>A lekért dokumentumok száma körülbelül egyenlő a kimeneti dokumentumok számával

- [A több partíciós lekérdezések elkerülése](#avoid-cross-partition-queries)

- [Szűrők több tulajdonságon](#filters-on-multiple-properties)

- [Lekérdezés szűrővel és ORDER BY záradékkal](#queries-with-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>A lekérdezés RU-díja elfogadható, de a késés még mindig túl magas

- [A közelség javítása](#improve-proximity)

- [Kiosztott átviteli sebesség növelése](#increase-provisioned-throughput)

- [MaxConcurrency javítása](#increase-maxconcurrency)

- [MaxBufferedItemCount javítása](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Lekérdezések, amelyekben a lekérdezett dokumentumok száma meghaladja a kimeneti dokumentumok darabszámát

 A beolvasott dokumentumok száma érték a lekérdezéshez szükséges dokumentumok számát adja meg. A kimeneti dokumentumok száma érték a lekérdezés eredményeihez szükséges dokumentumok számát adja meg. Ha a beolvasott dokumentumok száma jelentősen meghaladja a kimeneti dokumentumok számát, akkor a lekérdezésnek legalább egy része nem tudta kihasználni az indexet, és nem szükséges a vizsgálathoz.

 Az alábbi példa olyan vizsgálati lekérdezést mutat be, amelyet az index nem teljes egészében szolgált.

Lekérdezés:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Lekérdezési metrikák:

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

A beolvasott dokumentumok száma (60 951) lényegesen nagyobb, mint a kimeneti dokumentumok száma (7), ezért ez a lekérdezés szükséges a vizsgálathoz. Ebben az esetben a [felső ()](sql-query-upper.md) rendszerfunkció nem használja az indexet.

## <a name="include-necessary-paths-in-the-indexing-policy"></a>A szükséges elérési utak belefoglalása az indexelési házirendbe

Az indexelési szabályzatnak tartalmaznia kell `WHERE` záradékok, `ORDER BY` záradékok, `JOIN`és a legtöbb rendszerfunkció összes tulajdonságát. Az index szabályzatban megadott elérési útnak meg kell egyeznie a JSON-dokumentumokban szereplő tulajdonsággal (kis-és nagybetűk megkülönböztetése).

Ha egy egyszerű lekérdezést futtatunk a [táplálkozási](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) adatkészleten, a `WHERE` záradékban található tulajdonság indexelése sokkal alacsonyabb költséget vesz figyelembe.

### <a name="original"></a>Eredeti

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

**Ru díj:** 409,51 ru

### <a name="optimized"></a>Optimalizált

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

**Ru díj:** 2,98 ru

Bármikor hozzáadhat további tulajdonságokat az indexelési szabályzathoz, és nem befolyásolhatja a rendelkezésre állást és a teljesítményt. Ha új tulajdonságot ad az indexhez, az ezt a tulajdonságot használó lekérdezések azonnal az új elérhető indexet fogják használni. A lekérdezés az új indexet fogja használni a kiépítés során. Ennek eredményeképpen a lekérdezési eredmények inkonzisztensek lehetnek, mert az index újraépítése folyamatban van. Ha egy új tulajdonság indexelve van, a rendszer csak a meglévő indexeket használó lekérdezéseket fogja érinteni az index újraépítése során. [Nyomon követheti az index átalakításának folyamatát](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

## <a name="understand-which-system-functions-utilize-the-index"></a>Az indexet használó rendszerfunkciók ismertetése

Ha a kifejezés lefordítható különböző sztringértékekre, akkor használhat indexet. Ellenkező esetben nem.

Íme az indexet használó sztringfüggvények listája:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0

Néhány olyan általános rendszerfunkció, amely nem használja az indexet, és az egyes dokumentumokat be kell töltenie az alábbiak szerint:

| **System függvény**                     | **Optimalizálási ötletek**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Teljes szöveges keresés Azure Search használata                        |
| FELSŐ/ALSÓ                             | Ahelyett, hogy a rendszerfüggvényt használja az összehasonlítások során az adatnormalizálás során, inkább normalizálja a burkolatot a Beszúrás után. Ezután egy lekérdezés, például ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` egyszerűen ```SELECT * FROM c WHERE c.name = 'BOB'``` |
| Matematikai függvények (nem összesítések) | Ha gyakran kell kiszámítani egy értéket a lekérdezésben, érdemes lehet ezt az értéket a JSON-dokumentumban található tulajdonságként tárolni. |

------

A lekérdezés más részei továbbra is használhatják az indexet, annak ellenére, hogy a rendszerfunkciók nem használják az indexet.

## <a name="queries-with-both-a-filter-and-an-order-by-clause"></a>Lekérdezés szűrővel és ORDER BY záradékkal

Egy szűrővel és egy `ORDER BY` záradékkal rendelkező lekérdezések általában a tartomány indexét fogják használni, ha egy összetett indexből tudnak kiszolgálni. Az indexelési házirend módosításán kívül az összes tulajdonságot fel kell vennie az összetett indexbe a `ORDER BY` záradékba. A lekérdezés módosítása biztosítja, hogy az a kompozit indexet használja.  A hatás megfigyeléséhez futtasson egy lekérdezést a [táplálkozási](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) adatkészleten.

### <a name="original"></a>Eredeti

Lekérdezés:

```sql
SELECT * FROM c WHERE c.foodGroup = “Soups, Sauces, and Gravies” ORDER BY c._ts ASC
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

**Ru díj:** 44,28 ru

### <a name="optimized"></a>Optimalizált

Frissített lekérdezés (a `ORDER BY` záradékban mindkét tulajdonságot tartalmazza):

```sql
SELECT * FROM c
WHERE c.foodGroup = “Soups, Sauces, and Gravies”
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

**Ru díj:** 8,86 ru

## <a name="optimize-join-expressions-by-using-a-subquery"></a>ILLESZTÉSi kifejezések optimalizálása segédlekérdezés használatával
A többértékű allekérdezések optimalizálhatja `JOIN` kifejezéseket úgy, hogy az összes Select-many kifejezés után lenyomja a predikátumokat, és nem a `WHERE` záradékban lévő összes kereszthivatkozást.

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

**Ru díj:** 167,62 ru

Ennél a lekérdezésnél az index minden olyan dokumentumhoz megfelel, amelynek a neve "Infant formula" nevű címkével rendelkezik, és a nutritionValue nagyobb, mint 1. A `JOIN` kifejezés itt fogja elvégezni a címkék, a tápanyagok és a minden egyező dokumentum összes elemének szorzatát a szűrők alkalmazása előtt. A `WHERE` záradék Ezután alkalmazza a szűrő predikátumot az egyes `<c, t, n, s>` rekordokra.

Ha például egy egyező dokumentum 10 elemet tartalmaz mindhárom tömbben, akkor az 1 x 10 x 10 x 10 (azaz 1 000) rekordok bővül. Az allekérdezések használatával a következő kifejezéssel való csatlakozás előtt segíthet a csatlakoztatott tömbök kiszűrésében.

Ez a lekérdezés megegyezik az előzővel, de allekérdezéseket használ:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Ru díj:** 22,17 ru

Tegyük fel, hogy a címkék tömbben csak egy elem felel meg a szűrőnek, és öt elem van a tápanyagok számára, és tömböket is kiszolgál. A `JOIN` kifejezések 1 x 1 x 5 x 5 = 25 elemre lesznek kiterjesztve, az első lekérdezésben szereplő 1 000-elemek helyett.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Lekérdezések, amelyekben a lekérdezett dokumentumok száma megegyezik a kimeneti dokumentumok számával

Ha a beolvasott dokumentumok száma nagyjából megegyezik a kimeneti dokumentumok számával, akkor a lekérdezésnek nem kellett sok felesleges dokumentumot beolvasnia. Számos lekérdezés, például a legfelső kulcsszót használó lekérdezések esetében a beolvasott dokumentumok száma meghaladhatja a kimeneti dokumentumok számát 1 értékkel. Ez nem okoz gondot.

## <a name="avoid-cross-partition-queries"></a>A több partíciós lekérdezések elkerülése

A Azure Cosmos DB [particionálás](partitioning-overview.md) használatával méretezi az egyes tárolókat a kérelmek egysége és az adattárolási igények növekedésével. Mindegyik fizikai partíció külön és független indexszel rendelkezik. Ha a lekérdezésben olyan esélyegyenlőségi szűrő szerepel, amely megfelel a tároló partíciós kulcsának, akkor csak a megfelelő partíció indexét kell ellenőriznie. Ez az optimalizálás csökkenti a lekérdezés által igényelt RU-k teljes számát.

Ha nagy számú kiépített RU-t (több mint 30 000) vagy nagy mennyiségű adat van tárolva (több mint ~ 100 GB), valószínűleg elég nagy tárolóval rendelkezik ahhoz, hogy jelentősen csökkentse a lekérdezés RU-díjait.

Ha például létrehozunk egy tárolót a partíciós kulcs foodGroup, a következő lekérdezéseknek csak egyetlen fizikai partíciót kell ellenőriznie:

```sql
SELECT * FROM c
WHERE c.foodGroup = “Soups, Sauces, and Gravies” and c.description = "Mushroom, oyster, raw"
```

Ezeket a lekérdezéseket a (z) a lekérdezésben szereplő partíciós kulccsal is optimalizálhatja:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Azok a lekérdezések, amelyek tartomány-szűrői vannak a partíciós kulcson, vagy nincsenek szűrők a partíciós kulcson, a "ventilátor" kiválasztására és a fizikai partíciók összes indexének az eredményekre való vizsgálatára van szükség.

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > “Soups, Sauces, and Gravies” and c.description = "Mushroom, oyster, raw"
```

## <a name="filters-on-multiple-properties"></a>Szűrők több tulajdonságon

Míg a szűrőket használó lekérdezések több tulajdonság esetében általában a tartomány indexét fogják használni, hatékonyabbak lesznek, ha összetett indexből is kiszolgálják őket. Kis mennyiségű adat esetében ez az optimalizálás nem lesz jelentős hatással. Nagy mennyiségű adattal azonban hasznos lehet. Az összetett indexek esetében csak egyetlen nem egyenlőségi szűrőt lehet optimalizálni. Ha a lekérdezés több nem egyenlőségi szűrővel rendelkezik, válasszon egyet azok közül, akik használni fogják az összetett indexet. A maradék továbbra is kihasználja a tartomány indexeit. A nem egyenlőségi szűrőt az összetett indexben kell megadni. [További információ az összetett indexekről](index-policy.md#composite-indexes)

Íme néhány példa az összetett indextel optimalizált lekérdezésekre:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Itt látható a kapcsolódó összetett index:

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

## <a name="optimizations-that-reduce-query-latency"></a>A lekérdezés késését csökkentő optimalizálások:

Sok esetben előfordulhat, hogy az RU-díj elfogadható, de a lekérdezés késése továbbra is túl magas. Az alábbi részekben áttekintheti a lekérdezések késését csökkentő tippeket. Ha ugyanazzal a lekérdezéssel többször is futtatja ugyanazt az adatkészletet, akkor minden alkalommal ugyanazzal az RU-díjjal fog rendelkezni. A lekérdezési késések azonban eltérőek lehetnek a lekérdezés végrehajtása során.

## <a name="improve-proximity"></a>A közelség javítása

A Azure Cosmos DB fióktól eltérő régióból futtatott lekérdezések nagyobb késéssel fognak rendelkezni, mint ha ugyanabban a régióban futnak. Ha például programkódot futtatott az asztali számítógépen, a késést várhatóan több tízezer (vagy több) ezredmásodpercnél nagyobb értékre kell állítani, mint ha a lekérdezés a Azure Cosmos DB azonos Azure-régióban található virtuális gépről származik. Az [adatAzure Cosmos DBek globális elosztása](distribute-data-globally.md) egyszerű, így biztosítható, hogy az adatai közelebb legyenek az alkalmazáshoz.

## <a name="increase-provisioned-throughput"></a>Kiosztott átviteli sebesség növelése

Azure Cosmos DB a kiosztott átviteli sebességet a kérelmek egységében (RU) mérjük. Tegyük fel, hogy rendelkezik egy olyan lekérdezéssel, amely 5 RU átviteli sebességet használ. Ha például 1 000 RU-t épít ki, akkor az adott lekérdezést a másodpercenként 200-szor futtathatja. Ha a lekérdezés futtatására tett kísérlet sikertelen volt, mert nem áll rendelkezésre elegendő átviteli sebesség, Azure Cosmos DB HTTP 429-hibát ad vissza. A jelenlegi Core (SQL) API SDK-k bármelyike automatikusan újrapróbálkozik a lekérdezéssel egy rövid időszak várakozása után. A szabályozott kérelmek hosszabb időt is igénybe vehetnek, így a kiépített átviteli sebesség növelhető a lekérdezés késésének növelésével. Megfigyelheti a [kérelmek teljes számát](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) a Azure Portal metrikák paneljén.

## <a name="increase-maxconcurrency"></a>MaxConcurrency javítása

A párhuzamos lekérdezések több partíció párhuzamos lekérdezésével működnek. Az egyedi particionált gyűjteményekből származó adatok azonban a lekérdezéssel kapcsolatos sorosan kerülnek beolvasásra. Így a MaxConcurrency és a partíciók számának beállításával a lehető legnagyobb eséllyel lehet elérni a legtöbb teljesítményű lekérdezést, ha az összes többi rendszerfeltétel változatlan marad. Ha nem ismeri a partíciók számát, beállíthatja a MaxConcurrency (vagy a MaxDegreesOfParallelism a régebbi SDK-verziókban) magas értékre, és a rendszer a lehető legkevesebb párhuzamosságot választja (a partíciók száma, a felhasználó által megadott bemenet).

## <a name="increase-maxbuffereditemcount"></a>MaxBufferedItemCount javítása

A lekérdezések az eredmények előzetes beolvasására lettek kialakítva, miközben az ügyfél az aktuális eredményt dolgozza fel. Az előzetes beolvasás a lekérdezés teljes késésének javulását segíti elő. A MaxBufferedItemCount beállítása korlátozza az előre beolvasott eredmények számát. Ha ezt az értéket a visszaadott várt számú eredmény (vagy egy magasabb szám) értékre állítja, a lekérdezés maximális előnyt kaphat az előzetes lekéréstől. Ha a-1 értékre állítja a beállítást, a rendszer automatikusan eldönti, hogy hány elemből kell puffert beállítani.

## <a name="next-steps"></a>Következő lépések
Az alábbi dokumentumokban megtekintheti, hogyan mérjük le a lekérdezéseket, és hogyan végezhet végrehajtási statisztikát a lekérdezések finomhangolásához, és így tovább:

* [SQL-lekérdezés végrehajtási metrikáinak lekérése a .NET SDK használatával](profile-sql-api-query.md)
* [A lekérdezési teljesítmény finomhangolása az Azure Cosmos DB-vel](sql-api-sql-query-metrics.md)
* [Teljesítménnyel kapcsolatos tippek .NET SDK-hoz](performance-tips.md)
