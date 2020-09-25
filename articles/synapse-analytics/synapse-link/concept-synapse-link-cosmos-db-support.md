---
title: Azure szinapszis-hivatkozás (előzetes verzió) Azure Cosmos DB támogatott funkciókhoz
description: Az Azure szinapszis-hivatkozás által támogatott műveletek aktuális listájának megismerése Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 0cdc9e242c2254cafaf0af75bcb8f8879cf3eb58
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287828"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>Azure szinapszis-hivatkozás (előzetes verzió) Azure Cosmos DB támogatott funkciókhoz

Ez a cikk az Azure Cosmos DB-hez készült Azure Synapse Link jelenleg támogatott funkcióit ismerteti.

## <a name="azure-synapse-support"></a>Azure szinapszis-támogatás

A Azure Cosmos DB kétféle tároló létezik:
* HTAP-tároló – egy szinapszis-hivatkozással rendelkező tároló. Ez a tároló tranzakciós tárolót és analitikai tárolót is tartalmaz. 
* OLTP-tároló – az Synaspe-hivatkozással rendelkező tároló nincs engedélyezve. Ez a tároló csak tranzakciós tárolóval rendelkezik, és nem rendelkezik analitikus tárolóval.

> [!IMPORTANT]
> A Azure Cosmos DB Azure szinapszis-hivatkozása jelenleg olyan szinapszis-munkaterületeken támogatott, amelyeken nincs engedélyezve a felügyelt virtuális hálózat. 

A szinapszis-hivatkozás engedélyezése nélkül csatlakozhat egy Azure Cosmos DB tárolóhoz. Ebben az esetben csak a tranzakciós tárolót lehet olvasni/írni. A következő lista felsorolja a szinapszis-hivatkozáson belüli jelenleg támogatott szolgáltatásokat Azure Cosmos DB. 

| Kategória              | Leírás |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL Server nélküli](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **Futásidejű támogatás** |Az Azure szinapszis futtatókörnyezetének támogatása a Azure Cosmos DB eléréséhez| ✓ | [Kapcsolatfelvétel](mailto:cosmosdbsynapselink@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB) |
| **API-támogatás Azure Cosmos DB** | Támogatott Azure Cosmos DB API-típus | SQL/MongoDB | SQL/MongoDB |
| **Objektum**  |Olyan objektumok, mint például egy létrehozható tábla, amely közvetlenül a Azure Cosmos DB tárolóra mutat| Dataframe, megtekintés, tábla | Nézet |
| **Olvasás**    | Az olvasható Azure Cosmos DB tároló típusa | OLTP/HTAP | HTAP  |
| **Írás**   | Használható az Azure szinapszis futtatókörnyezete az adatAzure Cosmos DB tárolóba való írásra | Igen | Nem |

* Ha a Sparkból ír Azure Cosmos DB tárolóba, ez a folyamat a Azure Cosmos DB tranzakciós tárolójából történik. Ez hatással lesz Azure Cosmos DB tranzakciós teljesítményére a kérelmek elfogyasztásával.
* Az SQL Pool külső táblákon keresztüli integrációja jelenleg nem támogatott.
 
## <a name="supported-code-generated-actions-for-spark"></a>Támogatott, kód által generált műveletek a Sparkhoz

| Kézmozdulat              | Leírás |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Betöltés a DataFrame** |Betöltés és beolvasás egy Spark-DataFrame |✓| ✓ |
| **Spark-tábla létrehozása** |Azure Cosmos DB tárolóra mutató tábla létrehozása|✓| ✓ |
| **DataFrame írása tárolóba** |Adatbevitel tárolóba|✓| ✓ |
| **Adatfolyam-DataFrame betöltése a tárolóból** |Adatfolyam-adatátvitel Azure Cosmos DB változási csatornán keresztül|✓| ✓ |
| **Adatfolyam-DataFrame írása tárolóba** |Adatfolyam-adatátvitel Azure Cosmos DB változási csatornán keresztül|✓| ✓ |


## <a name="supported-code-generated-actions-for-sql-serverless"></a>Támogatott programkód által generált műveletek az SQL Server nélküli rendszerhez

| Kézmozdulat              | Leírás |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Adatok feltárása** |Az ismerős T-SQL szintaxissal és az automatikus séma-következtetéssel megvizsgálhatja a tároló adatait|X| ✓ |
| **Nézetek létrehozása és BI-jelentések készítése** |SQL-nézet létrehozása, hogy közvetlen hozzáférést biztosítson a BI-hoz készült tárolóhoz az SQL Server nélküli |X| ✓ |
| **Különböző adatforrások csatlakoztatása Cosmos DB-adatforrásokkal együtt** | A lekérdezés eredményeinek tárolása Cosmos DB tárolóból származó adatok beolvasásával, valamint az Azure Blob Storage vagy a Azure Data Lake Storage CETAS használatával |X| ✓ |

## <a name="next-steps"></a>Következő lépések

* Lásd: [Csatlakozás a szinapszishoz hivatkozás Azure Cosmos db](../quickstart-connect-synapse-link-cosmos-db.md)
* [Ismerje meg, hogyan kérdezheti le az analitikai áruházat a Spark használatával](how-to-query-analytical-store-spark.md)
