---
title: Az Azure Cosmos DB Gremlin kompatibilitás a TinkerPop funkcióival
description: Referenciadokumentáció A diagrammotor kompatibilitásával kapcsolatos problémák
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81449884"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Az Azure Cosmos DB Gremlin kompatibilitása
Az Azure Cosmos DB Graph motor szorosan követi [az Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) bejárási lépéseit, de vannak különbségek.

## <a name="behavior-differences"></a>Viselkedésbeli különbségek

* Az Azure Cosmos DB Graph motor ***a szélesség-első*** bejárást futtatja, míg a TinkerPop Gremlin mélység-első. Ez a viselkedés jobb teljesítményt ér el a vízszintesen méretezhető rendszerben, például a Cosmos DB-ben. 

## <a name="unsupported-features"></a>Nem támogatott szolgáltatások

* A ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** egy programnyelvfüggetlen specifikáció gráfbejárásokhoz. Cosmos DB Graph még nem támogatja. A `GremlinClient.SubmitAsync()` bejárást szöveges karakterláncként használhatja és adja át.

* ***`property(set, 'xyz', 1)`*** a set számosság ma nem támogatott. A `property(list, 'xyz', 1)` használható helyette. További információ: [Vertex properties with TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** lehetővé teszi a deklaratív mintaegyeztetéssel történő grafikonok lekérdezését. Ez a funkció nem érhető el.

* ***Az objektumok a*** csúcspontokon vagy éleken tulajdonságként nem támogatottak. A tulajdonságok csak egyszerű típusok vagy tömbök lehetnek.

* ***Tömbtulajdonságok*** `order().by(<array property>)` szerint történő rendezés nem támogatott. A rendezést csak az egyszerű típusok támogatják.

* ***A nem primitív JSON-típusok*** nem támogatottak. Használja `string` `number`a `true` / `false` , , vagy típusokat. `null`értékek nem támogatottak. 

* ***GraphSONv3*** szerializáló jelenleg nem támogatott. Használja `GraphSONv2` a Szerializáló, a Reader és a Writer osztályokat a kapcsolatkonfigurációban. Az Azure Cosmos DB Gremlin API által visszaadott eredmények nem rendelkeznek a GraphSON formátummal megegyező formátummal. 

* **Lambda kifejezések és függvények** jelenleg nem támogatottak. Ez magában `.map{<expression>}`foglalja `.by{<expression>}`a `.filter{<expression>}` , a és a függvényeket. Ha többet szeretne megtudni, és megtudhatja, hogyan kell átírni őket a Gremlin lépésekkel, olvassa el [A Megjegyzés a Lambdas-ról](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***A tranzakciók*** nem támogatottak a rendszer elosztott jellege miatt.  Konfigurálja a gremlin-fiók megfelelő konzisztenciamodelljét a "saját írások olvasásához", és használjon optimista egyidejűséget az ütköző írások feloldásához.

## <a name="next-steps"></a>További lépések
* Látogasson el [a Cosmos DB felhasználói hangoldalára,](https://feedback.azure.com/forums/263030-azure-cosmos-db) ahol megoszthatja visszajelzéseit, és segít a csapatnak az Ön számára fontos funkciókra összpontosítani.
