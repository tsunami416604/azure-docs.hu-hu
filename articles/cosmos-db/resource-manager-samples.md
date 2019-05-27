---
title: Az Azure Cosmos DB az Azure Resource Manager-sablonok
description: Használja az Azure Resource Manager-sablonok létrehozása és konfigurálása az Azure Cosmos DB-hez.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 4a32b0497d2457a740e9c082f990bb9112208bfd
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969177"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Az Azure Cosmos DB az Azure Resource Manager-sablonok

Az alábbi táblázatok az Azure Cosmos DB Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmazzák:

## <a name="sql-core-api"></a>SQL (Core) API

|**Sablon**|**Leírás**|
|---| ---|
|[Egy Azure Cosmos-fiók, adatbázis, a tároló létrehozása](manage-sql-with-resource-manager.md#create-resource) | Ezzel a sablonnal hoz létre, több főkiszolgálós engedélyezve (mag) az SQL API-fiók két különböző régiójában. Az Azure Cosmos-fiók két tárolót használó adatbázisszintű átviteli sebesség lesz. |
|[Átviteli sebesség (RU/s)-adatbázis frissítése](manage-sql-with-resource-manager.md#database-ru-update) | Ez a sablon frissíti az átviteli sebesség (mag) az SQL API-fiók az adatbázishoz. |
|[Átviteli sebesség (RU/s), a tároló frissítése](manage-sql-with-resource-manager.md#container-ru-update) | Ez a sablon frissíti az átviteli sebesség (mag) az SQL API-fiók a tárolóhoz. |

## <a name="mongodb-api"></a>MongoDB API

|**Sablon**|**Leírás**|
|---| ---|
|[Egy Azure Cosmos-fiók, adatbázis, gyűjtemény létrehozása](manage-mongodb-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy fiókot az Azure Cosmos DB API használatával a mongodb-hez két régióban több főkiszolgálós engedélyezve van. Az Azure Cosmos-fiók két tárolót használó adatbázisszintű átviteli sebesség lesz. |
|[Átviteli sebesség (RU/s)-adatbázis frissítése](manage-mongodb-with-resource-manager.md#database-ru-update) | Ez a sablon frissíti az átviteli sebességet egy adatbázis MongoDB API-fiók. |
|[Átviteli sebesség (RU/s), a gyűjtemény frissítése](manage-mongodb-with-resource-manager.md#collection-ru-update) | Ez a sablon frissíti az átviteli sebességet egy tárolóhoz, a MongoDB API-fiókban. |

## <a name="cassandra-api"></a>Cassandra API

|**Sablon**|**Leírás**|
|---| ---|
|[Egy Azure Cosmos-fiók, kulcstér, tábla létrehozása](manage-cassandra-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy Cassandra API-fiók engedélyezve van több főkiszolgálós két régióban. Az Azure Cosmos-fiók lesz a két táblázat kulcstér szintű teljesítmény megosztani. |
|[Frissítse a kulcstér átviteli sebesség (RU/s)](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | Ez a sablon az átviteli sebesség a Cassandra API-fiók egy kulcstér frissíti. |
|[Átviteli sebesség (RU/s) tábla frissítése](manage-cassandra-with-resource-manager.md#table-ru-update) | Ez a sablon frissíti az átviteli sebességet a Cassandra API-fiók egyik táblájában. |

## <a name="gremlin-api"></a>Gremlin API

|**Sablon**|**Leírás**|
|---| ---|
|[Hozzon létre egy Azure Cosmos-fiók, adatbázis, gráf](manage-gremlin-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy Gremlin API-fiók engedélyezve van több főkiszolgálós két régióban. Az Azure Cosmos-fiók lesz a két grafikon, amelyek adatbázisszintű átviteli sebességet. |
|[Átviteli sebesség (RU/s)-adatbázis frissítése](manage-gremlin-with-resource-manager.md#database-ru-update) | Ez a sablon a Gremlin API-fiók az adatbázis átviteli frissíti. |
|[Átviteli sebesség (RU/s), egy grafikon frissítése](manage-gremlin-with-resource-manager.md#graph-ru-update) | Ez a sablon frissíti az átviteli sebességet a Gremlin API-fiók egy diagram. |

## <a name="table-api"></a>Tábla API

|**Sablon**|**Leírás**|
|---| ---|
|[Egy Azure Cosmos-fiók létrehozása, tábla](manage-table-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy Table API-fiókot két régióban több főkiszolgálós engedélyezve van. Az Azure Cosmos-fiók lesz az egyetlen táblában. |
|[Átviteli sebesség (RU/s) tábla frissítése](manage-table-with-resource-manager.md#table-ru-update) | Ez a sablon frissíti az átviteli sebességet egy tábla a Table API-fiókban. |

> [!TIP]
> Megosztott átviteli Table API használatakor engedélyezéséhez fiókszintű átviteli sebesség az Azure Portalon.

Lásd: [ARM-referencia az Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) a referencia-dokumentáció oldalán.