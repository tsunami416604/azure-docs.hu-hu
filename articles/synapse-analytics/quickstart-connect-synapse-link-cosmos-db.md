---
title: 'Gyors útmutató: csatlakozás az Azure szinapszis-hoz Azure Cosmos DB'
description: Azure Cosmos DB csatlakoztatása a szinapszis-munkaterülethez a szinapszis-hivatkozással
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.subservice: synapse-link
ms.topic: quickstart
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 14c71d5888aefc11485c03935c5d8c7ff7d17045
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995879"
---
# <a name="quickstart-connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Gyors útmutató: csatlakozás az Azure szinapszis-hoz Azure Cosmos DB

Ez a cikk bemutatja, hogyan férhet hozzá egy Azure Cosmos DB-adatbázishoz az Azure szinapszis Analytics Studio és a szinapszis hivatkozás használatával. 

## <a name="prerequisites"></a>Előfeltételek

A Azure Cosmos DB-fiók munkaterülethez való kapcsolódása előtt néhány dolog szükséges.

* Meglévő Azure Cosmos DB fiók, vagy hozzon létre egy új [fiókot a rövid](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account) útmutató után
* Meglévő szinapszis munkaterület, vagy hozzon létre egy új munkaterületet [a rövid](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) útmutató után 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analitikus tároló engedélyezése

Ha nagy léptékű elemzést szeretne Azure Cosmos DB a működési teljesítményének befolyásolása nélkül, javasoljuk, hogy engedélyezze a szinapszis-hivatkozást Azure Cosmos DBhoz. Ez a függvény HTAP képességet biztosít egy tárolóhoz, és beépített támogatást nyújt az Azure Szinapszisban. Ezt a rövid útmutatót követve engedélyezheti Cosmos DB tárolók számára a szinapszis-hivatkozást.

## <a name="navigate-to-synapse-studio"></a>Navigáljon a szinapszis studióhoz

A szinapszis munkaterületen válassza a **szinapszis Studio elindítása** lehetőséget. A szinapszis Studio kezdőlapján válassza az * * adatelemet, amely az **adatObject Explorer**.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Azure Cosmos DB-adatbázis összekötése egy szinapszis-munkaterülettel

Azure Cosmos DB adatbázis csatlakoztatása társított szolgáltatásként történik. A Cosmos DB társított szolgáltatás lehetővé teszi a felhasználók számára az adatok tallózását, olvasását és írását Apache Spark az Azure szinapszis Analytics vagy az SQL rendszerbe a Azure Cosmos DBba.

Az adatok Object Explorer a következő lépések végrehajtásával közvetlenül kapcsolódhat egy Azure Cosmos DB-adatbázishoz:

1. Válassza a * *_+_* _ ikont az adatközpont közelében
2. Válassza *az _ Kapcsolódás külső adatforráshoz* lehetőséget.*
3. Válassza ki azt az API-t, amelyhez csatlakozni szeretne: SQL vagy MongoDB
4. Válassza a **_Folytatás_* _
5. Nevezze el a társított szolgáltatást. A név megjelenik a Object Explorerban, és a szinapszis futtatási idejének használatával csatlakozik az adatbázishoz és a tárolóhoz. A felhasználóbarát név használatát javasoljuk.
6. Válassza ki az _ *Cosmos db fiók nevét** és az **adatbázis nevét** .
7. Választható Ha nincs megadva régió, a szinapszis futásidejű műveletei a legközelebbi régió felé irányítva lesznek, ahol az analitikai tároló engedélyezve van. Manuálisan is beállíthatja, hogy mely régióhoz szeretné elérni a felhasználókat Cosmos DB analitikus tárolóban. Válassza a **további kapcsolatok tulajdonságai** lehetőséget, majd az **új** elemet. A **tulajdonságnév** alatt írja **_PreferredRegions_*be a PreferredRegions _ értéket, és állítsa a _* értéket** a kívánt régióra (például: WestUS2, nincs szóköz a szavak és a számok között)
8. A **_create_* _ kiválasztása

Azure Cosmos DB adatbázisok láthatók a Azure Cosmos DB szakaszban található _ *linked** lapon. A HTAP engedélyezett Azure Cosmos DB tárolót egy csak OLTP tárolóból lehet megkülönböztetni a következő ikonokkal:

**Szinapszis-tároló**:

![HTAP-tároló](./media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

**OLTP csak tároló**:

![OLTP-tároló](./media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Gyors interakció a kód által generált műveletekkel

Ha a jobb gombbal rákattint egy tárolóra, megjelenik egy olyan kézmozdulatok listája, amelyek a Spark vagy az SQL futási idejét indítják el. A tárolóba való írás a Azure Cosmos DB tranzakciós tárolójával történik, és a rendszer a kérelmek egységeit fogja felhasználni.  

## <a name="next-steps"></a>További lépések

* [Ismerkedjen meg a szinapszis és a Azure Cosmos DB között támogatottak között](./synapse-link/concept-synapse-link-cosmos-db-support.md)
* [Megtudhatja, hogyan kérdezheti le a Apache Spark for Azure szinapszis Analytics szolgáltatással rendelkező analitikus tárolót](synapse-link/how-to-query-analytical-store-spark.md)