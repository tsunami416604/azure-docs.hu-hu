---
title: A végrehajtási profil használatával kiértékelheti a lekérdezéseket az Azure Cosmos DB Gremlin API-ban
description: Ismerje meg, hogyan háríthatja el a hibákat, és javíthatja a Gremlin-lekérdezéseket a végrehajtási profil lépés használatával.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441857"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Gremlin-lekérdezések kiértékelése a végrehajtási profil lépés segítségével

Ez a cikk áttekinti, hogyan használható a végrehajtási profil lépés az Azure Cosmos DB Gremlin API gráfadatbázisokkal. Ez a lépés a hibaelhárításhoz és a lekérdezés optimalizálásához nyújt releváns információkat, és kompatibilis minden olyan Gremlin-lekérdezéssel, amelyet egy Cosmos DB Gremlin API-fiókon lehet végrehajtani.

A lépés használatához egyszerűen fűzze hozzá a `executionProfile()` függvényhívás végén a Gremlin lekérdezés. **A Gremlin-lekérdezés végrehajtása megtörténik,** és a művelet eredménye egy JSON-válaszobjektumot ad vissza a lekérdezés végrehajtási profillal.

Példa:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

A lépés `executionProfile()` meghívása után a válasz egy JSON-objektum lesz, amely tartalmazza a végrehajtott Gremlin lépést, a teljes időt, és a Cosmos DB futásidejű operátorok egy tömbjét, amelyet a utasítás eredményezett.

> [!NOTE]
> Ez a végrehajtási profil megvalósítása nincs definiálva az Apache Tinkerpop specifikációban. Az Azure Cosmos DB Gremlin API implementációja egyedi.


## <a name="response-example"></a>Példa válaszra

A következő egy megjegyzésvel ellátható példa a visszaadandó kimenetre:

> [!NOTE]
> Ez a példa megjegyzésekkel van elmagyarázva, amelyek a válasz általános szerkezetét magyarázzák. A tényleges executionProfile válasz nem tartalmaz megjegyzéseket.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> A executionProfile lépés végrehajtja a Gremlin lekérdezést. Ez magában `addV` `addE`foglalja a vagy lépéseket, amelyek a létrehozást eredményezik, és véglegesítik a lekérdezésben megadott módosításokat. Ennek eredményeképpen a Gremlin lekérdezés által létrehozott kérelem egységek is felszámolásra kerül.

## <a name="execution-profile-response-objects"></a>Végrehajtási profilválasz-objektumok

A executionProfile() függvény válasza a JSON-objektumok hierarchiáját eredményezi a következő struktúrával:
  - **Gremlin műveleti objektum**: A teljes Gremlin műveletet jelképezi, amelyet végrehajtottak. A következő tulajdonságokat tartalmazza.
    - `gremlin`: A végrehajtott explicit Gremlin utasítás.
    - `totalTime`: Az az idő, ezredmásodpercben, amikor a lépés végrehajtása bekövetkező. 
    - `metrics`: A rendszer, amely tartalmazza az egyes Cosmos DB futásidejű operátorok, amelyek végrehajtása a lekérdezés teljesítéséhez. Ez a lista a végrehajtás sorrendjében van rendezve.
    
  - **Cosmos DB futásidejű operátorok:** A teljes Gremlin-művelet egyes összetevőit jelöli. Ez a lista a végrehajtás sorrendjében van rendezve. Minden objektum a következő tulajdonságokat tartalmazza:
    - `name`: Az üzemeltető neve. Ez a kiértékelt és végrehajtott lépés típusa. Bővebben az alábbi táblázatban.
    - `time`: Az az idő, ezredmásodpercben, amelyet egy adott operátor vett igénybe.
    - `annotations`: A végrehajtott operátorra vonatkozó további információkat tartalmaz.
    - `annotations.percentTime`: Az adott operátor végrehajtásához szükséges teljes idő százaléka.
    - `counts`: Azon objektumok száma, amelyeket ez az operátor visszaadott a tárolórétegről. Ez a skaláris értékben `counts.resultCount` található.
    - `storeOps`: Egy olyan tárolási műveletet jelöl, amely egy vagy több partícióra is kiterjedhet.
    - `storeOps.fanoutFactor`: Az adott tárolási művelet által elért partíciók számát jelöli.
    - `storeOps.count`: A tárolási művelet által visszaadott eredmények számát jelöli.
    - `storeOps.size`: Egy adott tárolási művelet eredményének méretét bájtban adja meg.

