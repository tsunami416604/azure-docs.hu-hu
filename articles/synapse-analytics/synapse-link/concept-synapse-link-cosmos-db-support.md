---
title: Azure szinapszis-hivatkozás (előzetes verzió) Azure Cosmos DB támogatott funkciókhoz
description: Az Azure szinapszis-hivatkozás által támogatott műveletek aktuális listájának megismerése Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 9c90bc9527bffff55d7478150cc1f31827bacc46
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235287"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>Azure szinapszis-hivatkozás (előzetes verzió) Azure Cosmos DB támogatott funkciókhoz

Ez a cikk azokat a funkciókat ismerteti, amelyeket jelenleg a Azure Cosmos DB Azure szinapszis-hivatkozása támogat.

## <a name="azure-synapse-support"></a>Azure szinapszis-támogatás

A Azure Cosmos DB kétféle tároló létezik:
* HTAP-tároló – egy szinapszis-hivatkozással rendelkező tároló. Ez a tároló tranzakciós tárolót és analitikai tárolót is tartalmaz. 
* OLTP-tároló – csak tranzakciós tárolóval rendelkező tároló; A szinapszis-hivatkozás nincs engedélyezve. 

> [!IMPORTANT]
> A Azure Cosmos DB Azure szinapszis hivatkozása jelenleg támogatott olyan munkaterületek esetében, amelyeken nincs engedélyezve a felügyelt virtuális hálózat. 

A szinapszis-hivatkozás engedélyezése nélkül csatlakozhat egy Azure Cosmos DB tárolóhoz, ebben az esetben csak olvasható/írható lehet a tranzakciós tároló. Az alábbi lista a Azure Cosmos DB szinapszis-hivatkozáson belüli jelenleg támogatott szolgáltatásainak listáját tartalmazza. 

| Kategória              | Leírás |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL Server nélküli](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| **Futásidejű támogatás** |Az Azure szinapszis futtatási idejének olvasási vagy írási támogatása| ✓ | [Kapcsolatfelvétel](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| **API-támogatás Azure Cosmos DB** |API-támogatás szinapszis-hivatkozásként| SQL/MongoDB | SQL/MongoDB |
| **Objektum**  |Olyan objektumok, mint például egy létrehozható tábla, amely közvetlenül a Azure Cosmos DB tárolóra mutat| Nézet, tábla | Nézet |
| **Olvasás**    |Adatok beolvasása egy Azure Cosmos DB tárolóból| OLTP/HTAP | HTAP  |
| **Írás**   |Adatok írása a futtatási időről Azure Cosmos DB tárolóba| OLTP | n.a. |

* Ha a Spark-ból ír egy Azure Cosmos DB tárolóba, ez a folyamat a Azure Cosmos DB tranzakciós tárolóján keresztül történik, és az Azure Cosmos DB tranzakciós teljesítményét is befolyásolja.
* Az SQL Pool külső táblákon keresztüli integrációja jelenleg nem támogatott.

## <a name="supported-code-generated-actions-for-spark"></a>Támogatott, kód által generált műveletek a Sparkhoz

| Kézmozdulat              | Leírás |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Betöltés a DataFrame** |Betöltés és beolvasás egy Spark-DataFrame |X| ✓ |
| **Spark-tábla létrehozása** |Azure Cosmos DB tárolóra mutató tábla létrehozása|X| ✓ |
| **DataFrame írása tárolóba** |Adatbevitel tárolóba|✓| ✓ |
| **Adatfolyam-DataFrame betöltése a tárolóból** |Adatfolyam-adatátvitel Azure Cosmos DB változási csatornán keresztül|✓| ✓ |
| **Adatfolyam-DataFrame írása tárolóba** |Adatfolyam-adatátvitel Azure Cosmos DB változási csatornán keresztül|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>Támogatott programkód által generált műveletek az SQL Server nélküli rendszerhez

| Kézmozdulat              | Leírás |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Válassza ki a Top 100** |Első 100-elem előnézete egy tárolóból|X| ✓ |
| **Nézet létrehozása** |Hozzon létre egy nézetet, amely közvetlenül rendelkezik BI-hozzáféréssel egy tárolóban a szinapszis SQL használatával|X| ✓ |

## <a name="next-steps"></a>További lépések

* Lásd: [Csatlakozás a szinapszishoz hivatkozás Azure Cosmos db](../quickstart-connect-synapse-link-cosmos-db.md)
* [Ismerje meg, hogyan kérdezheti le az analitikai áruházat a Spark használatával](how-to-query-analytical-store-spark.md)