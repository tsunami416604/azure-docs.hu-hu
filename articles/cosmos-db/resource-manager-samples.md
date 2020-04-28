---
title: Azure Resource Manager-sablonok az Azure Cosmos DB-hez
description: Azure Cosmos DB létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: 14c3438c5d6e7bed110f9e0dbfcd04655c3e9121
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193258"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-sablonok az Azure Cosmos DB-hez

Az alábbi táblázatok a Azure Cosmos DB Azure Resource Manager sablonjaira mutató hivatkozásokat tartalmaznak:

## <a name="sql-core-api"></a>SQL (Core) API

|**Sablon**|**Leírás**|
|---|---|
|[Azure Cosmos-fiók, adatbázis, tároló létrehozása](manage-sql-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy SQL-(Core-) API-fiókot két régióban két tárolóval, amelyek közös adatbázis-átviteli sebességgel és egy dedikált átviteli sebességgel rendelkező tárolóval rendelkeznek. Az átviteli sebesség frissítéséhez küldje el újra a sablont a frissített átviteli érték tulajdonságával. |
|[Azure Cosmos-fiók, adatbázis és tároló létrehozása tárolt eljárással, triggerrel és UDF-vel](manage-sql-with-resource-manager.md#create-sproc) | Ez a sablon egy SQL-(Core-) API-fiókot hoz létre két régióban egy tárolóhoz tárolt eljárással, triggerrel és UDF-vel. |
|[Privát végpont létrehozása meglévő Azure Cosmos-fiókhoz](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Ez a sablon egy meglévő virtuális hálózat meglévő Azure Cosmos SQL API-fiókjához hoz létre privát végpontot. |
|[Ingyenes szintű Azure Cosmos-fiók létrehozása](manage-sql-with-resource-manager.md#free-tier) |  Ez a sablon egy Azure Cosmos DB fiókot hoz létre az ingyenes szinten. |

## <a name="mongodb-api"></a>MongoDB API

|**Sablon**|**Leírás**|
|---| ---|
|[Azure Cosmos-fiók, adatbázis, gyűjtemény létrehozása](manage-mongodb-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy fiókot Azure Cosmos DB API-val a MongoDB két régióban, ahol engedélyezve van a több főkiszolgálós szolgáltatás. Az Azure Cosmos-fióknak két tárolója lesz, amelyek osztoznak az adatbázis-szintű átviteli sebességgel. |

## <a name="cassandra-api"></a>Cassandra API

|**Sablon**|**Leírás**|
|---| ---|
|[Azure Cosmos-fiók, szóköz, tábla létrehozása](manage-cassandra-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy Cassandra API fiókot két régióban, ahol a több főkiszolgálós engedélyezve van. Az Azure Cosmos-fiók két olyan táblát fog tartalmazni, amelyek a tárterület-szintű átviteli sebességet használják. |

## <a name="gremlin-api"></a>Gremlin API

|**Sablon**|**Leírás**|
|---| ---|
|[Azure Cosmos-fiók, adatbázis, gráf létrehozása](manage-gremlin-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy Gremlin API-fiókot két régióban, ahol a több főkiszolgálós engedélyezve van. Az Azure Cosmos-fióknak két gráfja lesz, amelyek osztoznak az adatbázis-szintű átviteli sebességgel. |

## <a name="table-api"></a>Table API

|**Sablon**|**Leírás**|
|---| ---|
|[Azure Cosmos-fiók létrehozása, tábla](manage-table-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy Table API fiókot két régióban, ahol a több főkiszolgálós engedélyezve van. Az Azure Cosmos-fiók egyetlen táblával fog rendelkezni. |

> [!TIP]
> Ha Table API használatával szeretné engedélyezni a megosztott átviteli sebességet, engedélyezze az Azure Portalon a fiókok szintjének átviteli sebességét.

Tekintse meg a dokumentációhoz [Azure Resource Manager referenciát Azure Cosmos db](/azure/templates/microsoft.documentdb/allversions) oldalon.