Cosmos DB Gremlin futásidejű operátor|Leírás
---|---
`GetVertices`| Ez a lépés az objektumok predikátus készletét a perzisztenciarétegből szerzi be. 
`GetEdges`| Ez a lépés a csúcsok készletével szomszédos éleket kapja meg. Ez a lépés egy vagy több tárolási műveletet eredményezhet.
`GetNeighborVertices`| Ez a lépés a szélekhez csatlakoztatott csúcspontokat szerzi be. Az élek tartalmazzák a partíciókulcsokat és a forrás- és célcsúcsok azonosítóit.
`Coalesce`| Ez a lépés két művelet kiértékelését `coalesce()` számlálja a Gremlin lépés végrehajtásakor.
`CartesianProductOperator`| Ez a lépés egy descartes-i terméket számít ki két adatkészlet között. Általában végre, amikor a `to()` `from()` predikátumok vagy használják.
`ConstantSourceOperator`| Ez a lépés kiszámítja a kifejezést, hogy ennek eredményeképpen állandó értéket eredményez.
`ProjectOperator`| Ez a lépés előkészíti és szerializálja a választ az előző műveletek eredményének használatával.
`ProjectAggregation`| Ez a lépés előkészíti és szerializálja az összesítő műveletekre adott választ.

> [!NOTE]
> Ez a lista az új operátorok hozzáadásakor is frissülni fog.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Példák a végrehajtási profilválasz elemzésére

Az alábbiakban példákat mutatunk be a Végrehajtási profil válaszával kiszúrt gyakori optimalizálásokra:
  - Vak fan-out lekérdezés.
  - Szűretlen lekérdezés.

### <a name="blind-fan-out-query-patterns"></a>Vak, fan-out lekérdezési minták

Tegyük fel, hogy a következő végrehajtási profilválaszt egy **particionált grafikon**:

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

Ebből a következő következtetéseket lehet levonni:
- A lekérdezés egyetlen azonosító-lekérdezés, mivel a Gremlin utasítás `g.V('id')`követi a mintát.
- A mérőszám `time` alapján a lekérdezés késése magasnak tűnik, mivel [több mint 10 ms egyetlen pontolvasási művelethez.](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide)
- Ha megnézzük `storeOps` az objektumot, láthatjuk, hogy az `fanoutFactor` is `5`, ami azt jelenti, hogy 5 [partíciót](https://docs.microsoft.com/azure/cosmos-db/partition-data) is elérhető ez a művelet.

Ennek az elemzésnek a következtetéseként megállapíthatjuk, hogy az első lekérdezés a szükségesnél több partícióhoz fér hozzá. Ez a lekérdezésben a particionálási kulcs predikátumként történő megadásával orvosható. Ez kevesebb késést és lekérdezésenkénti költséghez vezet. További információ a [gráfparticionálásról.](graph-partitioning.md) Egy optimálisabb lekérdezés `g.V('tt0093640').has('partitionKey', 't1001')`lenne .

### <a name="unfiltered-query-patterns"></a>Szűretlen lekérdezési minták

Hasonlítsa össze a következő két végrehajtási profil válaszok. Az egyszerűség kedvéért ezek a példák egyetlen particionált grafikont használnak.

Ez az első lekérdezés lekéri az `tweet` összes csúcsot a címkével, majd beszerzi a szomszédos csúcspontokat:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Figyelje meg ugyanannak a lekérdezésnek a `has('lang', 'en')`profilját, de most egy további szűrővel, mielőtt felfedezné a szomszédos csúcspontokat:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Ez a két lekérdezés ugyanazt az eredményt érte el, azonban az első több kérelemegységet igényel, mivel a szomszédos elemek lekérdezése előtt nagyobb kezdeti adatkészletet kellett itegálnia. Láthatjuk mutatók ezt a viselkedést, ha összehasonlítjuk a következő paramétereket mindkét válasz:
- Az `metrics[0].time` érték magasabb az első válaszban, ami azt jelzi, hogy ez az egyetlen lépés feloldása hosszabb időt vett igénybe.
- Az `metrics[0].counts.resultsCount` érték az első válaszban is magasabb, ami azt jelzi, hogy a kezdeti munkaadatkészlet nagyobb volt.

## <a name="next-steps"></a>További lépések
* Ismerje meg az Azure Cosmos DB [támogatott Gremlin-funkcióit.](gremlin-support.md) 
* Tudjon meg többet a [Gremlin API-ról az Azure Cosmos DB-ben.](graph-introduction.md)
