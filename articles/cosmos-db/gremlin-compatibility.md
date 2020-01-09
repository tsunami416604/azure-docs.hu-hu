---
title: Azure Cosmos DB Gremlin kompatibilitás az TinkerPop-funkciókkal
description: Dokumentációs Graph Engine kompatibilitási problémák
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 581bc813ca27067b1f27ab9866a45df3084dbbcc
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644732"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin kompatibilitása
Azure Cosmos DB Graph Engine szorosan követi az [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) bejárási lépések specifikációját, de különbségek vannak.

## <a name="behavior-differences"></a>Viselkedési különbségek

* Azure Cosmos DB gráf motorja a ***szélesség – első*** bejárást futtatja, miközben a TinkerPop-Gremlin a mélysége. Ez a viselkedés jobb teljesítményt érhet el vízszintesen méretezhető, például Cosmos DB. 

## <a name="unsupported-features"></a>Nem támogatott funkciók

* A ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** egy programnyelvfüggetlen specifikáció gráfbejárásokhoz. Cosmos DB gráf még nem támogatja. Használjon `GremlinClient.SubmitAsync()`, és szöveges karakterláncként adja át a bejárást.

* ***`property(set, 'xyz', 1)`*** a kardinális beállítás jelenleg nem támogatott. A `property(list, 'xyz', 1)` használható helyette. További információ: [Vertex Properties with TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** lehetővé teszi a graphok lekérdezését a deklaratív minta egyeztetésével. Ez a funkció nem érhető el.

* A csúcspontokon vagy éleken lévő ***Tulajdonságok*** nem támogatottak. A tulajdonságok csak egyszerű típusok vagy tömbök lehetnek.

* ***A tömb tulajdonságainak rendezése*** `order().by(<array property>)` nem támogatott. A rendezést csak az egyszerű típusok támogatják.

* A ***nem PRIMITÍV JSON-típusok*** nem támogatottak. `string`, `number`vagy `true`/`false` típusokat használjon. `null` értékek nem támogatottak. 

* A ***GraphSONv3*** szerializáló jelenleg nem támogatott. `GraphSONv2` szerializáló, olvasó és író osztályok használata a kapcsolatok konfigurációjában.

* **A lambda kifejezések és függvények** jelenleg nem támogatottak. Ebbe beletartozik a `.map{<expression>}`, a `.by{<expression>}`és a `.filter{<expression>}` függvények is. Ha többet szeretne megtudni, és meg szeretné tudni, hogyan írhatók újra a Gremlin lépések használatával, tekintse [meg a lambdas-ról szóló megjegyzést](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* A rendszer elosztott jellege miatt a ***tranzakciók*** nem támogatottak.  Konfigurálja a megfelelő konzisztencia-modellt a Gremlin-fiókban a "saját írások olvasása" elemre, és használja az optimista párhuzamosságot az ütköző írások feloldásához.

## <a name="next-steps"></a>Következő lépések
* Látogasson el [Cosmos db felhasználói hang](https://feedback.azure.com/forums/263030-azure-cosmos-db) oldalára, ahol megoszthatja a visszajelzéseket, és segítheti a csapatot a fontos funkciókhoz.
