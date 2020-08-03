---
title: Azure PowerShell-minták az Azure Cosmos DB-ben
description: Azure PowerShell minták beszerzése általános feladatok végrehajtásához Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 678e9490fe52a329365174e1a2283a475f3312e5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506657"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure PowerShell-minták az Azure Cosmos DB-ben

Az alábbi táblázat a Azure Cosmos DB leggyakrabban használt Azure PowerShell parancsfájljaira mutató hivatkozásokat tartalmaz. A jobb oldalon található hivatkozásokkal navigáljon az API-specifikus mintákhoz. Az általános minták minden API esetében azonosak. Az összes Azure Cosmos DB PowerShell-parancsmagra vonatkozó hivatkozási lapok a [Azure PowerShell-hivatkozásban](/powershell/module/az.cosmosdb)érhetők el. Ellenőrizze, hogy rendszeresen frissülnek-e a frissítések `Az.CosmosDB` . Ezeket a PowerShell-mintákat a GitHub-adattárból is letölthetik Cosmos DBhoz, [Cosmos db PowerShell-mintákat a githubon](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Gyakori minták

|Feladat | Leírás |
|---|---|
|[Fiók frissítése](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB fiók alapértelmezett konzisztencia-szintjének frissítése. |
|[Fiók régiói frissítése](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB fiók régióinak frissítése. |
|[Feladatátvételi prioritás módosítása vagy feladatátvételi trigger](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók regionális feladatátvételi prioritásának módosítása vagy manuális feladatátvétel indítása. |
|[Fiók kulcsai vagy a kapcsolatok karakterláncai](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elsődleges és másodlagos kulcsok, a kapcsolatok karakterláncának beolvasása vagy egy Azure Cosmos DB fiók kulcsának újragenerálása. |
|[Cosmos-fiók létrehozása IP-tűzfallal](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos DB fiókot, amelyen engedélyezve van az IP-tűzfal. |
|||

## <a name="core-sql-api-samples"></a>Core (SQL) API-minták

|Feladat | Leírás |
|---|---|
|[Fiók, adatbázis és tároló létrehozása](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos DB fiókot, adatbázist és tárolót. |
|[Hozzon létre egy fiókot, egy adatbázist és egy tárolót az autoscale](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos DB fiókot, adatbázist és tárolót az autoscale paranccsal. |
|[Tároló létrehozása nagyméretű partíciós kulccsal](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy tárolót egy nagyméretű partíciós kulccsal. |
|[Index-szabályzat nélküli tároló létrehozása](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hozzon létre egy Azure Cosmos-tárolót az index Policy kikapcsolva.|
|[Adatbázisok vagy tárolók listázása és lekérése](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy tárolók listázása vagy beolvasása. |
|[Átviteli sebesség lekérdezése](scripts/powershell/sql/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy tároló átviteli sebességének beolvasása. |
|[Frissítési sebesség](scripts/powershell/sql/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy tároló sebességének frissítése. |
|[Erőforrások zárolásának törlése a törlésből](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal. |
|||

## <a name="cassandra-api-samples"></a>Cassandra API minták

|Feladat | Leírás |
|---|---|
|[Fiók, szóköz és tábla létrehozása](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot, a térközt és a táblát. |
|[Fiók, térköz és tábla létrehozása az autoscale paranccsal](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot, a térközt és a táblát az autoscaletel. |
|[Szóközök vagy táblázatok listázása és lekérése](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Kilistázhatja vagy beolvashatja a tárhelyeket vagy a táblákat. |
|[Átviteli sebesség lekérdezése](scripts/powershell/cassandra/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lemezterület vagy tábla átviteli sebességének beolvasása. |
|[Frissítési sebesség](scripts/powershell/cassandra/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lemezterület vagy tábla frissítési sebességének frissítése. |
|[Erőforrások zárolásának törlése a törlésből](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal. |
|||

## <a name="mongo-db-api-samples"></a>Mongo DB API-minták

|Feladat | Leírás |
|---|---|
|[Fiók, adatbázis és gyűjtemény létrehozása](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot,-adatbázist és-gyűjteményt. |
|[Hozzon létre egy fiókot, adatbázist és gyűjteményt az autoscale](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot,-adatbázist és-gyűjteményt az autoskálázással. |
|[Adatbázisok vagy gyűjtemények listázása és lekérése](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy gyűjtemény listázása vagy beolvasása. |
|[Átviteli sebesség lekérdezése](scripts/powershell/mongodb/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy gyűjtemény átviteli sebességének beolvasása. |
|[Frissítési sebesség](scripts/powershell/mongodb/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy gyűjtemény frissítési sebességének frissítése. |
|[Erőforrások zárolásának törlése a törlésből](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal. |
|||

## <a name="gremlin-api-samples"></a>Gremlin API-minták

|Feladat | Leírás |
|---|---|
|[Fiók, adatbázis és gráf létrehozása](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot, adatbázist és gráfot. |
|[Hozzon létre egy fiókot, adatbázist és gráfot az autoscale](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot, egy adatbázist és egy gráfot az autoscaletel. |
|[Adatbázisok vagy diagramok listázása és lekérése](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy gráf listázása vagy beolvasása. |
|[Átviteli sebesség lekérdezése](scripts/powershell/gremlin/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy gráf adatforgalmának beolvasása. |
|[Frissítési sebesség](scripts/powershell/gremlin/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy gráf sebességének frissítése. |
|[Erőforrások zárolásának törlése a törlésből](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal. |
|||

## <a name="table-api-samples"></a>Table API minták

|Feladat | Leírás |
|---|---|
|[Fiók és tábla létrehozása](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot és-táblát. |
|[Fiók és tábla létrehozása az autoscale paranccsal](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot és egy tábla-méretezést. |
|[Táblázatok listázása és lekérése](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Táblázatok listázása vagy beolvasása. |
|[Átviteli sebesség lekérdezése](scripts/powershell/table/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy tábla átviteli sebességének beolvasása. |
|[Frissítési sebesség](scripts/powershell/table/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy tábla átviteli sebességének frissítése. |
|[Erőforrások zárolásának törlése a törlésből](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal. |
|||
