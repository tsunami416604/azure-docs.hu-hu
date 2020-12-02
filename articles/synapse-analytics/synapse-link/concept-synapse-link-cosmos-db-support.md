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
ms.openlocfilehash: 3d12e60e2aeee667b89d56589870d53366772014
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459033"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Az Azure Cosmos DB-hez készült Azure Synapse Link támogatott szolgáltatásai

Ez a cikk az Azure Cosmos DB-hez készült Azure Synapse Link jelenleg támogatott funkcióit ismerteti.

## <a name="azure-synapse-support"></a>Azure szinapszis-támogatás

A Azure Cosmos DB kétféle tároló létezik:
* HTAP-tároló – egy szinapszis-hivatkozással rendelkező tároló. Ez a tároló tranzakciós tárolót és analitikai tárolót is tartalmaz. 
* OLTP-tároló – az Synaspe-hivatkozással rendelkező tároló nincs engedélyezve. Ez a tároló csak tranzakciós tárolóval rendelkezik, és nem rendelkezik analitikus tárolóval.

> [!IMPORTANT]
> A Azure Cosmos DB Azure szinapszis-hivatkozása jelenleg olyan szinapszis-munkaterületeken támogatott, amelyeken nincs engedélyezve a felügyelt virtuális hálózat. 

A szinapszis-hivatkozás engedélyezése nélkül csatlakozhat egy Azure Cosmos DB tárolóhoz. Ebben az esetben csak a tranzakciós tárolót lehet olvasni/írni. A következő lista felsorolja a szinapszis-hivatkozáson belüli jelenleg támogatott szolgáltatásokat Azure Cosmos DB. 

| Kategória              | Leírás |[Apache Spark készlet](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [Kiszolgáló nélküli SQL-készlet](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **Futásidejű támogatás** |Az Azure szinapszis futtatókörnyezetének támogatása a Azure Cosmos DB eléréséhez| ✓ | Előnézet |
| **API-támogatás Azure Cosmos DB** | Támogatott Azure Cosmos DB API-típus | SQL/MongoDB | SQL/MongoDB |
| **Objektum**  |Olyan objektumok, mint például egy létrehozható tábla, amely közvetlenül a Azure Cosmos DB tárolóra mutat| Dataframe, megtekintés, tábla | Nézet |
| **Olvasás**    | Az olvasható Azure Cosmos DB tároló típusa | OLTP/HTAP | HTAP  |
| **Írás**   | Használható az Azure szinapszis futtatókörnyezete az adatAzure Cosmos DB tárolóba való írásra | Igen | Nem |

* Ha a Sparkból ír Azure Cosmos DB tárolóba, ez a folyamat a Azure Cosmos DB tranzakciós tárolójából történik. Ez hatással lesz Azure Cosmos DB tranzakciós teljesítményére a kérelmek elfogyasztásával.
* A dedikált SQL Pool külső táblákon keresztüli integrációja jelenleg nem támogatott.
 
## <a name="supported-code-generated-actions-for-spark"></a>Támogatott, kód által generált műveletek a Sparkhoz

| Kézmozdulat              | Description |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Betöltés a DataFrame** |Betöltés és beolvasás egy Spark-DataFrame |✓| ✓ |
| **Spark-tábla létrehozása** |Azure Cosmos DB tárolóra mutató tábla létrehozása|✓| ✓ |
| **DataFrame írása tárolóba** |Adatbevitel tárolóba|✓| ✓ |
| **Adatfolyam-DataFrame betöltése a tárolóból** |Adatfolyam-adatátvitel Azure Cosmos DB változási csatornán keresztül|✓| ✓ |
| **Adatfolyam-DataFrame írása tárolóba** |Adatfolyam-adatátvitel Azure Cosmos DB változási csatornán keresztül|✓| ✓ |


## <a name="supported-code-generated-actions-for-serverless-sql-pool"></a>Támogatott programkód által generált műveletek a kiszolgáló nélküli SQL-készlethez

| Kézmozdulat              | Description |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Adatok feltárása** |Az ismerős T-SQL szintaxissal és az automatikus séma-következtetéssel megvizsgálhatja a tároló adatait|X| ✓ |
| **Nézetek létrehozása és BI-jelentések készítése** |SQL-nézet létrehozása, amely közvetlen hozzáférést biztosít a BI-tárolóhoz a kiszolgáló nélküli SQL-készleten keresztül |X| ✓ |
| **Különböző adatforrások csatlakoztatása Cosmos DB-adatforrásokkal együtt** | A lekérdezés eredményeinek tárolása Cosmos DB tárolóból származó adatok beolvasásával, valamint az Azure Blob Storage vagy a Azure Data Lake Storage CETAS használatával |X| ✓ |

## <a name="next-steps"></a>További lépések

* Lásd: [Csatlakozás a szinapszishoz hivatkozás Azure Cosmos db](../quickstart-connect-synapse-link-cosmos-db.md)
* [Ismerje meg, hogyan kérdezheti le a Cosmos DB analitikus áruházat a Spark használatával](how-to-query-analytical-store-spark.md)
