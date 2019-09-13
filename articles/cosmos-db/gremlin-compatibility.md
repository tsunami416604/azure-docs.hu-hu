---
title: Azure Cosmos DB Gremlin kompatibilitás az TinkerPop-funkciókkal
description: Dokumentációs Graph Engine kompatibilitási problémák
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: d58d1ea50251c3568161ce008db2672ff0fd6372
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910991"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin kompatibilitása
Azure Cosmos DB Graph Engine szorosan követi az [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) bejárási lépések specifikációját, de különbségek vannak.

## <a name="behavior-differences"></a>Viselkedési különbségek

* Azure Cosmos DB gráf motorja a ***szélesség – első*** bejárást futtatja, miközben a TinkerPop-Gremlin a mélysége. Ez a viselkedés jobb teljesítményt érhet el vízszintesen méretezhető, például Cosmos DB. 

## <a name="unsupported-features"></a>Nem támogatott szolgáltatások

* A ***[Gremlin Bytecode](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** egy programnyelvfüggetlen specifikáció gráfbejárásokhoz. Cosmos DB gráf még nem támogatja. A ```GremlinClient.SubmitAsync()``` bejárást szöveges karakterláncként használja és adja át.

* ***```property(set, 'xyz', 1)```*** a kardinális beállítása jelenleg nem támogatott. Használja ```property(list, 'xyz', 1)``` helyette.

* ***```match()```*** lehetővé teszi a graphok lekérdezését a deklaratív minta egyeztetésével. Ez a funkció nem érhető el.

* A csúcspontokon vagy éleken lévő ***Tulajdonságok*** nem támogatottak. A tulajdonságok csak egyszerű típusok vagy tömbök lehetnek.

* ***Rendezés tömb tulajdonságai szerint*** ```.order().by(<array property>)``` nem támogatott. A rendezést csak az egyszerű típusok támogatják.

* A ***nem PRIMITÍV JSON-típusok*** nem támogatottak. Használjon ```string```, ```number```vagy ```true``` típusokat./ ```false``` ```null```az értékek nem támogatottak. 

* A ***GraphSONv3*** szerializáló jelenleg nem érhető el.

* A rendszer elosztott jellege miatt a ***tranzakciók*** nem támogatottak.  Konfigurálja a megfelelő konzisztencia-modellt a Gremlin-fiókban a "saját írások olvasása" elemre, és használja az optimista párhuzamosságot az ütköző írások feloldásához.

## <a name="next-steps"></a>További lépések
* Látogasson el [Cosmos db felhasználói hang](https://feedback.azure.com/forums/263030-azure-cosmos-db) oldalára, ahol megoszthatja a visszajelzéseket, és segítheti a csapatot a fontos funkciókhoz.
