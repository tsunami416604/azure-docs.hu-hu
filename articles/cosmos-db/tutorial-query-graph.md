---
title: Hogyan kérdezhetők le gráfadatok az Azure Cosmos DB-ben? | Microsoft Docs
description: Megismerheti, hogyan kérdezhetők le gráfadatok az Azure Cosmos DB-ben
services: cosmos-db
author: luisbosquez
manager: kfile
editor: ''
tags: ''
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: dfaf3fe1427c9069939f5421c6a87efb584ebe48
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763204"
---
# <a name="tutorial-query-azure-cosmos-db-graph-api-by-using-gremlin"></a>Oktatóanyag: Azure Cosmos DB Graph API lekérdezése a Gremlin használatával

Az Azure Cosmos DB [Graph API](graph-introduction.md) támogatja a [Gremlin](https://github.com/tinkerpop/gremlin/wiki)-lekérdezéseket. Ez a cikk mintadokumentumokat és -lekérdezéseket tartalmaz a kezdéshez. A [Gremlin-támogatási](gremlin-support.md) cikkben talál részletes Gremlin-referenciát.

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Adatok lekérdezése a Gremlin használatával

## <a name="prerequisites"></a>Előfeltételek

A lekérdezések működéséhez Azure Cosmos DB-fiókkal kell rendelkeznie, és a tárolóban gráfadatoknak kell lennie. Nem rendelkezik ezekkel? A fiók létrehozásához és az adatbázis feltöltéséhez végezze el az [5 perces gyors útmutatót](create-graph-dotnet.md) vagy a [fejlesztői oktatóanyagot](tutorial-query-graph.md). A következő lekérdezéseket futtathatja az [Azure Cosmos DB .NET gráfkódtárával](graph-sdk-dotnet.md), a [Gremlin-konzollal](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) vagy a kedvenc Gremlin-illesztőjével.

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

Továbbléphet a következő oktatóanyagra, amelyben megismerheti, hogyan terjesztheti az adatait globálisan.

> [!div class="nextstepaction"]
> [Globális adatterjesztés](tutorial-global-distribution-graph.md)
