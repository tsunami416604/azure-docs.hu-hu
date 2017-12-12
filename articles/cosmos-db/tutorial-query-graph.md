---
title: "Hogyan lehet lekérdezni az Azure Cosmos Adatbázisba Diagramadatok? | Microsoft Docs"
description: "Ismerje meg, hogyan graph adatait az Azure Cosmos-Adatbázisba"
services: cosmos-db
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: 
tags: 
ms.assetid: 8bde5c80-581c-4f70-acb4-9578873c92fa
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 358f2650f767057f4a220d3b3b8507de4ce14e6f
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-cosmos-db-how-to-query-with-the-graph-api-preview"></a>Azure Cosmos DB: Hogyan lekérdezése Graph API-t (előzetes verzió)?

Az Azure Cosmos DB [Graph API](graph-introduction.md) (előzetes verzió) támogatja a [Gremlin](https://github.com/tinkerpop/gremlin/wiki) lekérdezések. Ez a cikk ismerteti a minta dokumentumok és lekérdezések az első lépésekhez. A részletes Gremlin hivatkozás megtalálható a [Gremlin támogatási](gremlin-support.md) cikk.

Ez a cikk ismerteti a következő feladatokat: 

> [!div class="checklist"]
> * Gremlin az adatok lekérdezése

## <a name="prerequisites"></a>Előfeltételek

A lekérdezések működéséhez kell rendelkezik Azure Cosmos DB fiókkal, és a tárolóban lévő adatok graph rendelkezik. Nem rendelkezik egyetlen, az? Fejezze be a [5 perces gyors üzembe helyezés](create-graph-dotnet.md) vagy a [fejlesztői útmutató](tutorial-query-graph.md) hozzon létre egy fiókot, és töltse fel az adatbázist. Használja a következő lekérdezéseket is futtathat a [Azure Cosmos DB .NET graph könyvtár](graph-sdk-dotnet.md), [Gremlin konzol](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console), vagy a kedvenc Gremlin illesztőprogramot.

## <a name="count-vertices-in-the-graph"></a>A grafikonon száma csúcsban

A következő kódrészletet a grafikonon csúcsban számát mutatja be:

```
g.V().count()
```

## <a name="filters"></a>Szűrők

Szűrők Gremlin tartozó használatával végezheti el `has` és `hasLabel` lépéseit, és egyesíthet használatával `and`, `or`, és `not` összetettebb szűrők létrehozásához. Azure Cosmos DB biztosít a csúcsban és gyors lekérdezések fok belül az összes tulajdonság séma-független indexelése:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Leképezése

Kivetítheti az egyes tulajdonságok a lekérdezés eredményében használatával a `values` . lépés:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Kapcsolódó szélek és csúcsban keresése

Eddig is csak láttuk lekérdezési operátorok, amelyek működnek a bármely adatbázis. Diagramok esetén gyors és hatékony átjárás műveleteihez kapcsolódó szélek és csúcsban keresse meg kell. Keressük Thomas összes barátok. Jelenleg ezt úgy teheti meg Gremlin `outE` keresheti meg az összes lépést a Thomas, majd a használatával Gremlin tartozó szegélyek a a-csúcsban bejárása a kimenő éleinek `inV` . lépés:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

A következő lekérdezés hajtja végre két ugrások található összes Thomas' "ismerősök olyan ismerőseinek", a függvény meghívásával `outE` és `inV` kétszer. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Összetettebb lekérdezések létrehozhatja és hatékony graph átjárás logika Gremlin, beleértve a keverése szűrőkifejezéseket, ismétlési használatával végez használatával valósítja meg a `loop` lépés, és végrehajtási feltételes navigációs használ a `choose` lépés. További információ a teendők [Gremlin támogatási](gremlin-support.md)!

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban ezt a következők:

> [!div class="checklist"]
> * Megtudta, hogyan lekérdezés Graph használatával 

Most már folytathatja a következő oktatóanyag megtudhatja, miként ossza el az adatokat globális.

> [!div class="nextstepaction"]
> [Az adatok globálisan terjesztése](tutorial-global-distribution-documentdb.md)
