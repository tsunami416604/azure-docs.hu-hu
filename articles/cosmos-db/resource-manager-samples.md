---
title: Azure Cosmos DB Azure Resource Manager sablonjai
description: Azure Cosmos DB létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7861196daf3b04430200096a0d42bdcc6e6d5cf9
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053224"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB Azure Resource Manager sablonjai

Az alábbi táblázatok a Azure Cosmos DB Azure Resource Manager sablonjaira mutató hivatkozásokat tartalmaznak:

## <a name="sql-core-api"></a>SQL (Core) API

|**Sablon**|**Leírás**|
|---|---|
|[Azure Cosmos-fiók, adatbázis, tároló létrehozása](manage-sql-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy SQL-(Core-) API-fiókot két régióban, ahol engedélyezve van a több főkiszolgáló. Az Azure Cosmos-fióknak két tárolója lesz, amelyek osztoznak az adatbázis-szintű átviteli sebességgel. |
|[Azure Cosmos-fiók, adatbázis és tároló létrehozása tárolt eljárással, triggerrel és UDF-vel](manage-sql-with-resource-manager.md#create-sproc) | Ez a sablon egy SQL-(Core-) API-fiókot hoz létre két régióban egy tárolóhoz tárolt eljárással, triggerrel és UDF-vel. |
|[Adatbázis frissítési teljesítményének (RU/s) frissítése](manage-sql-with-resource-manager.md#database-ru-update) | Ez a sablon egy SQL-(Core-) API-fiókban frissíti egy adatbázis átviteli sebességét. |
|[Egy tárolóra vonatkozó frissítési teljesítmény (RU/s)](manage-sql-with-resource-manager.md#container-ru-update) | Ez a sablon egy SQL-(Core-) API-fiókban lévő tároló átviteli sebességét frissíti. |

## <a name="mongodb-api"></a>MongoDB API

|**Sablon**|**Leírás**|
|---| ---|
|[Azure Cosmos-fiók, adatbázis, gyűjtemény létrehozása](manage-mongodb-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy fiókot Azure Cosmos DB API-val a MongoDB két régióban, ahol engedélyezve van a több főkiszolgálós szolgáltatás. Az Azure Cosmos-fióknak két tárolója lesz, amelyek osztoznak az adatbázis-szintű átviteli sebességgel. |
|[Adatbázis frissítési teljesítményének (RU/s) frissítése](manage-mongodb-with-resource-manager.md#database-ru-update) | Ez a sablon frissíti egy adatbázis átviteli sebességét egy MongoDB API-fiókban. |
|[A gyűjteményhez tartozó frissítési teljesítmény (RU/s)](manage-mongodb-with-resource-manager.md#collection-ru-update) | Ez a sablon egy MongoDB API-fiókban lévő tároló átviteli sebességét frissíti. |

## <a name="cassandra-api"></a>Cassandra API

|**Sablon**|**Leírás**|
|---| ---|
|[Azure Cosmos-fiók, szóköz, tábla létrehozása](manage-cassandra-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy Cassandra API fiókot két régióban, ahol a több főkiszolgálós engedélyezve van. Az Azure Cosmos-fiók két olyan táblát fog tartalmazni, amelyek a tárterület-szintű átviteli sebességet használják. |
|[Az átviteli sebesség (RU/s) frissítése egy szóközre](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | Ez a sablon frissíti a Cassandra API-fiókban található lemezterület átviteli sebességét. |
|[Egy tábla frissítési teljesítményének (RU/s) frissítése](manage-cassandra-with-resource-manager.md#table-ru-update) | Ez a sablon egy Cassandra API-fiókban lévő tábla átviteli sebességét frissíti. |

## <a name="gremlin-api"></a>Gremlin API

|**Sablon**|**Leírás**|
|---| ---|
|[Azure Cosmos-fiók, adatbázis, gráf létrehozása](manage-gremlin-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy Gremlin API-fiókot két régióban, ahol a több főkiszolgálós engedélyezve van. Az Azure Cosmos-fióknak két gráfja lesz, amelyek osztoznak az adatbázis-szintű átviteli sebességgel. |
|[Adatbázis frissítési teljesítményének (RU/s) frissítése](manage-gremlin-with-resource-manager.md#database-ru-update) | Ez a sablon frissíti egy adatbázis átviteli sebességét egy Gremlin API-fiókban. |
|[Sebesség (RU/s) frissítése egy gráfhoz](manage-gremlin-with-resource-manager.md#graph-ru-update) | Ez a sablon frissíti egy gráf átviteli sebességét egy Gremlin API-fiókban. |

## <a name="table-api"></a>Tábla API

|**Sablon**|**Leírás**|
|---| ---|
|[Azure Cosmos-fiók létrehozása, tábla](manage-table-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy Table API fiókot két régióban, ahol a több főkiszolgálós engedélyezve van. Az Azure Cosmos-fiók egyetlen táblával fog rendelkezni. |
|[Egy tábla frissítési teljesítményének (RU/s) frissítése](manage-table-with-resource-manager.md#table-ru-update) | Ez a sablon egy Table API-fiókban lévő tábla átviteli sebességét frissíti. |

> [!TIP]
> Ha Table API használatával szeretné engedélyezni a megosztott átviteli sebességet, engedélyezze az Azure Portalon a fiókok szintjének átviteli sebességét.

Tekintse meg a hivatkozási dokumentáció Azure Cosmos DB oldalának [ARM-referenciáját](/azure/templates/microsoft.documentdb/allversions) .
