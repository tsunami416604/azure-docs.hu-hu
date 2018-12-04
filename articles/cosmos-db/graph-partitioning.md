---
title: Az Azure Cosmos DB Gremlin API particionálása
description: Ismerje meg, hogyan használhatja egy particionált Graph Azure Cosmos DB-ben.
services: cosmos-db
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 27b0d9d7ca22ba346dbc288020f704dc7d27aa6a
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837196"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Az Azure Cosmos DB egy particionált graph használatával

Az Azure Cosmos DB Gremlin API-legfontosabb funkcióit egyik képes kezelni a nagyméretű gráfok horizontális skálázhatóság révén. Ez a folyamat a gazdafájlon keresztül, a [képességek az Azure Cosmos DB particionálási](partition-data.md), amely győződjön meg arról, használja a tárolók, amelyek egymástól függetlenül méretezhetők a tárolás és átviteli sebesség tekintetében. Az Azure Cosmos DB támogatja a következő típusú tárolókat minden API-k között:

- **Rögzített tárolót**: ezek a tárolók tárolhatja egy graph legfeljebb 10 GB-nál, legfeljebb 10 000 kérelemegység / másodperc számára lefoglalt adatbázis. Rögzített tároló létrehozásához meg kell határozni egy partíció kulcstulajdonság az adatok nem.

- **A korlátlan tároló**: ezek a tárolók automatikusan skálázhatja meghaladja a 10 GB-os korlátot horizontális particionálás segítségével egy graph tárolásához. Mindegyik partíció 10 GB-ot fogja tárolni, és az adatok lesz automatikusan kiegyensúlyozott alapján a **megadott partíciókulcs**, amely egy kötelező paraméter lesz a korlátlan tároló használata esetén. Az ilyen típusú gyakorlatilag korlátlan adatméret képes tárolni, és lehetővé teszi akár 100 000 kérelemegység / másodperc, vagy további [támogatási szolgálatával](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request).

Ebben a dokumentumban a gráf típusú adatbázisokat particionáltak hogyan adatait a következmények mindkét csúcspontok (vagy csomópontok) együtt leírását és élek.

## <a name="requirements-for-partitioned-graph"></a>A particionált graph követelményei

Részletek, amelyek egy particionált gráftárolót létrehozásakor konfigurálná a következők:

- **Particionálás beállításához szükséges lesz** , ha a tároló várhatóan több mint 10 GB méretű, illetve ha több mint 10 000 kérelemegység / másodperc (RU/s) lefoglalása lesz szükség.

- **Csúcsok és élek egyaránt JSON-dokumentumok formájában tárolja** a a háttéralkalmazás egy Azure Cosmos DB Gremlin API-tárolóhoz.

- **Csúcspontok szükséges partíciókulcsot**. Ez a kulcs határozza meg, melyik partícióba a csúcspont egy kivonatoló algoritmus használatával tárolhatja. A partíciós kulcs neve szóközt és speciális karakterek nélküli egyszavas karakterláncok, és amikor hoz létre egy új tárolót, a következő formátumban van definiálva `/partitioning-key-name` a portálon.

- **Élek tárolja a rendszer a forráscsúcspont**. Más szóval minden csúcspont számára a partíciós kulcs határozza meg azokat a rendszer hol tárolja a kimenő szélei együtt. Ez azért történt, elkerülheti a partícióra kiterjedő lekérdezések használata esetén a `out()` számossága a graph-lekérdezéseket.

- **Graph-lekérdezések meg kell adni egy partíciókulcsot**. Teljes mértékben kihasználhatja a vízszintes particionálás az Azure Cosmos DB, a partíciókulcs meg kell határozni egy egyetlen csúcspont kiválasztásakor, ha lehetséges. Egy vagy több csúcspontok kiválasztásakor egy particionált gráfon lekérdezések a következők:

    - `/id` és `/label` nem támogatottak, egy tárolóhoz, a Gremlin API partíciókulcsok...


    - Csúcs azonosítója, majd kiválasztja **használatával a `.has()` lépéssel megadhatja azokat a partíciós kulcs tulajdonságát**: 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - A csúcspont kiválasztásával **egy rekord, beleértve a partíciókulcs-értékkel, és az azonosító megadása**: 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Adjon meg egy **partíciókulcs és azonosítók rekordokat tartalmazó tömb**:
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Csúcspontok készletét kiválasztása és **partíciókulcs-értékek listáját megadó**: 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Ajánlott eljárások, amikor egy particionált graph használatával

Az alábbiakban útmutatást, amelyet a leghatékonyabb teljesítményének és méretezhetőségének biztosításához a korlátlan tárolókra particionált diagramok használata esetén:

- **Mindig adja meg a partíciókulcs-értékkel csúcs lekérdezésekor**. Csúcs beszerzése egy ismert partíció a teljesítmény tekintetében a leghatékonyabb módja.

- **Használja a kimenő irányban élek lekérdezésekor, ha lehetséges**. Amint már említettük, a élek együtt a forrás csúcspontok a kimenő irányban vannak tárolva. Ez azt jelenti, hogy a szoftver-és hibahivatkozások foka partíciók közötti lekérdezések, amikor az adatok és a lekérdezések terveztük, vegye figyelembe ezt a mintát kis méretben.

- **Válassza ki a partíciókulcs egyenletesen között az adatokat több partícióra kiterjedő**. Ezt a döntést az adatmodellt, a megoldás az erősen függ. További információk a létrehozása egy, a megfelelő partíciókulcs [particionálás és skálázás az Azure Cosmos DB](partition-data.md).

- **Szerezhetők be adatok egy partíciót, ha lehetséges határain belül-lekérdezések optimalizálása**. Az optimális particionálási stratégia szeretné igazítani a lekérdezési minták. Szerezze be az adatokat egyetlen partícióról lekérdezéseket a lehető legjobb teljesítményt nyújtanak.

## <a name="next-steps"></a>További lépések
Ebben a cikkben áttekintést fogalmak és ajánlott eljárások a particionálás az Azure Cosmos DB Gremlin API lett megadva. 

* Ismerje meg [particionálási és horizontális az Azure Cosmos DB](partition-data.md).
* További információ a [Gremlin-támogatás a Gremlin API](gremlin-support.md).
* Ismerje meg [Gremlin API bemutatása](graph-introduction.md).
