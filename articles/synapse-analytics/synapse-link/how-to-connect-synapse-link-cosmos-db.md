---
title: Csatlakozás az Azure szinapszis hivatkozásához Azure Cosmos DB
description: Azure Cosmos DB csatlakoztatása egy szinapszis-munkaterülethez az Azure szinapszis hivatkozásával
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: d72d1feda4c267dfa3d5c0dea6928f1b7541b26d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599936"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Csatlakozás az Azure szinapszis hivatkozásához Azure Cosmos DB

Ez a cikk azt ismerteti, hogyan férhet hozzá egy Azure Cosmos DB-adatbázishoz az Azure szinapszis Analytics studióból az Azure szinapszis hivatkozásával.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt a munkaterülethez csatlakoztatná egy Azure Cosmos DB adatbázist, a következőkre lesz szüksége:

* Meglévő Azure Cosmos DB adatbázis, vagy hozzon létre egy új [fiókot a rövid](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account) útmutató után
* Meglévő szinapszis munkaterület, vagy hozzon létre egy új munkaterületet [a rövid](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) útmutató után 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analitikus tároló engedélyezése

Ha nagy léptékű elemzést szeretne Azure Cosmos DB a működési teljesítményének befolyásolása nélkül, javasoljuk, hogy engedélyezze a szinapszis-hivatkozást Azure Cosmos DBhoz. A szinapszis hivatkozása HTAP képességet biztosít egy tárolóhoz, és beépített támogatást nyújt az Azure Szinapszisban.

## <a name="navigate-to-synapse-studio"></a>Navigáljon a szinapszis studióhoz

A szinapszis munkaterületen válassza a **szinapszis Studio elindítása**lehetőséget. A szinapszis Studio kezdőlapján válassza az * * adatelemet, amely az **adatObject Explorer**.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Azure Cosmos DB-adatbázis összekötése egy szinapszis-munkaterülettel

Azure Cosmos DB adatbázis csatlakoztatása társított szolgáltatásként történik. Az Azure Cosmos DB társított szolgáltatás lehetővé teszi a felhasználóknak az adatok tallózását, olvasását és írását Apache Spark az Azure szinapszis Analytics vagy az SQL alkalmazásba Azure Cosmos DBba.

Az adatok Object Explorer a következő lépések végrehajtásával közvetlenül kapcsolódhat egy Azure Cosmos DB-adatbázishoz:

1. Válassza ki ***+*** az ikont az adatközpont közelében
2. **Külső adatkapcsolat** kiválasztása
3. Válassza ki azt az API-t, amelyhez csatlakozni szeretne: SQL API vagy API a MongoDB-hez
4. Válassza a ***Folytatás*** lehetőséget.
5. Nevezze el a társított szolgáltatást. A név megjelenik a Object Explorerban, és a szinapszis futtatási idejének használatával csatlakozik az adatbázishoz és a tárolóhoz. A felhasználóbarát név használatát javasoljuk.
6. Válassza ki a **Azure Cosmos db fiók nevét** és az **adatbázis nevét**
7. Választható Ha nincs megadva régió, a szinapszis futásidejű műveletei a legközelebbi régió felé irányítva lesznek, ahol az analitikai tároló engedélyezve van. Manuálisan is beállíthatja, hogy mely régióhoz szeretné elérni a felhasználókat Azure Cosmos DB analitikus tárolóban. Válassza a **további kapcsolatok tulajdonságai** lehetőséget, majd az **új**elemet. A **tulajdonságnév**területen írjon ***PreferredRegions*** , és állítsa be a kívánt régió **értékét** (például: WestUS2, nincs szóköz a szavak és a számok között)
8. ***Létrehozás*** kiválasztása

Azure Cosmos DB adatbázisok láthatók a Azure Cosmos DB szakaszban **csatolt** lapon. A Azure Cosmos DB lehetővé teszi, hogy egy HTAP-kompatibilis tárolót a OLTP csak a következő ikonokkal lehessen megkülönböztetni:

**OLTP csak tároló**:

![OLTP-tároló](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**HTAP engedélyezett tárolója**:

![HTAP-tároló](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Gyors interakció a kód által generált műveletekkel

Ha a jobb gombbal rákattint egy tárolóra, olyan kézmozdulatok listája látható, amelyek elindítják a Spark vagy az SQL futási idejét. A tárolóba való írás a Azure Cosmos DB tranzakciós tárolójával történik, és a rendszer a kérelmek egységeit fogja felhasználni.  

## <a name="next-steps"></a>További lépések

* [Ismerkedjen meg a szinapszis és a Azure Cosmos DB között támogatottak között](./concept-synapse-link-cosmos-db-support.md)
* [Ismerje meg, hogyan kérdezheti le az analitikai áruházat a Spark használatával](./how-to-query-analytical-store-spark.md)