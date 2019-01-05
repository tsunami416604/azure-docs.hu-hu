---
title: Hogyan kérdezhetők le gráfadatok az Azure Cosmos DB-ben?
description: Megismerheti, hogyan kérdezhetők le gráfadatok az Azure Cosmos DB-ben
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: tutorial
ms.date: 01/02/2018
ms.reviewer: sngun
ms.openlocfilehash: 2bc79801864481562967702a7c52a7670950199b
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043974"
---
# <a name="tutorial-query-azure-cosmos-db-gremlin-api-by-using-gremlin"></a>Oktatóanyag: Azure Cosmos DB Gremlin API lekérdezése a Gremlin használatával

Az Azure Cosmos DB [Gremlin API](graph-introduction.md) támogatja a [Gremlin](https://github.com/tinkerpop/gremlin/wiki)-lekérdezéseket. Ez a cikk mintadokumentumokat és -lekérdezéseket tartalmaz a kezdéshez. A [Gremlin-támogatási](gremlin-support.md) cikkben talál részletes Gremlin-referenciát.

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Adatok lekérdezése a Gremlin használatával

## <a name="prerequisites"></a>Előfeltételek

A lekérdezések működéséhez Azure Cosmos DB-fiókkal kell rendelkeznie, és a tárolóban gráfadatoknak kell lennie. Nem rendelkezik ezekkel? A fiók létrehozásához és az adatbázis feltöltéséhez végezze el az [5 perces gyors útmutatót](create-graph-dotnet.md) vagy a [fejlesztői oktatóanyagot](tutorial-query-graph.md). A következő lekérdezéseket futtathatja a [Gremlin-konzollal](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) vagy a kedvenc Gremlin-illesztőjével.

## <a name="count-vertices-in-the-graph"></a>Csúcspontok megszámlálása a gráfban

A következő kódrészlet azt mutatja be, hogyan számlálható meg a csúcspontok száma a gráfban:

```
g.V().count()
```

## <a name="filters"></a>Szűrők

A Gremlin `has` és `hasLabel` lépéseivel végezhet szűrést, és az `and`, `or` és `not` operátorokkal kombinálva azokat összetettebb szűrőket hozhat létre. Az Azure Cosmos DB a csúcspontokon és fokokon belüli összes tulajdonság sémafüggetlen indexelését nyújtja a gyors lekérdezések érdekében:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Vetület

A `values` lépéssel megkaphatja bizonyos tulajdonságok vetületét a lekérdezés eredményeiben:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Kapcsolódó élek és csúcspontok keresése

Eddig csak minden adatbázisban működő lekérdezési operátorokat láttunk. A gráfok gyors és hatékony módjai az bejárási műveleteknek, amikor kapcsolódó élekhez vagy csúcspontokhoz kell navigálni. Keressük meg Thomas összes barátját. Ezt a Gremlin `outE` lépésével tesszük meg, hogy megtaláljuk Thomas összes külső élét, majd ezekről az élekről áthaladjunk a belső csúcspontokra a Gremlin `inV` lépésével:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

A következő lekérdezés két ugrást végez Thomas összes „barátja barátainak” megkereséséhez az `outE` és `inV` kétszeri hívásával. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

A Gremlin használatával összetettebb lekérdezéseket hozhat létre és hatékony gráfbejárási logikákat implementálhat, beleértve a szűrőkifejezések keverését, a hurkolás elvégzését a `loop` lépéssel, valamint a feltételes navigáció implementálását a `choose` lépéssel. A [Gremlin-támogatás](gremlin-support.md) segítségével további lehetőségeket ismerhet meg.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Megismerte, hogyan végezhet lekérdezéseket a Graph használatával 

A Cosmos DB-vel kapcsolatos további információkért folytassa az Alapfogalmak szakasz áttekintésével.

> [!div class="nextstepaction"]
> [Globális terjesztés](distribute-data-globally.md) 

