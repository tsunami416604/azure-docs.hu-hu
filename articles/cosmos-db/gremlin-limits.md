---
title: Az Azure Cosmos DB Gremlin korlátai
description: Referenciadokumentáció a Graph motor futásidejű korlátaihoz
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72029849"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Az Azure Cosmos DB Gremlin korlátai
Ez a cikk az Azure Cosmos DB Gremlin motor korlátairól szól, és ismerteti, hogyan befolyásolhatják az ügyfelek bejárásait.

Cosmos DB Gremlin épül tetején Cosmos DB infrastruktúra. Ennek köszönhetően az Azure [Cosmos DB szolgáltatáskorlátokban](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) ismertetett összes korlátozás továbbra is érvényben van. 

## <a name="limits"></a>Korlátok

A Gremlin-korlát elérésekor a bejárás megszakad egy **429-es x-ms-állapotkóddal,** amely szabályozási hibát jelez. További információt a [Gremlin szerverválasz-fejlécekben](gremlin-limits.md) talál.

**Erőforrás**    | **Alapértelmezett korlát** | **Magyarázat**
--- | --- | ---
*Parancsfájl hossza* | **64 KB** | Gremlin bejárási szkript maximális hossza kérelmenként.
*Kezelőmélysége* | **400** |  A bejárások során megtett egyedi lépések teljes száma. Például ```g.V().out()``` 2: V() és out() operátori számmal rendelkezik, ```g.V('label').repeat(out()).times(100)``` amelynek operátori mélysége ```.times(100)``` 3: V(), repeat(), és out(), mert az operátor paramétere. ```.repeat()```
*Párhuzamosság szintje* | **32** | A tárolási partíciók maximális száma a tárolási réteg felé küldött egyes kérelmekben. Ez a korlát hatással lesz a több száz partícióval rendelkező grafikonokra.
*Ismétlési korlát* | **32** | A ```.repeat()``` operátor által végrehajtható iterációk maximális száma. A lépés minden ```.repeat()``` egyes iterációja a legtöbb esetben szélesség-első bejárást futtat, ami azt jelenti, hogy minden átjárás legfeljebb 32 ugrásra korlátozódik a csúcspontok között.
*Bejárási időelés* | **30 másodperc** | A bejárás megszakad, ha túllépi ezt az időt. A Cosmos DB-gráf egy OLTP-adatbázis, amelynek bejárásai többségében néhány ezredmásodperc alatt befejeződnek. OLAP-lekérdezések futtatásához a Cosmos DB Graph használatával használja az [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) [graph adatkeretekkel](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) és a [Cosmos DB Spark Connector.](https://github.com/Azure/azure-cosmosdb-spark)
*Tétlen kapcsolat időkorlátja* | **1 óra** | A Gremlin szolgáltatás nyitva tartja az alapjárati websocket-kapcsolatokat. A TCP életben tartó csomagok vagy a HTTP-életben tartás kérések nem hosszabbítják meg a kapcsolat élettartamát ezen a korláton túl. A Cosmos DB Graph motor úgy véli, hogy a websocket-kapcsolatok tétlenek, ha nincsenek rajta aktív Gremlin-kérelmek.
*Erőforrás-jogkivonat óránként* | **100** | A Gremlin-ügyfelek által egy régióban lévő Gremlin-fiókhoz való csatlakozáshoz használt egyedi erőforrás-jogkivonatok száma. Ha az alkalmazás túllépi az `"Exceeded allowed resource token limit of 100 that can be used concurrently"` óránkénti egyedi jogkivonat-korlátot, a következő hitelesítési kérelem reklamációja kerül vissza.

## <a name="next-steps"></a>További lépések
* [Az Azure Cosmos DB Gremlin válaszfejlécei](gremlin-headers.md) 
* [Az Azure Cosmos DB erőforrástokenek Gremlinnel](how-to-use-resource-tokens-gremlin.md)
