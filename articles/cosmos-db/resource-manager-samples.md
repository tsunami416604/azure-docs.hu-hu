---
title: Azure Resource Manager-sablonok az Azure Cosmos DB-hez
description: Azure Cosmos DB létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 501065875cafc035d491e606c016f3ad4dbfc5d4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791629"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-sablonok az Azure Cosmos DB-hez

Az alábbi táblázatok a Azure Cosmos DB Azure Resource Manager sablonjaira mutató hivatkozásokat tartalmaznak:

## <a name="core-sql-api"></a>Core (SQL) API

|**Sablon**|**Leírás**|
|---|---|
|[Azure Cosmos-fiók, adatbázis és tároló létrehozása az autoscale átviteli sebességgel](manage-sql-with-resource-manager.md#create-autoscale) | Ez a sablon egy alapszintű (SQL-) API-fiókot hoz létre két régióban, egy adatbázist és egy tárolót az automatikusan méretezhető átviteli sebességgel. |
|[Hozzon létre egy Azure Cosmos-fiókot, egy adatbázist, egy standard (manuális) átviteli sebességgel rendelkező tárolót](manage-sql-with-resource-manager.md#create-manual) | Ez a sablon létrehoz egy core (SQL) API-fiókot két régióban, egy adatbázist és egy standard átviteli sebességgel rendelkező tárolót. |
|[Azure Cosmos-fiók, adatbázis és tároló létrehozása tárolt eljárással, triggerrel és UDF-vel](manage-sql-with-resource-manager.md#create-sproc) | Ez a sablon egy alapszintű (SQL-) API-fiókot hoz létre két régióban egy tárolóhoz tárolt eljárással, triggerrel és UDF-vel. |
|[Privát végpont létrehozása meglévő Azure Cosmos-fiókhoz](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Ez a sablon egy meglévő virtuális hálózat meglévő Azure Cosmos Core-(SQL-) API-fiókjához hoz létre privát végpontot. |
|[Ingyenes szintű Azure Cosmos-fiók létrehozása](manage-sql-with-resource-manager.md#free-tier) |  Ez a sablon egy Azure Cosmos DB Core (SQL) API-fiókot hoz létre a szabad szintű szinten. |

## <a name="mongodb-api"></a>MongoDB API

|**Sablon**|**Leírás**|
|---| ---|
|[Azure Cosmos-fiók, adatbázis, gyűjtemény létrehozása az autoscale átviteli sebességgel](manage-mongodb-with-resource-manager.md#create-autoscale) | Ez a sablon Azure Cosmos DB API-val hoz létre egy fiókot két régió MongoDB, amely az adatbázis-szintű autoskálázási átviteli sebességet osztja meg két tárolóval. |
|[Hozzon létre egy Azure Cosmos-fiókot, adatbázist, gyűjteményt a standard (manuális) átviteli sebességgel](manage-mongodb-with-resource-manager.md#create-manual) | Ez a sablon létrehoz egy fiókot Azure Cosmos DB API-val a MongoDB két régióban két olyan tárolóval, amelyek osztoznak az adatbázis szintű standard átviteli sebességgel. |

## <a name="cassandra-api"></a>Cassandra API

|**Sablon**|**Leírás**|
|---| ---|
|[Hozzon létre egy Azure Cosmos-fiókot, a térközt és a táblázatot az autoscale átviteli sebességgel](manage-cassandra-with-resource-manager.md#create-autoscale) | Ez a sablon egy Cassandra API fiókot hoz létre két régióban, és az automatikusan méretezhető átviteli sebességgel rendelkezik. |
|[Hozzon létre egy Azure Cosmos-fiókot, egy alaptárhelyet, a standard (manuális) átviteli sebességű táblázatot](manage-cassandra-with-resource-manager.md#create-manual) | Ez a sablon egy Cassandra API fiókot hoz létre két régióban, amely egy alapterülettel és egy tábla manuális átviteli sebességgel rendelkezik. |

## <a name="gremlin-api"></a>Gremlin API

|**Sablon**|**Leírás**|
|---| ---|
|[Azure Cosmos-fiók, adatbázis és gráf létrehozása az autoscale átviteli sebességgel](manage-gremlin-with-resource-manager.md#create-autoscale) | Ez a sablon létrehoz egy Gremlin API-fiókot két régióban egy adatbázissal és gráfmal, amely automatikusan méretezhető átviteli sebességgel rendelkezik. |
|[Hozzon létre egy Azure Cosmos-fiókot, adatbázist, gráfot standard (manuális) átviteli sebességgel](manage-gremlin-with-resource-manager.md#create-manual) | Ez a sablon egy Gremlin API-fiókot hoz létre két régióban a standard átviteli sebességgel rendelkező adatbázis és gráf használatával. |

## <a name="table-api"></a>Table API

|**Sablon**|**Leírás**|
|---| ---|
|[Hozzon létre egy Azure Cosmos-fiókot, amely az autoscale átviteli sebességgel rendelkező tábla](manage-table-with-resource-manager.md#create-autoscale) | Ez a sablon létrehoz egy Table API fiókot két régióban, és egyetlen táblát, amely automatikusan méretezhető átviteli sebességgel rendelkezik. |
|[Hozzon létre egy Azure Cosmos-fiókot, a standard (manuális) átviteli sebességgel rendelkező táblázatot](manage-table-with-resource-manager.md#create-manual) | Ez a sablon létrehoz egy Table API fiókot két régióban, és egyetlen táblázatot, amely standard átviteli sebességgel rendelkezik. |

Tekintse meg a dokumentációhoz [Azure Resource Manager referenciát Azure Cosmos db](/azure/templates/microsoft.documentdb/allversions) oldalon.
