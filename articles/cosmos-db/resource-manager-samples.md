---
title: Azure Cosmos DB Azure Resource Manager sablonjai
description: Azure Cosmos DB létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961853"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB Azure Resource Manager sablonjai

Az alábbi táblázatok a Azure Cosmos DB Azure Resource Manager sablonjaira mutató hivatkozásokat tartalmaznak:

## <a name="sql-core-api"></a>SQL (Core) API

|**Sablon**|**Leírás**|
|---|---|
|[Azure Cosmos-fiók, adatbázis, tároló létrehozása](manage-sql-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy SQL-(Core-) API-fiókot két régióban két tárolóval, amelyek közös adatbázis-átviteli sebességgel és egy dedikált átviteli sebességgel rendelkező tárolóval rendelkeznek. Az átviteli sebesség frissítéséhez küldje el újra a sablont a frissített átviteli érték tulajdonságával. |
|[Azure Cosmos-fiók, adatbázis és tároló létrehozása tárolt eljárással, triggerrel és UDF-vel](manage-sql-with-resource-manager.md#create-sproc) | Ez a sablon egy SQL-(Core-) API-fiókot hoz létre két régióban egy tárolóhoz tárolt eljárással, triggerrel és UDF-vel. |

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

## <a name="table-api"></a>Tábla API

|**Sablon**|**Leírás**|
|---| ---|
|[Azure Cosmos-fiók létrehozása, tábla](manage-table-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy Table API fiókot két régióban, ahol a több főkiszolgálós engedélyezve van. Az Azure Cosmos-fiók egyetlen táblával fog rendelkezni. |

> [!TIP]
> Ha Table API használatával szeretné engedélyezni a megosztott átviteli sebességet, engedélyezze az Azure Portalon a fiókok szintjének átviteli sebességét.

Tekintse meg a dokumentációhoz [Azure Resource Manager referenciát Azure Cosmos db](/azure/templates/microsoft.documentdb/allversions) oldalon.
