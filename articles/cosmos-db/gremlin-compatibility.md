---
title: Azure Cosmos DB Gremlin kompatibilitás az TinkerPop-funkciókkal
description: Dokumentációs Graph Engine kompatibilitási problémák
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81449884"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin kompatibilitása
Azure Cosmos DB Graph Engine szorosan követi az [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) bejárási lépések specifikációját, de különbségek vannak.

## <a name="behavior-differences"></a>Viselkedési különbségek

* Azure Cosmos DB gráf motorja a ***szélesség – első*** bejárást futtatja, miközben a TinkerPop-Gremlin a mélysége. Ez a viselkedés jobb teljesítményt érhet el vízszintesen méretezhető, például Cosmos DB. 

## <a name="unsupported-features"></a>Nem támogatott funkciók

* A ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** egy programnyelvfüggetlen specifikáció gráfbejárásokhoz. Cosmos DB gráf még nem támogatja. A `GremlinClient.SubmitAsync()` bejárást szöveges karakterláncként használja és adja át.

* ***`property(set, 'xyz', 1)`*** a kardinális beállítása jelenleg nem támogatott. A `property(list, 'xyz', 1)` használható helyette. További információ: [Vertex Properties with TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** lehetővé teszi a graphok lekérdezését a deklaratív minta egyeztetésével. Ez a funkció nem érhető el.

* A csúcspontokon vagy éleken lévő ***Tulajdonságok*** nem támogatottak. A tulajdonságok csak egyszerű típusok vagy tömbök lehetnek.

* ***A tömb tulajdonságainak*** `order().by(<array property>)` rendezése nem támogatott. A rendezést csak az egyszerű típusok támogatják.

* A ***nem PRIMITÍV JSON-típusok*** nem támogatottak. Használjon `string`, `number` `true` / vagy `false` típusokat. `null`az értékek nem támogatottak. 

* A ***GraphSONv3*** szerializáló jelenleg nem támogatott. Szerializáló, olvasó és író osztályok használata `GraphSONv2` a kapcsolatok konfigurációjában. A Azure Cosmos DB Gremlin API által visszaadott eredmények formátuma nem egyezik meg a GraphSON formátumával. 

* **A lambda kifejezések és függvények** jelenleg nem támogatottak. Ez magában foglalja `.map{<expression>}`a, `.by{<expression>}`a, és `.filter{<expression>}` a függvényeket. Ha többet szeretne megtudni, és meg szeretné tudni, hogyan írhatók újra a Gremlin lépések használatával, tekintse [meg a lambdas-ról szóló megjegyzést](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* A rendszer elosztott jellege miatt a ***tranzakciók*** nem támogatottak.  Konfigurálja a megfelelő konzisztencia-modellt a Gremlin-fiókban a "saját írások olvasása" elemre, és használja az optimista párhuzamosságot az ütköző írások feloldásához.

## <a name="next-steps"></a>További lépések
* Látogasson el [Cosmos db felhasználói hang](https://feedback.azure.com/forums/263030-azure-cosmos-db) oldalára, ahol megoszthatja a visszajelzéseket, és segítheti a csapatot a fontos funkciókhoz.
