---
title: Azure Cosmos DB Gremlin korlátai
description: A Graph Engine futásidejű korlátozásait ismertető dokumentáció
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "72029849"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Az Azure Cosmos DB Gremlin korlátai
Ez a cikk a Azure Cosmos DB Gremlin motor korlátairól beszél, és ismerteti, hogy miként befolyásolhatják az ügyfelek bejárásokat.

Cosmos DB Gremlin Cosmos DB-infrastruktúrára épül. Ennek következtében a [Azure Cosmos db szolgáltatási korlátokban](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) ismertetett összes korlát továbbra is érvényes. 

## <a name="limits"></a>Korlátok

Ha elérte a Gremlin korlátot, a bejárási művelet megszakad a 429 **x-MS-Status-Code** értékkel, amely egy sávszélesség-szabályozási hibát jelez. További információért lásd: [Gremlin Server Response headers](gremlin-limits.md) .

**Erőforrás**    | **Alapértelmezett korlát** | **Magyarázat**
--- | --- | ---
*Parancsfájl hossza* | **64 KB** | Gremlin bejárási szkript maximális hossza kérelmenként.
*Kezelő mélysége* | **400** |  A bejárások során megtett egyedi lépések teljes száma. Például ```g.V().out()``` az operátorok száma 2: v () és ki (), ```g.V('label').repeat(out()).times(100)``` a kezelő mélysége 3: v (), REPEAT () és out (), mert ```.times(100)``` a paraméter az ```.repeat()``` operátor.
*Párhuzamosság szintje* | **32** | A tárolási partíciók maximális száma a tárolási réteg felé küldött egyes kérelmekben. Ezt a korlátot a több száz partíciót tartalmazó gráfok is érintik.
*Ismétlési korlát* | **32** | A ```.repeat()``` operátor által végrehajtható iterációk maximális száma. A legtöbb esetben minden ```.repeat()``` egyes iteráció a szélesség – első bejárást futtatja, ami azt jelenti, hogy minden bejárás a csúcspontok között legfeljebb 32 ugrásra van korlátozva.
*Bejárási időkorlát* | **30 másodperc** | A bejárást a rendszer megszakítja, ha ez meghaladja ezt az időt. A Cosmos DB-gráf egy OLTP-adatbázis, amelynek bejárásai többségében néhány ezredmásodperc alatt befejeződnek. Az OLAP-lekérdezések Cosmos DB gráfon való futtatásához használja a [Graph adatkereteket](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) és a [Cosmos db Spark-összekötőt](https://github.com/Azure/azure-cosmosdb-spark)tartalmazó [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) .
*Tétlen kapcsolat időkorlátja* | **1 óra** | Az a időtartam, ameddig a Gremlin szolgáltatás megnyitva tartja az üresjáratban lévő WebSocket-kapcsolatokat. A TCP Keep-Alive csomagok vagy a HTTP Keep-Alive kérelmek nem bővítik a kapcsolatok élettartamát ezen a korláton túl. Cosmos DB gráf-kezelő úgy véli, hogy a WebSocket-kapcsolatok üresjáratban vannak, ha nem fut aktív Gremlin-kérelem.
*Erőforrás-jogkivonat óránként* | **100** | A Gremlin-ügyfelek által egy régióban lévő Gremlin-fiókhoz való csatlakozáshoz használt egyedi erőforrás-jogkivonatok száma. Ha az alkalmazás túllépi az óránkénti egyedi token `"Exceeded allowed resource token limit of 100 that can be used concurrently"` korlátot, a rendszer a következő hitelesítési kérelemben adja vissza.

## <a name="next-steps"></a>További lépések
* [Azure Cosmos DB Gremlin-válasz fejlécei](gremlin-headers.md) 
* [Erőforrás-tokenek Azure Cosmos DB Gremlin](how-to-use-resource-tokens-gremlin.md)
