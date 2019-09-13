---
title: Azure Cosmos DB Gremlin korlátai
description: A Graph Engine futásidejű korlátozásait ismertető dokumentáció
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: f1fe4cfac22a651f44338986d0a767fe3e1a360b
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911077"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin korlátai
Ez a cikk a Azure Cosmos DB Gremlin motor korlátairól beszél, és ismerteti, hogy miként befolyásolhatják az ügyfelek bejárásokat.

Cosmos DB a Gremlin a Cosmos DB infrastruktúrára épül, ezért a [Azure Cosmos db](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) összes korlátja továbbra is érvényes. 

## <a name="limits"></a>Korlátok

Ha elérte a Gremlin korlátot, a bejárási művelet megszakadt az **x-MS-Status-Code** = 429, amely sávszélesség-szabályozási hibát jelez.

**Erőforrás**    | **Alapértelmezett korlát** | **Magyarázat**
--- | --- | ---
*Memória/kérelem* | **2 GB** | A maximális memória, amelyet a kérések a feldolgozás során felhasználhatnak. A nagyméretű adatkészletek kiszámításához szükséges kérelmek további memóriát fognak használni. Vegye figyelembe a kisebb adatkészletekre irányuló kérelmeket, hogy elkerülje a korlát átlépését vagy OLAP-megoldások használatát.
*Parancsfájl hossza* | **64 KB** | Gremlin bejárási szkript maximális hossza kérelmenként.
*Kezelő mélysége* | **400** |  A bejárások során megtett egyedi lépések teljes száma. Például ```g.V().out()``` az operátorok száma 2: A V () és a out ( ```g.V('label').repeat(out()).times(100)``` ) operátor 3. mélységű: A V (), a REPEAT () és a out ( ```.times(100)``` ), mert az ```.repeat()``` operátor paramétere.
*Párhuzamosság szintje* | **32** | A tárolási partíciók maximális száma a tárolási réteg felé küldött egyes kérelmekben. Ezt a korlátot a több száz partíciót tartalmazó gráfok is érintik.
*Ismétlési korlát* | **32** | A ```.repeat()``` operátor által végrehajtható iterációk maximális száma. A legtöbb esetben minden ```.repeat()``` egyes iteráció a szélesség – első bejárást futtatja, ami azt jelenti, hogy minden bejárás a csúcspontok között legfeljebb 32 ugrásra van korlátozva.
*Bejárási időkorlát* | **30 másodperc** | A bejárást a rendszer megszakítja, ha ez meghaladja ezt az időt. A Cosmos DB-gráf egy OLTP-adatbázis, amelynek bejárásai többségében néhány ezredmásodperc alatt befejeződnek. Az OLAP-lekérdezések Cosmos DB gráfon való futtatásához használja a [Graph adatkereteket](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) és a [Cosmos db Spark-összekötőt](https://github.com/Azure/azure-cosmosdb-spark)tartalmazó [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) .
*Predikátum korlátja* | **20** | Az egyetlen csúcspontra vagy élre alkalmazott ```.has()``` vagy ```.hasNot()``` lépések száma. A korlát elérésekor a következő hiba jelenik meg az alkalmazásban: ```The SQL query exceeded the maximum number of joins. The allowed limit is 20```. Ez egy ideiglenes kényelmetlenség, mivel a csapat dolgozik a korlát emelésén. 
*Tétlen kapcsolat időtúllépése* | **5 óra** | A gráf-kiszolgáló mennyisége az adatforgalom nélkül megnyitva marad a WebSocket-kapcsolaton. A TCP Keep-Alive csomagok vagy a HTTP Keep-Alive-kérelmek nem terjesztik le az élettartamot ezen a korláton túl, de ha nem küldik el, akkor a mögöttes Azure-infrastruktúra még hamarabb lezárja a kapcsolatokat. Cosmos DB gráf-kezelő úgy gondolja, hogy tétlen, ha nem fut Gremlin-bejárásokat.
*Erőforrás-token/óra* | **100** | A Gremlin-ügyfelek által az adott régióban Gremlin-fiókhoz való kapcsolódáshoz használt egyedi erőforrás-jogkivonatok száma. Ha az alkalmazás meghaladja az óránkénti egyedi jogkivonat `"Exceeded allowed resource token limit of 100 that can be used concurrently"` -korlátot, a rendszer a következő hitelesítési kérelemben adja vissza.

## <a name="next-steps"></a>További lépések
* [Azure Cosmos DB Gremlin-válasz fejlécei](gremlin-headers.md) 
* [Erőforrás-tokenek Azure Cosmos DB Gremlin](how-to-use-resource-tokens-gremlin.md)
