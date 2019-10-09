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
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029849"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin korlátai
Ez a cikk a Azure Cosmos DB Gremlin motor korlátairól beszél, és ismerteti, hogy miként befolyásolhatják az ügyfelek bejárásokat.

Cosmos DB Gremlin Cosmos DB-infrastruktúrára épül. Ennek következtében a [Azure Cosmos db szolgáltatási korlátokban](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) ismertetett összes korlát továbbra is érvényes. 

## <a name="limits"></a>Korlátok

Ha elérte a Gremlin korlátot, a bejárási művelet megszakad a 429 **x-MS-Status-Code** értékkel, amely egy sávszélesség-szabályozási hibát jelez. További információért lásd: [Gremlin Server Response headers](gremlin-limits.md) .

**Erőforrás**    | **Alapértelmezett korlát** | **Magyarázat**
--- | --- | ---
*Szkript hossza* | **64 KB** | Gremlin bejárási szkript maximális hossza kérelmenként.
*Operátormélység* | **400** |  A bejárások során megtett egyedi lépések teljes száma. Például a ```g.V().out()``` operátor száma 2: V () és out (), ```g.V('label').repeat(out()).times(100)``` a kezelő mélysége 3: V (), REPEAT () és out (), mert a ```.times(100)``` paraméter ```.repeat()``` operátornak.
*Párhuzamosság szintje* | **32** | A tárolási partíciók maximális száma a tárolási réteg felé küldött egyes kérelmekben. Ezt a korlátot a több száz partíciót tartalmazó gráfok is érintik.
*Ismétlési korlát* | **32** | A ```.repeat()``` operátor által végrehajtható iterációk maximális száma. A legtöbb esetben a ```.repeat()``` lépés minden iterációja a szélesség – első bejárást futtatja, ami azt jelenti, hogy minden bejárás a csúcspontok között legfeljebb 32 ugrásra van korlátozva.
*Bejárási időtúllépés* | **30 másodperc** | A bejárást a rendszer megszakítja, ha ez meghaladja ezt az időt. A Cosmos DB-gráf egy OLTP-adatbázis, amelynek bejárásai többségében néhány ezredmásodperc alatt befejeződnek. Az OLAP-lekérdezések Cosmos DB gráfon való futtatásához használja a [Graph adatkereteket](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) és a [Cosmos db Spark-összekötőt](https://github.com/Azure/azure-cosmosdb-spark)tartalmazó [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) .
*Tétlen kapcsolat időkorlátja* | **1 óra** | Az a időtartam, ameddig a Gremlin szolgáltatás megnyitva tartja az üresjáratban lévő WebSocket-kapcsolatokat. A TCP Keep-Alive csomagok vagy a HTTP Keep-Alive kérelmek nem bővítik a kapcsolatok élettartamát ezen a korláton túl. Cosmos DB gráf-kezelő úgy véli, hogy a WebSocket-kapcsolatok üresjáratban vannak, ha nem fut aktív Gremlin-kérelem.
*Erőforrás-jogkivonat óránként* | **100** | A Gremlin-ügyfelek által egy régióban lévő Gremlin-fiókhoz való csatlakozáshoz használt egyedi erőforrás-jogkivonatok száma. Ha az alkalmazás túllépi az óránkénti egyedi token-korlátot, a rendszer `"Exceeded allowed resource token limit of 100 that can be used concurrently"` értéket ad vissza a következő hitelesítési kérelemnél.

## <a name="next-steps"></a>További lépések
* [Azure Cosmos DB Gremlin-válasz fejlécei](gremlin-headers.md) 
* [Erőforrás-tokenek Azure Cosmos DB Gremlin](how-to-use-resource-tokens-gremlin.md)
