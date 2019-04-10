---
title: A függvény a végrehajtási profil lekérdezések kiértékelése az Azure Cosmos DB Gremlin API
description: Ismerje meg, hogyan javíthatja a Gremlin-lekérdezések végrehajtási profil lépés használata és hibáinak elhárítása.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 2f3967c64e79b2bc7b01b35eff26f5ac0d4e3db4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288606"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>A végrehajtási profil lépés használata a Gremlin-lekérdezések kiértékelése

Ez a cikk áttekinti a végrehajtási profil lépés használata az Azure Cosmos DB Gremlin API gráf típusú adatbázisokat. Ebben a lépésben mutatjuk vonatkozó információk és lekérdezés-optimalizálást, és kompatibilis a bármely Gremlin-lekérdezés is végrehajtható egy Cosmos DB Gremlin API-fiók.

Ezt a lépést, egyszerűen csak fűzze hozzá a `executionProfile()` függvényhívás a Gremlin-lekérdezés végén. **A Gremlin-lekérdezés végrehajtásának** és a művelet eredményét adja vissza a lekérdezés-végrehajtási profil rendelkező JSON-válasz objektum.

Példa:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Hívása után a `executionProfile()` lépést, a válasz lesz, JSON-objektum, amely tartalmazza a Gremlin végrehajtott lépést, a teljes idő alatt jutott és a Cosmos DB modul operátorok, melyek kiváltották a utasítás tömbjét.

> [!NOTE]
> Ez a megvalósítás végrehajtási profil nincs definiálva az Apache Tinkerpop-specifikáció. Azt az Azure Cosmos DB Gremlin API-k megvalósításához.


## <a name="response-example"></a>Példa a válasz

Az alábbiakban látható a kimenete a visszaad egy jelmagyarázattal kiegészített példát:

> [!NOTE]
> Ez a példa a válasz általános szerkezetét világítsa megjegyzésekkel van feliratozva. Tényleges executionProfile választ nem tartalmaz megjegyzéseket.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation obtains a set of Edge objects. Depending on the query, these might be directly adjacent to a set of vertices, or separate, in the case of an E() query.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation represents the serialization and preparation for a result from the preceding graph operations.
        // The metrics include: time, percentTime of total execution time and resultCount.
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
> A executionProfile lépés végrehajtja a Gremlin-lekérdezés. Ez magában foglalja a `addV` vagy `addE`lépést is, hogy a létrehozás eredményez, és véglegesíti a módosításokat a lekérdezésben megadott. Ennek eredményeképpen a kérelemegység a Gremlin-lekérdezés által generált is kell kifizetnie.

## <a name="execution-profile-response-objects"></a>Végrehajtási profil válaszobjektumok

A válasz egy executionProfile() függvény értékét fogják eredményezni hierarchiájában a JSON-objektumok az alábbi struktúra használatával:
  - **Gremlin művelet objektum**: A teljes Gremlin művelet végrehajtott jelöli. A következő tulajdonságokat tartalmazza.
    - `gremlin`: Az explicit Gremlin-utasítás, amely végre lett hajtva.
    - `totalTime`: Az idő, ezredmásodpercben, amely a lépés végrehajtása során felmerült. 
    - `metrics`: Egy tömb, amely tartalmazza a Cosmos DB modul operátorok, a lekérdezés teljesítése érdekében végrehajtott minden egyes. Ez a lista rendezése a végrehajtás sorrendje.
    
  - **A cosmos DB modul operátorok**: Egyes összetevők, a teljes Gremlin művelet jelöli. Ez a lista rendezése a végrehajtás sorrendje. Minden objektum a következő tulajdonságokat tartalmazza:
    - `name`: Az operátor neve. Ez a lépés kiértékelve és végrehajtva típusát. Tudjon meg többet az alábbi táblázatban.
    - `time`: Eltelt idő (MS), egy adott operátor igénybe vett.
    - `annotations`: További információkat tartalmaz, az üzemeltető végrehajtott jellemző.
    - `annotations.percentTime`: Által az adott operátor végrehajtásához szükséges idő teljes százaléka.
    - `counts`: Ez az operátor által a tárolási réteg által visszaadott objektumok száma. Ez az szerepel a `counts.resultCount` skaláris érték belül.
    - `storeOps`: Egy tárolási művelet, amely egy vagy több partíció is kiterjedhet jelöli.
    - `storeOps.fanoutFactor`: Bizonyos tárolási művelet elérhető a partíciók számát jelenti.
    - `storeOps.count`: A tárolási művelet által visszaadott eredmények számát jelenti.
    - `storeOps.size`: A mérete (bájt) a megadott művelet eredményének jelöli.

