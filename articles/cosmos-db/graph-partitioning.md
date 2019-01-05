---
title: Az Azure Cosmos DB Gremlin API adatparticionálás
description: Ismerje meg, hogyan használhatja egy particionált graph Azure Cosmos DB-ben. Ez a cikk is ismerteti a követelmények és ajánlott eljárások a particionált grafikon.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f1e486a302b440d819e15ef86f8d76ea5e50d201
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036324"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Az Azure Cosmos DB egy particionált graph használatával

Az Azure Cosmos DB Gremlin API-legfontosabb funkcióit egyik képes kezelni a nagyméretű gráfok vízszintes méretezés során. Horizontális skálázás a gazdafájlon keresztül, a [képességek az Azure Cosmos DB particionálási](partition-data.md). A tárolók egymástól függetlenül méretezhetők a tárolás és átviteli sebesség tekintetében. Létrehozhat tárolókat az Azure Cosmos dB-ben, amely a grafikon adatainak tárolásához automatikusan skálázhatók. Az adatok automatikusan kiegyensúlyozott alapján a megadott **partíciókulcs**.

Ebben a dokumentumban a gráf típusú adatbázisokat particionáltak hogyan adatait a következmények mindkét csúcspontok (vagy csomópontok) együtt leírását és élek.

## <a name="requirements-for-partitioned-graph"></a>A particionált graph követelményei

Részletek, amelyek egy particionált gráftárolót létrehozásakor konfigurálná a következők:

- **A particionálás kötelező** Ha várhatóan a tároló tárolja a több mint 10 GB méretű, vagy ha szeretne lefoglalni a több mint 10 000 kérelemegység / másodperc (RU-k).

- **Csúcsok és élek egyaránt JSON-dokumentumok formájában tárolja**.

- **Csúcspontok szükséges partíciókulcsot**. Ez a kulcs határozza meg, melyik partícióba a csúcspont egy kivonatoló algoritmus használatával tárolhatja. A partíciós kulcs neve szóközt és speciális karakterek nélküli egyszavas karakterláncok. A partíciós kulcs van definiálva, amikor egy új tárolót hoz létre, és a egy formátuma: `/partitioning-key-name`.

- **Élek tárolja a rendszer a forráscsúcspont**. Minden csúcspont számára más szóval a partíciókulcs meghatározása mellett a kimenő szélei találhatók. Ez azért történt, elkerülheti a partícióra kiterjedő lekérdezések használata esetén a `out()` számossága a graph-lekérdezéseket.

- **Graph-lekérdezések meg kell adni egy partíciókulcsot**. Teljes mértékben kihasználhatja a vízszintes particionálás az Azure Cosmos DB, a partíciókulcs meg kell határozni egy egyetlen csúcspont kiválasztásakor, ha lehetséges. Egy vagy több csúcspontok kiválasztásakor egy particionált gráfon lekérdezések a következők:

    - `/id` és `/label` egy tárolóhoz, a Gremlin API partíciókulcsok nem támogatottak.


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

Teljesítményének és méretezhetőségének biztosításához a korlátlan tárolókkal particionált diagramok használata esetén használja az alábbi útmutatást:

- **Mindig adja meg a partíciókulcs-értékkel csúcs lekérdezésekor**. Csúcspont lekérése egy ismert partíció módja a teljesítmény eléréséhez.

- **Használja a kimenő irányban élek lekérdezésekor, ha lehetséges**. Amint már említettük, a élek együtt a forrás csúcspontok a kimenő irányban vannak tárolva. Így a szoftver-és hibahivatkozások foka partíciók közötti lekérdezések, így a lehető legkisebb, amikor az adatok és a lekérdezések terveztük, vegye figyelembe ezt a mintát.

- **Válassza ki a partíciókulcs egyenletesen között az adatokat több partícióra kiterjedő**. Ezt a döntést az adatmodellt, a megoldás az erősen függ. További információk a létrehozása egy, a megfelelő partíciókulcs [particionálás és skálázás az Azure Cosmos DB](partition-data.md).

- **Szerezhetők be adatok egy partíció határain belül-lekérdezések optimalizálása**. Az optimális particionálási stratégia szeretné igazítani a lekérdezési minták. Szerezze be az adatokat egyetlen partícióról lekérdezéseket a lehető legjobb teljesítményt nyújtanak.

## <a name="next-steps"></a>További lépések

Ezután folytassa a következő cikkekben:

* Ismerje meg [particionálási és horizontális az Azure Cosmos DB](partition-data.md).
* További információ a [Gremlin-támogatás a Gremlin API](gremlin-support.md).
* Ismerje meg [Gremlin API bemutatása](graph-introduction.md).
