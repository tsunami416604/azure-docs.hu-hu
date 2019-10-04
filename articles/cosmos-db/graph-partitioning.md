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
ms.openlocfilehash: 4c8761d82c8a735ac9c4bff2e5ac0107b2a57fe0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537540"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Az Azure Cosmos DB egy particionált graph használatával

Az Azure Cosmos DB Gremlin API-legfontosabb funkcióit egyik képes kezelni a nagyméretű gráfok vízszintes méretezés során. A tárolók egymástól függetlenül méretezhetők a tárolás és átviteli sebesség tekintetében. Létrehozhat tárolókat az Azure Cosmos dB-ben, amely a grafikon adatainak tárolásához automatikusan skálázhatók. Az adatok automatikusan kiegyensúlyozott alapján a megadott **partíciókulcs**.

**A particionálás kötelező** Ha várhatóan a tároló tárolja a több mint 10 GB méretű, vagy ha szeretne lefoglalni a több mint 10 000 kérelemegység / másodperc (RU-k). Az általános alapelveket a [Azure Cosmos DB particionálási mechanizmust](partition-data.md) az alábbiakban néhány graph-specifikus optimalizálása a alkalmazni.

![Graph-particionálás.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Graph-particionálás mechanizmus

Az alábbi irányelvek leírják, hogyan működik, az Azure Cosmos DB particionálási stratégia:

- **Csúcsok és élek egyaránt JSON-dokumentumok formájában tárolja**.

- **Csúcspontok szükséges partíciókulcsot**. Ez a kulcs határozza meg, melyik partícióba a csúcspont egy kivonatoló algoritmus használatával tárolhatja. A partíció kulcstulajdonság neve van definiálva, amikor egy új tárolót hoz létre, és a egy formátuma: `/partitioning-key-name`.

- **Élek tárolja a rendszer a forráscsúcspont**. Minden csúcspont számára más szóval a partíciókulcs meghatározása mellett a kimenő szélei találhatók. Az optimalizálás használata során a partícióra kiterjedő lekérdezések elkerülése érdekében történik a `out()` számossága a graph-lekérdezéseket.

- **Élek a csúcspontok mutassanak mutató hivatkozásokat tartalmaz**. Minden élek a partíciókulcsokról és azok mutató csúcspontjának azonosítóit együtt vannak tárolva. A számítási lehetővé teszi az összes `out()` iránya lekérdezések mindig kell egy hatókörrel rendelkező particionált lekérdezést, és nem venné partícióra kiterjedő lekérdezések. 

- **Graph-lekérdezések meg kell adni egy partíciókulcsot**. Teljes mértékben kihasználhatja a vízszintes particionálás az Azure Cosmos DB, a partíciókulcs meg kell határozni egy egyetlen csúcspont kiválasztásakor, ha lehetséges. Egy vagy több csúcspontok kiválasztásakor egy particionált gráfon lekérdezések a következők:

    - `/id` és `/label` egy tárolóhoz, a Gremlin API partíciókulcsok nem támogatottak.


    - Csúcs azonosítója, majd kiválasztja **használatával a `.has()` lépéssel megadhatja azokat a partíciós kulcs tulajdonságát**: 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - A csúcspont kiválasztásával **egy rekord, beleértve a partíciókulcs-értékkel, és az azonosító megadása**: 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Adjon meg egy **partíciókulcs és azonosítók rekordokat tartalmazó tömb**:
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Csúcspontok az azonosítók készletét kiválasztása és **partíciókulcs-értékek listáját megadó**: 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Használatával a **stratégia particionálása** lekérdezés elején és a egy partíciót a többi részének a Gremlin lekérdezési hatókör megadása: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Ajánlott eljárások, amikor egy particionált graph használatával

Teljesítményének és méretezhetőségének biztosításához a korlátlan tárolókkal particionált diagramok használata esetén használja az alábbi útmutatást:

- **Mindig adja meg a partíciókulcs-értékkel csúcs lekérdezésekor**. Csúcspont lekérése egy ismert partíció módja a teljesítmény eléréséhez. Minden ezt követő Simuló művelet mindig tartozni fog egy partíció mivel élek hivatkozási azonosítója és a partíció kulcs a cél csúcspontok tartalmaznak.

- **Használja a kimenő irányban élek lekérdezésekor, ha lehetséges**. Amint már említettük, a élek együtt a forrás csúcspontok a kimenő irányban vannak tárolva. Így a szoftver-és hibahivatkozások foka partíciók közötti lekérdezések, így a lehető legkisebb, amikor az adatok és a lekérdezések terveztük, vegye figyelembe ezt a mintát. Ezzel szemben a `in()` lekérdezés mindig, az költséges logikájával lekérdezést.

- **Válassza ki a partíciókulcs egyenletesen között az adatokat több partícióra kiterjedő**. Ezt a döntést az adatmodellt, a megoldás az erősen függ. További információk a létrehozása egy, a megfelelő partíciókulcs [particionálás és skálázás az Azure Cosmos DB](partition-data.md).

- **Szerezhetők be adatok egy partíció határain belül-lekérdezések optimalizálása**. Az optimális particionálási stratégia szeretné igazítani a lekérdezési minták. Szerezze be az adatokat egyetlen partícióról lekérdezéseket a lehető legjobb teljesítményt nyújtanak.

## <a name="next-steps"></a>További lépések

Ezután folytassa a következő cikkekben:

* Ismerje meg [particionálási és horizontális az Azure Cosmos DB](partition-data.md).
* További információ a [Gremlin-támogatás a Gremlin API](gremlin-support.md).
* Ismerje meg [Gremlin API bemutatása](graph-introduction.md).
