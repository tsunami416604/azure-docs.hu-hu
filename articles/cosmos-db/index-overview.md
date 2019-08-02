---
title: Indexelés Azure Cosmos DB
description: Ismerje meg, hogy az indexelés hogyan működik Azure Cosmos DBban.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: thweiss
ms.openlocfilehash: c8e21ea89f3e23709d636ab8af4716bff76d7217
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479291"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexelés Azure Cosmos DBban – áttekintés

Azure Cosmos DB egy séma-agnosztikus adatbázis, amely lehetővé teszi az alkalmazáson belüli iterációt anélkül, hogy a sémát vagy az indexelést kellene foglalkoznia. Alapértelmezés szerint a Azure Cosmos DB automatikusan indexel minden tulajdonságot a [tároló](databases-containers-items.md#azure-cosmos-containers) összes eleméhez anélkül, hogy sémát kellene meghatároznia vagy másodlagos indexeket kellene konfigurálnia.

Ennek a cikknek a célja annak ismertetése, hogyan Azure Cosmos DB indexeli az adatindexeket, és hogyan használja az indexeket a lekérdezési teljesítmény javítása érdekében. Azt javasoljuk, hogy az indexelési szabályzatok testreszabásának megismerése [](index-policy.md)előtt folytassa ezt a szakaszt.

## <a name="from-items-to-trees"></a>Elemektől a fákig

Minden alkalommal, amikor egy elem egy tárolóban tárolódik, a tartalma JSON-dokumentumként van kialakítva, majd egy faszerkezetbe konvertálva. Ez azt jelenti, hogy az adott elem minden tulajdonsága egy fa csomópontjaiként lesz megjelenítve. A rendszer egy pszeudo-főcsomópontot hoz létre szülőként az elem összes első szintű tulajdonságához. A levél csomópontjai tartalmazzák az elemek tényleges skaláris értékeit.

Példaként tekintse meg ezt az tételt:

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

Azure Cosmos DB jelenleg három típusú indexet támogat:

A **tartomány** indexének típusa a következő:

- Esélyegyenlőségi lekérdezések:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
    ```

- Tartomány lekérdezései:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ``` 
  `>`(a `<` ,`<=`,,, )`!=` `>=`

- `ORDER BY`lekérdezések

   ```sql 
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`lekérdezések

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

A tartomány-indexek a skaláris értékeken (String vagy Number) is használhatók.

A **térbeli** index típusát a következőhöz használhatja:

- Földrajzi távolsági lekérdezések: 

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Térinformatika a lekérdezéseken belül: 

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

A térbeli indexek megfelelően formázott [GeoJSON](geospatial.md) -objektumokon is használhatók. A pontok, a Linestring és a sokszögek jelenleg támogatottak.

Az **összetett** index típusa a következő:

- `ORDER BY`több tulajdonság lekérdezései: 

   ```sql
   SELECT * FROM container c ORDER BY c.firstName, c.lastName
   ```

## <a name="querying-with-indexes"></a>Lekérdezés indexekkel

Az adatindexelés során kinyert elérési utak megkönnyítik az index keresését egy lekérdezés feldolgozásakor. Az indexelt `WHERE` elérési utak listáját tartalmazó lekérdezés záradékának egyeztetésével nagyon gyorsan azonosíthatja a lekérdezési predikátumnak megfelelő elemeket.

Vegyük például a következő lekérdezést: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. A lekérdezési predikátum (elemek szűrése, ahol bármely hely "Franciaország", mint országa) az alábbi piros színnel jelölt elérési útra hasonlít:

![Megadott elérési út megfeleltetése egy fában belül](./media/index-overview/matching-path.png)

> [!NOTE]
> Egy olyan záradék, amelyet egy adott tulajdonság megrendelése mindig egy tartomány indexre van szüksége, és sikertelen lesz, ha az általa hivatkozott elérési út nem rendelkezik ilyennel.  `ORDER BY` Hasonlóképpen, a több `ORDER BY` lekérdezésnek *mindig* összetett indexre van szüksége.

## <a name="next-steps"></a>További lépések

Az indexeléssel kapcsolatos további információkért olvassa el a következő cikkeket:

- [Indexelési házirend](index-policy.md)
- [Az indexelési szabályzat kezelése](how-to-manage-indexing-policy.md)
