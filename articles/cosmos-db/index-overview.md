---
title: Indexelés az Azure Cosmos DB-ben
description: Ismerje meg, hogy az indexelés hogyan működik a Azure Cosmos DBban, különböző típusú indexek, például a tartomány, a térbeli és az összetett indexek támogatottak.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: thweiss
ms.openlocfilehash: 921a11d8846c868436365fe400852eac0f7dcd3e
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83712094"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexelés az Azure Cosmos DB-ben – Áttekintés

Azure Cosmos DB egy séma-agnosztikus adatbázis, amely lehetővé teszi az alkalmazáson belüli iterációt anélkül, hogy a sémát vagy az indexelést kellene foglalkoznia. Alapértelmezés szerint a Azure Cosmos DB automatikusan indexel minden tulajdonságot a [tároló](databases-containers-items.md#azure-cosmos-containers) összes eleméhez anélkül, hogy sémát kellene meghatároznia vagy másodlagos indexeket kellene konfigurálnia.

A cikk célja annak ismertetése, hogy az Azure Cosmos DB hogyan indexeli az adatokat, illetve hogy miként használja az indexeket a lekérdezési teljesítmény javításához. Azt javasoljuk, hogy az [indexelési szabályzatok](index-policy.md)testreszabásának megismerése előtt folytassa ezt a szakaszt.

## <a name="from-items-to-trees"></a>Elemektől a fákig

Minden alkalommal, amikor egy elem egy tárolóban tárolódik, a tartalma JSON-dokumentumként van kialakítva, majd egy faszerkezetbe konvertálva. Ez azt jelenti, hogy az adott elem minden tulajdonsága egy fa csomópontjaiként lesz megjelenítve. A rendszer egy pszeudo-főcsomópontot hoz létre szülőként az elem összes első szintű tulajdonságához. A levél csomópontjai tartalmazzák az elemek tényleges skaláris értékeit.

Példaként tekintse meg ezt az tételt:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

A következő fa fogja képviselni:

![Az előző elem faszerkezetként van ábrázolva](./media/index-overview/item-as-tree.png)

Vegye figyelembe, hogy a tömbök a fában vannak kódolva: a tömb minden bejegyzése egy köztes csomópontot kap, amely a tömbben (0, 1 stb.) lévő bejegyzés indexével van megjelölve.

## <a name="from-trees-to-property-paths"></a>A fák és a tulajdonságok közötti útvonalakig

Ennek az az oka, hogy Azure Cosmos DB átalakítja az elemeket a fákba, mivel lehetővé teszi a tulajdonságok hivatkozását a fák elérési útjain belül. Ha egy tulajdonság elérési útját szeretné lekérni, a fában áthaladhat a gyökér csomópontból a tulajdonságba, és összefűzheti az egyes bejárt csomópontok feliratait.

Itt láthatók a fent ismertetett példában szereplő egyes tulajdonságok elérési útjai:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Egy elem írásakor Azure Cosmos DB hatékonyan indexeli az egyes tulajdonságok elérési útját és a hozzá tartozó értéket.

## <a name="index-kinds"></a>Index típusa

Azure Cosmos DB jelenleg három típusú indexet támogat.

### <a name="range-index"></a>Tartomány indexe

A **Range** index egy megrendelt fastruktúrán alapul. A tartomány indexének típusa a következő:

- Esélyegyenlőségi lekérdezések:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Egyenlőségi egyezés egy tömb elemnél
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- Tartomány lekérdezései:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (a,,,, `>` `<` `>=` `<=` `!=` )

- Tulajdonság meglétének ellenőrzése:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Karakterláncrendszer-függvények:

   ```sql
   SELECT * FROM c WHERE CONTAINS(c.property, "value")
   ```

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY`lekérdezések

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`lekérdezések

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

A tartomány-indexek a skaláris értékeken (String vagy Number) is használhatók.

### <a name="spatial-index"></a>Térbeli index

A **térbeli** indexek hatékony lekérdezéseket tesznek lehetővé térinformatikai objektumokon, például pontokon, vonalakon, sokszögeken és több sokszögen. Ezek a lekérdezések ST_DISTANCE, ST_WITHIN, ST_INTERSECTS kulcsszavakat használnak. A következő példák a térbeli index típusát használják:

- Földrajzi távolsági lekérdezések:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Térinformatika a lekérdezéseken belül:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Térinformatikai metsző lekérdezések:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

A térbeli indexek megfelelően formázott [GeoJSON](geospatial.md) -objektumokon is használhatók. A pontok, Linestring, sokszögek és többsokszögek jelenleg támogatottak.

### <a name="composite-indexes"></a>Összetett indexek

Az **összetett** indexek nagyobb hatékonyságot biztosítanak, ha több mezőn végez műveleteket. Az összetett index típusa a következő:

- `ORDER BY`több tulajdonság lekérdezései:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Lekérdezések szűrővel és `ORDER BY` . Ezek a lekérdezések összetett indexet használhatnak, ha a Filter tulajdonságot hozzáadja a `ORDER BY` záradékhoz.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Lekérdezés két vagy több tulajdonsággal rendelkező szűrővel, ahol legalább egy tulajdonság egy egyenlőségi szűrő.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Mindaddig, amíg az egyik szűrési predikátum az egyik indexet használja, a lekérdezési motor kiértékeli, hogy először a REST ellenőrzése előtt. Ha például SQL-lekérdezéssel rendelkezik, például:`SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* A fenti lekérdezés először szűrni fogja azokat a bejegyzéseket, ahol a firstName = "Andrew" kifejezést használja az index használatával. Ezután továbbítja az összes firstName = "Andrew" bejegyzést egy későbbi folyamaton keresztül, hogy kiértékelje a tartalmazza a szűrő predikátumát.

* Felgyorsíthatja a lekérdezéseket, és elkerülheti a tárolók teljes vizsgálatát, ha olyan függvényeket használ, amelyek nem használják az indexet (például tartalmazza) az indexet használó további szűrési predikátumok hozzáadásával. A Filter záradékok sorrendje nem fontos. A lekérdezési motor kideríti, hogy mely predikátumok szelektívek, és ennek megfelelően futtatják a lekérdezést.


## <a name="querying-with-indexes"></a>Lekérdezés indexekkel

Az adatindexelés során kinyert elérési utak megkönnyítik az index keresését egy lekérdezés feldolgozásakor. Az `WHERE` indexelt elérési utak listáját tartalmazó lekérdezés záradékának egyeztetésével nagyon gyorsan azonosíthatja a lekérdezési predikátumnak megfelelő elemeket.

Vegyük például a következő lekérdezést: `SELECT location FROM location IN company.locations WHERE location.country = 'France'` . A lekérdezési predikátum (elemek szűrése, ahol bármely hely "Franciaország", mint országa) az alábbi piros színnel jelölt elérési útra hasonlít:

![Megadott elérési út megfeleltetése egy fában belül](./media/index-overview/matching-path.png)

> [!NOTE]
> Egy olyan `ORDER BY` záradék, amelyet egy adott tulajdonság megrendelése *mindig* egy tartomány indexre van szüksége, és sikertelen lesz, ha az általa hivatkozott elérési út nem rendelkezik ilyennel. Hasonlóképpen, a `ORDER BY` több tulajdonság által megrendelést igénylő lekérdezésnek *mindig* összetett indexre van szüksége.

## <a name="next-steps"></a>Következő lépések

Az indexeléssel kapcsolatos további információkért olvassa el a következő cikkeket:

- [Indexelési házirend](index-policy.md)
- [Az indexelési szabályzat kezelése](how-to-manage-indexing-policy.md)