A cosmos DB Gremlin futásidejű operátor|Leírás
---|---
`GetVertices`| Ebben a lépésben a adatmegőrző réteget objektumok predicated csoportja kapja. 
`GetEdges`| Ebben a lépésben beszerzi az élek szomszédos csúcspontok készletét. Ebben a lépésben egy vagy több tároló műveleteket eredményezhet.
`GetNeighborVertices`| Ebben a lépésben beszerzi a csúcsok, élek készletét csatlakozó. Az élek a partíció tartalmazza a kulcsok és -azonosítók a forrás és a cél a csúcspontok.
`Coalesce`| Ebben a lépésben két művelet próbaidőszakára fiókok minden alkalommal, amikor a `coalesce()` Gremlin lépés hajtja végre.
`CartesianProductOperator`| Ebben a lépésben kiszámítja a Descartes-termék két adatkészlet között. Általában végrehajtott minden alkalommal, amikor a predikátumok `to()` vagy `from()` szolgálnak.
`ConstantSourceOperator`| Ebben a lépésben kiszámítja az kifejezés eredményeképpen létrehozzon egy állandó értéket.
`ProjectOperator`| Ebben a lépésben előkészíti, és a egy választ, az eredmény a fenti műveletek használatával szerializálja.
`ProjectAggregation`| Ebben a lépésben előkészíti, és szerializálja összesített művelet esetén választ.

> [!NOTE]
> Ez a lista továbbra is frissíteni kell az új kezelők hozzáadásakor.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Példák a végrehajtási profil választ elemzése

A következő példák közös optimalizálásokat is lehet pedig a pöttyös a végrehajtási profil válasz használatával:
  - Titkos logikájával lekérdezés.
  - A lekérdezés szűrés nélkül.

### <a name="blind-fan-out-query-patterns"></a>Titkos logikájával lekérdezési minták

Tegyük fel, a következő végrehajtási profil választ az egy **particionált graph**:

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

A következő következtetések származó lehet tenni:
- A lekérdezés nem egyetlen azonosító keresése, mivel a Gremlin-utasítást a mintát követi, `g.V('id')`.
- A pályamunkának a `time` mérőszám, a késés, a lekérdezés úgy tűnik, hogy magas, mert [több, mint az egyetlen pont olvasási művelet 10ms](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Ha mi megvizsgáljuk a `storeOps` objektumot, láthatjuk, hogy a `fanoutFactor` van `5`, ami azt jelenti, hogy [5 partíciók](https://docs.microsoft.com/azure/cosmos-db/partition-data) hozzáfértek a művelet.

Ez az elemzés megkötése, mint határozható meg, hogy az első lekérdezés szükségesnél több partíciót fér hozzá. Ez a lekérdezés a predikátum a particionálókulcs megadásával kell címezni. Ez azt eredményezi, kisebb késleltetésű és kevesebb költség / lekérdezés. Tudjon meg többet [graph-particionálás](graph-partitioning.md). Egy több optimális lekérdezési lenne `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>Nem szűrt lekérdezési minták

Hasonlítsa össze a következő két végrehajtási profil válaszok. Az egyszerűség kedvéért ezek a példák a particionált diagramon.

Az első lekérdezés lekéri a címkével ellátott összes csúcspontok `tweet` és majd beolvassa a szomszédos csúcsok:

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

Figyelje meg, hogy a profil ugyanabból a lekérdezés, de most egy kiegészítő szűrőt `has('lang', 'en')`, előtt a szomszédos csúcspontok felfedezése:

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

E két lekérdezést ugyanaz az eredmény elérése, azonban az elsőt már szükség van további Kérelemegységet rá szükség, egy nagyobb kezdeti adatkészlet újrafuttathatja előtt a szomszédos elemek lekérdezése óta. Ez a viselkedés kijelzőjét láthatjuk, összehasonlításakor mindkét válaszok a következő paraméterekkel:
- A `metrics[0].time` értéke magasabb a az első válasz, amely azt jelzi, hogy ez egyetlen lépésben hosszabb idő alatt megoldásához.
- A `metrics[0].counts.resultsCount` újabb, valamint az első választ, amely azt jelzi, hogy a kezdeti működő adatkészlet nagyobb az értéke.

## <a name="next-steps"></a>További lépések
* További információ a [támogatott Gremlin szolgáltatások](gremlin-support.md) Azure Cosmos DB-ben. 
* Tudjon meg többet a [az Azure Cosmos DB Gremlin API](graph-introduction.md).
