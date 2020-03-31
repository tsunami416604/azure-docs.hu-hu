---
title: Adatparticionálás az Azure Cosmos DB Gremlin API-ban
description: Megtudhatja, hogyan használhatja a particionált grafikont az Azure Cosmos DB-ben. Ez a cikk a particionált grafikon követelményeit és ajánlott eljárásokat is ismerteti.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623372"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Particionált gráfok használata az Azure Cosmos DB-ben

Az Azure Cosmos DB Gremlin API-jának egyik legfontosabb jellemzője a nagyméretű grafikonok vízszintes skálázáson keresztüli kezelése. A tárolók a tárolás és az átviteli érték tekintetében egymástól függetlenül skálázhatók. Az Azure Cosmos DB-ben hozhat létre tárolókat, amelyek automatikusan méretezhetők a gráfadatok tárolásához. Az adatok automatikusan kiegyensúlyozó alapján a megadott **partíciókulcs**.

**Particionálás szükséges,** ha a tároló várhatóan tárolni több mint 20 GB méretű, vagy ha azt szeretné, hogy lefoglalja több mint 10 000 kérelem egység másodpercenként (RT). Az [Azure Cosmos DB particionálási mechanizmus](partition-data.md) ugyanazok at alkalmaznak az alábbiakban ismertetett néhány graph-specifikus optimalizálással.

![Graph particionálás.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Gráf particionálási mechanizmusa

Az alábbi irányelvek ismertetik, hogyan működik a particionálási stratégia az Azure Cosmos DB-ben:

- **A csúcsok és az élek json-dokumentumként tárolódnak.**

- **A csúcspontokhoz partíciókulcs szükséges.** Ez a kulcs határozza meg, hogy a csúcspont melyik partíción lesz tárolva kivonatoló algoritmuson keresztül. A partíciókulcs tulajdonságneve új tároló létrehozásakor van definiálva, és formátuma van: `/partitioning-key-name`.

- Az élek a **forráscsúcsgal együtt lesznek tárolva.** Más szóval, minden csúcspont nál a partíciókulcs határozza meg, hogy hol tárolják őket a kimenő élek mellett. Ez az optimalizálás a partíciók közötti `out()` lekérdezések elkerülése érdekében a számosság diagram lekérdezések használata esetén.

- **Az élek a csúcsokra mutató csúcsokra mutató hivatkozásokat tartalmaznak.** Az összes él a partícióbillentyűkkel és az ondópontok azonosítóival van tárolva. Ez a számítás `out()` teszi az összes iránylekérdezések mindig egy hatókörrel particionált lekérdezés, és nem egy vak partícióközi lekérdezés. 

- **A gráflekérdezéseknek meg kell adniuk egy partíciókulcsot.** Az Azure Cosmos DB horizontális particionálásának teljes kihasználásához a partíciókulcsot meg kell adni, ha egyetlen csúcspont van kiválasztva, amikor csak lehetséges. A particionált diagramon egy vagy több csúcs kiválasztására szolgáló lekérdezések a következők:

    - `/id`és `/label` nem támogatott partíciós kulcsok egy tároló Gremlin API-ban.


    - Csúcspont kiválasztása azonosító szerint, majd **a `.has()` lépés sel a partíciókulcs tulajdonságának megadása:** 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Csúcspont kiválasztása a **partíciókulcs értékét és azonosítóját tartalmazó függő megadásával**: 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - **Partíciókulcs-értékek és -azonosítók tuple-jainak tömbjének**megadása:
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Csúcsok készletének kiválasztása az azonosítóikkal együtt, és **a partíciókulcs-értékek listájának megadása**: 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - A **partíciós stratégia** használata a lekérdezés elején, és a Gremlin-lekérdezés többi részének hatókörének partíciójának megadása: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Gyakorlati tanácsok particionált grafikon használata esetén

Az alábbi irányelvek segítségével biztosítható a teljesítmény és a méretezhetőség, ha korlátlan tárolókat tartalmazó particionált grafikonokat használ:

- **Csúcspont lekérdezésekéneknél mindig adja meg a partíciókulcs értékét.** A csúcspont ismert partícióról való leelső szerzése a teljesítmény elérésének egyik módja. Minden további szomszédos művelet hatóköre mindig egy partícióra lesz hatóköre, mivel az Élek hivatkozási azonosítót és partíciókulcsot tartalmaz a célcsúcsokhoz.

- **A kimenő irány használata élek lekérdezésekénélek lekérdezésekénélek lekérdezésekénéléseksorán, amikor csak lehetséges.** Mint már említettük, élek tárolja a forrás csúcsok a kimenő irányba. Így az esélyek a partíciók közötti lekérdezések minimálisra csökken, ha az adatok és lekérdezések tervezték ezt a mintát szem előtt tartva. Éppen ellenkezőleg, `in()` a lekérdezés mindig egy drága fan-out lekérdezést.

- **Válasszon olyan partíciókulcsot, amely egyenletesen osztja el az adatokat a partíciók között.** Ez a döntés nagymértékben függ a megoldás adatmodelljétől. További információ a megfelelő partíciókulcs létrehozásáról [az Azure Cosmos DB particionálási és méretezési szolgáltatásában.](partition-data.md)

- **A lekérdezések optimalizálása a partíció határain belüli adatok beszerzéséhez.** Az optimális particionálási stratégia a lekérdezési mintákhoz igazodna. Az egyetlen partícióról adatokat beszerző lekérdezések a lehető legjobb teljesítményt nyújtják.

## <a name="next-steps"></a>További lépések

Ezután folytassa a következő cikkek elolvasását:

* Ismerje meg [a partícióés a méretezés az Azure Cosmos DB.](partition-data.md)
* További információ a [Gremlin-támogatásról a Gremlin API-ban.](gremlin-support.md)
* További információ [a Gremlin API bevezetésről.](graph-introduction.md)
