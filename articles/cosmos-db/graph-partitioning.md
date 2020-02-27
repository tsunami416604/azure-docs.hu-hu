---
title: Az Azure Cosmos DB Gremlin API adatparticionálás
description: Ismerje meg, hogyan használhatja egy particionált graph Azure Cosmos DB-ben. Ez a cikk is ismerteti a követelmények és ajánlott eljárások a particionált grafikon.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623372"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Az Azure Cosmos DB egy particionált graph használatával

Az Azure Cosmos DB Gremlin API-legfontosabb funkcióit egyik képes kezelni a nagyméretű gráfok vízszintes méretezés során. A tárolók egymástól függetlenül méretezhetők a tárolás és átviteli sebesség tekintetében. Létrehozhat tárolókat az Azure Cosmos dB-ben, amely a grafikon adatainak tárolásához automatikusan skálázhatók. Az adatértékek a megadott **partíciós kulcs**alapján automatikusan egyensúlyban vannak.

A **particionálásra akkor van szükség** , ha a tároló várhatóan több mint 20 GB-ot tárol, vagy ha több mint 10 000 adategységet szeretne lefoglalni másodpercenként (RUs). A [Azure Cosmos db particionálási mechanizmussal](partition-data.md) azonos általános elvek vonatkoznak néhány, az alábbiakban ismertetett gráf-specifikus optimalizálásra.

![Gráf particionálás.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Gráf particionálási mechanizmusa

Az alábbi irányelvek azt írják le, hogyan működik a Azure Cosmos DB particionálási stratégiája:

- **A csúcspontokat és az éleket JSON-dokumentumként tárolja a rendszer**.

- **A csúcspontok partíciós kulcsot igényelnek**. Ez a kulcs határozza meg, melyik partícióba a csúcspont egy kivonatoló algoritmus használatával tárolhatja. A Partition Key tulajdonság neve új tároló létrehozásakor van meghatározva, és formátuma: `/partitioning-key-name`.

- Az **élek a forrás csúcspontjának megfelelően lesznek tárolva**. Minden csúcspont számára más szóval a partíciókulcs meghatározása mellett a kimenő szélei találhatók. Ez az optimalizálás azért történik, hogy elkerülje a több partíciós lekérdezéseket, amikor a `out()`i kardinálisot használja a Graph-lekérdezésekben.

- **Az élek a csúcspontokra mutató hivatkozásokat tartalmaznak**. Az összes élek tárolása a csúcspontok partíciós kulcsaival és azonosítói történik. Ez a számítás lehetővé teszi, hogy az összes `out()` irány lekérdezése mindig hatókörrel rendelkező particionált lekérdezés legyen, és ne egy vak, több partíciós lekérdezést. 

- **A Graph-lekérdezéseknek partíciós kulcsot kell megadniuk**. Teljes mértékben kihasználhatja a vízszintes particionálás az Azure Cosmos DB, a partíciókulcs meg kell határozni egy egyetlen csúcspont kiválasztásakor, ha lehetséges. Egy vagy több csúcspontok kiválasztásakor egy particionált gráfon lekérdezések a következők:

    - a `/id` és a `/label` nem támogatottak a Gremlin API-hoz tartozó tárolók partíciós kulcsaként.


    - Jelöljön ki egy csúcspontot azonosító alapján, majd **a `.has()` lépés használatával adja meg a Partition Key tulajdonságot**: 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Csúcspont kiválasztása **egy olyan rekord megadásával, amely tartalmazza a partíciós kulcs értékét és azonosítóját**: 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - **Rekordok-értékek és-azonosítók tömbjét**határozza meg:
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - A csúcspontok és az azonosítóik kiválasztásával **megadhatja a partíciós kulcs értékeinek listáját**: 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - A **partíciós stratégia** használata a lekérdezés elején, valamint egy partíció megadása a Gremlin-lekérdezés további részének hatóköréhez: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Ajánlott eljárások, amikor egy particionált graph használatával

Teljesítményének és méretezhetőségének biztosításához a korlátlan tárolókkal particionált diagramok használata esetén használja az alábbi útmutatást:

- **A csúcspont lekérdezésekor mindig meg kell adni a partíciós kulcs értékét**. Csúcspont lekérése egy ismert partíció módja a teljesítmény eléréséhez. Az összes további érintkezési művelet mindig a partícióra lesz kiterjedően, mivel a szegélyek hivatkozási azonosítót és partíciós kulcsot tartalmaznak a cél csúcspontokra.

- **Ha lehetséges, használja a kimenő irányt az élek lekérdezéséhez**. Amint már említettük, a élek együtt a forrás csúcspontok a kimenő irányban vannak tárolva. Így a szoftver-és hibahivatkozások foka partíciók közötti lekérdezések, így a lehető legkisebb, amikor az adatok és a lekérdezések terveztük, vegye figyelembe ezt a mintát. Éppen ellenkezőleg, a `in()` lekérdezés mindig drága kiugró lekérdezés lesz.

- **Olyan partíciós kulcsot válasszon, amely egyenletesen osztja el az adatelosztást a partíciók között**. Ezt a döntést az adatmodellt, a megoldás az erősen függ. További információ a megfelelő partíciós kulcs létrehozásáról a [particionálás és méretezés Azure Cosmos DBban című részében található](partition-data.md).

- **Optimalizálja a lekérdezéseket a partíció határain belüli**adatgyűjtéshez. Az optimális particionálási stratégia szeretné igazítani a lekérdezési minták. Szerezze be az adatokat egyetlen partícióról lekérdezéseket a lehető legjobb teljesítményt nyújtanak.

## <a name="next-steps"></a>Következő lépések

Ezután folytassa a következő cikkekben:

* Tudnivalók a [particionálásról és a méretezésről Azure Cosmos db](partition-data.md).
* Ismerje meg a [GREMLIN API Gremlin-támogatását](gremlin-support.md).
* Ismerje meg a [GREMLIN API bevezetését](graph-introduction.md).
