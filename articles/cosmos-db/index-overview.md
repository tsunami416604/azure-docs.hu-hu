---
title: Indexelés az Azure Cosmos DB-ben
description: Ismerje meg, hogyan működik az indexelés az Azure Cosmos DB-ben, különböző típusú indexek, például a tartomány, térbeli, összetett indexek támogatott.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: thweiss
ms.openlocfilehash: 65186262095560d7ae54d32b218d1c01f1fb921d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873624"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexelés az Azure Cosmos DB-ben – Áttekintés

Az Azure Cosmos DB egy séma-független adatbázis, amely lehetővé teszi, hogy az alkalmazás iterálni anélkül, hogy a séma vagy index kezelése. Alapértelmezés szerint az Azure Cosmos DB automatikusan indexeli a [tárolóban](databases-containers-items.md#azure-cosmos-containers) lévő összes elem minden tulajdonságát anélkül, hogy bármilyen sémát definiálna vagy másodlagos indexeket kellene konfigurálnia.

A cikk célja annak ismertetése, hogy az Azure Cosmos DB hogyan indexeli az adatokat, illetve hogy miként használja az indexeket a lekérdezési teljesítmény javításához. Javasoljuk, hogy az [indexelési házirendek](index-policy.md)testreszabása előtt menjen át ezen a szakaszon.

## <a name="from-items-to-trees"></a>Elemektől a fákig

Minden alkalommal, amikor egy elemet tárolóban tárolnak, annak tartalma JSON-dokumentumként lesz kivetítve, majd faábrázolássá alakul. Ez azt jelenti, hogy az elem minden tulajdonsága csomópontként jelenik meg egy fán. Egy pszeudo gyökércsomópont jön létre szülőként az elem összes első szintű tulajdonságához. A levélcsomópontok tartalmazzák az elem által szállított tényleges skaláris értékeket.

Vegyük például ezt az elemet:

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

Ez lenne képviseli a következő fa:

![Az előző elem faként jelenik meg](./media/index-overview/item-as-tree.png)

Figyelje meg, hogyan vannak kódolva a tömbök a fában: egy tömb minden bejegyzése kap egy köztes csomópontot, amely a tömbön belüli bejegyzés indexével van címkézve (0, 1 stb.).

## <a name="from-trees-to-property-paths"></a>A fáktól a tulajdonságelérési utakig

Az Azure Cosmos DB azért alakítja át az elemeket fákká, mert lehetővé teszi, hogy a tulajdonságokra hivatkozzon a fákon belüli útvonalaik. Egy tulajdonság elérési útjának leválasztásához átjárhatjuk a fát a gyökércsomóponttól a tulajdonságig, és összefűzhetjük az egyes áthaladó csomópontok feliratait.

Az alábbiakban a fent leírt példaelem minden tulajdonságának elérési útjai láthatók:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Egy elem írásakor az Azure Cosmos DB hatékonyan indexeli az egyes tulajdon elérési útját és a megfelelő értéket.

## <a name="index-kinds"></a>Index típusai

Az Azure Cosmos DB jelenleg háromféle indexet támogat.

### <a name="range-index"></a>Tartomány indexe

**A tartományindex** rendezett faszerű struktúrán alapul. A tartományindex-típusú a következőkre szolgál:

- Egyenlőségi lekérdezések:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Egyenlőségi egyezés tömbelemen
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1”)
    ```

- Tartománylekérdezések:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (munkák `>`, `<` `>=`, `<=` `!=`, , )

- Tulajdonság jelenlétének ellenőrzése:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- A karakterlánc-előtag egyezik (a kulcsszó ttartalmazza a tartományindexet):

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY`Lekérdezések:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`Lekérdezések:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

A tartományindexek skaláris értékeken (karakterlánc on vagy számon) használhatók.

### <a name="spatial-index"></a>Térbeli index

**A térbeli** indexek hatékony lekérdezéseket tesznek lehetővé térinformatikai objektumokon, például - pontokon, vonalakon, sokszögeken és többpoligonon. Ezek a lekérdezések ST_DISTANCE, ST_WITHIN ST_INTERSECTS kulcsszavakat használnak. Az alábbiakban néhány példa, hogy a térbeli index fajta:

- Térinformatikai távolságlekérdezések:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Térinformatikai belül lekérdezések:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Térinformatikai metsző lekérdezések:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

A térbeli indexek helyesen formázott [GeoJSON](geospatial.md) objektumokon használhatók. A pontok, a linestringek, a sokszögek és a multipoligonok jelenleg támogatottak.

### <a name="composite-indexes"></a>Összetett indexek

**Az összetett** indexek növelik a hatékonyságot, ha több mezőben hajt végre műveleteket. Az összetett index-fajtát a következőkre használjuk:

- `ORDER BY`több tulajdonságra vonatkozó lekérdezések:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Lekérdezések szűrővel `ORDER BY`és . Ezek a lekérdezések összetett indexet használhatnak, `ORDER BY` ha a szűrőtulajdonság hozzá van adva a záradékhoz.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Szűrővel rendelkező lekérdezések két vagy több olyan tulajdonságra, ahol legalább egy tulajdonság egyenlőségszűrő

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Mindaddig, amíg egy szűrő predikátum használja az index típusú, a lekérdezési motor értékeli, hogy az első vizsgálat előtt a többit. Ha például sql-lekérdezésünk van, például`SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* A fenti lekérdezés először szűri azokat a bejegyzéseket, ahol a keresztnév = "András" az index használatával. Ezután az összes firstName = "András" bejegyzést átadja egy későbbi folyamaton, hogy kiértékelje a tartalmazza a szűrőpredikátumot.

* Felgyorsíthatja a lekérdezéseket, és elkerülheti a teljes tárolóvizsgálatot, ha olyan függvényeket használ, amelyek nem használják az indexet (pl. tartalmazza) további szűrőpredikátumok hozzáadásával, amelyek az indexet használják. A szűrőzáradékok sorrendje nem fontos. A lekérdezési motor fogja kitalálni, hogy mely predikátumok szelektívebb, és ennek megfelelően futtassa a lekérdezést.


## <a name="querying-with-indexes"></a>Lekérdezés indexekkel

Az adatok indexeléseksorán kinyert elérési utak megkönnyítik az index lekérdezés feldolgozása során történő lekérdezésének lekérdezésének lekérdezése során történő lekérdezésének lekérdezésének lekérdezési lekérdezésének lekérdezése. Ha egy `WHERE` lekérdezés záradékát az indexelt elérési utak listájával egyezteti, a lekérdezéspredikátumnak megfelelő elemek nagyon gyorsan azonosíthatók.

Vegyük például a `SELECT location FROM location IN company.locations WHERE location.country = 'France'`következő lekérdezést: . A lekérdezési predikátum (az elemek szűrése, ahol bármely helyen "Franciaország" az országa) megegyezik az alábbi pirossal kiemelt elérési úttal:

![Adott elérési út egyeztetése egy fán belül](./media/index-overview/matching-path.png)

> [!NOTE]
> Egy `ORDER BY` záradék, amely egyetlen tulajdonság által imaszol, *mindig* tartományindexet igényel, és sikertelen lesz, ha az általa hivatkozott elérési út nem rendelkezik ilyentel. Hasonlóképpen a `ORDER BY` több tulajdonság által rendelésre leadott lekérdezésnek *mindig* összetett indexre van szüksége.

## <a name="next-steps"></a>További lépések

Az indexelésről az alábbi cikkekben olvashat bővebben:

- [Indexelési házirend](index-policy.md)
- [Az indexelési házirend kezelése](how-to-manage-indexing-policy.md)
