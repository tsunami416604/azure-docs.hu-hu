---
title: Azure Cosmos DB Gremlin kompatibilitás az TinkerPop-funkciókkal
description: Dokumentációs Graph Engine kompatibilitási problémák
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 7257246f618e3028534f3ebd60eaf6f94a3a4720
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092507"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin kompatibilitása
Azure Cosmos DB Graph Engine szorosan követi az [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) bejárási lépések specifikációját, de eltérések vannak a Azure Cosmos DBra vonatkozó implementációban. A támogatott Gremlin lépések listáját a [GREMLIN API-protokoll támogatását](gremlin-support.md) ismertető cikkben tekintheti meg.

## <a name="behavior-differences"></a>Viselkedési különbségek

* Azure Cosmos DB gráf motorja a ***szélesség – első*** bejárást futtatja, miközben a TinkerPop-Gremlin a mélysége. Ez a viselkedés jobb teljesítményt érhet el vízszintesen méretezhető, például Cosmos DB. 

## <a name="unsupported-features"></a>Nem támogatott funkciók

* A ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** egy programnyelvfüggetlen specifikáció gráfbejárásokhoz. Cosmos DB gráf még nem támogatja. `GremlinClient.SubmitAsync()`A bejárást szöveges karakterláncként használja és adja át.

* ***`property(set, 'xyz', 1)`*** a kardinális beállítása jelenleg nem támogatott. A `property(list, 'xyz', 1)` használható helyette. További információ: [Vertex Properties with TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* A *** `match()` lépés*** jelenleg nem érhető el. Ez a lépés deklaratív lekérdezési képességeket biztosít.

* A csúcspontokon vagy éleken lévő ***Tulajdonságok*** nem támogatottak. A tulajdonságok csak egyszerű típusok vagy tömbök lehetnek.

* ***Rendezés tömb tulajdonságai szerint*** `order().by(<array property>)` nem támogatott. A rendezést csak az egyszerű típusok támogatják.

* A ***nem PRIMITÍV JSON-típusok*** nem támogatottak. Használjon `string` , `number` vagy `true` / `false` típusokat. `null` az értékek nem támogatottak. 

* A ***GraphSONv3*** szerializáló jelenleg nem támogatott. `GraphSONv2`Szerializáló, olvasó és író osztályok használata a kapcsolatok konfigurációjában. A Azure Cosmos DB Gremlin API által visszaadott eredmények formátuma nem egyezik meg a GraphSON formátumával. 

* **A lambda kifejezések és függvények** jelenleg nem támogatottak. Ez magában foglalja a `.map{<expression>}` , a `.by{<expression>}` , és a `.filter{<expression>}` függvényeket. Ha többet szeretne megtudni, és meg szeretné tudni, hogyan írhatók újra a Gremlin lépések használatával, tekintse [meg a lambdas-ról szóló megjegyzést](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* A rendszer elosztott jellege miatt a ***tranzakciók*** nem támogatottak.  Konfigurálja a megfelelő konzisztencia-modellt a Gremlin-fiókban a "saját írások olvasása" elemre, és használja az optimista párhuzamosságot az ütköző írások feloldásához.

## <a name="known-limitations"></a>Ismert korlátozások

* A **Gremlin lekérdezések használatának indexelése a bejárási `.V()` lépésekkel**: jelenleg csak a `.V()` bejárások első hívása fogja használni az indexet az ahhoz csatolt szűrők vagy predikátumok feloldásához. A következő hívások nem fogják megkeresni az indexet, ami növelheti a lekérdezés késését és költségeit.
    
    Az alapértelmezett indexelés feltételezi, hogy a lépéssel kezdődő tipikus olvasási Gremlin-lekérdezés `.V()` paramétereket használ a csatolt szűrési lépéseiben, például a `.has()` vagy a `.where()` segítségével optimalizálja a lekérdezés költségeit és teljesítményét. Például:

    ```java
    g.V().has('category', 'A')
    ```

    Ha azonban `.V()` a Gremlin-lekérdezés több lépést is tartalmaz, előfordulhat, hogy a lekérdezéshez tartozó adatfelbontás nem optimális. Példaként végezze el a következő lekérdezést:

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    Ez a lekérdezés a csúcspontok két csoportját fogja visszaadni a megnevezett tulajdonság alapján `category` . Ebben az esetben csak az első hívás `g.V().has('category', 'A')` fogja használni az indexet, hogy feloldja a csúcspontokat a tulajdonságaik értékei alapján.

    A lekérdezésre vonatkozó Áthidaló megoldás az olyan bejárási lépések használata, mint a `.map()` és a `union()` . Ez az alábbi példán alapul:

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    A lekérdezések teljesítményét a [Gremlin `executionProfile()` lépés](graph-execution-profile.md)használatával tekintheti át.

## <a name="next-steps"></a>További lépések
* Látogasson el [Cosmos db felhasználói hang](https://feedback.azure.com/forums/263030-azure-cosmos-db) oldalára, ahol megoszthatja a visszajelzéseket, és segítheti a csapatot a fontos funkciókhoz.
