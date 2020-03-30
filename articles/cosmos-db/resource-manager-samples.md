---
title: Azure Resource Manager-sablonok az Azure Cosmos DB-hez
description: Azure Resource Manager-sablonok használatával hozhatja létre és konfigurálhatja az Azure Cosmos DB-t.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961853"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-sablonok az Azure Cosmos DB-hez

Az alábbi táblázatok az Azure Cosmos DB Azure Resource Manager-sablonjaira mutató hivatkozásokat tartalmaznak:

## <a name="sql-core-api"></a>SQL (Core) API

|**Sablon**|**Leírás**|
|---|---|
|[Hozzon létre egy Azure Cosmos-fiókot, adatbázist, tárolót](manage-sql-with-resource-manager.md#create-resource) | Ez a sablon két régióban hoz létre egy SQL (Core) API-fiókot két tárolóval, megosztott adatbázis-átviteli fokkal és egy dedikált átviteli fokkal rendelkező tárolóval. Átviteli átviteli lehet frissíteni a sablon újbóli elküldésével frissített átviteli tulajdonság értéke. |
|[Hozzon létre egy Azure Cosmos-fiókot, adatbázist és tárolót tárolt eljárással, eseményindítóval és UDF-fel](manage-sql-with-resource-manager.md#create-sproc) | Ez a sablon egy SQL (Core) API-fiókot hoz létre két régióban egy tárolt eljárás, eseményindító és UDF egy tárolóhoz. |

## <a name="mongodb-api"></a>MongoDB API

|**Sablon**|**Leírás**|
|---| ---|
|[Hozzon létre egy Azure Cosmos-fiókot, adatbázist, gyűjteményt](manage-mongodb-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy fiókot az Azure Cosmos DB API-t a MongoDB két régióban több főkiszolgálós engedélyezve van. Az Azure Cosmos-fiók két tárolóval rendelkezik, amelyek megosztják az adatbázis-szintű átviteli. |

## <a name="cassandra-api"></a>Cassandra API

|**Sablon**|**Leírás**|
|---| ---|
|[Hozzon létre egy Azure Cosmos-fiókot, kulcsterületet, táblát](manage-cassandra-with-resource-manager.md#create-resource) | Ez a sablon létrehoz egy Cassandra API-fiókot két régióban, ahol a többfőkiszolgálós engedélyezve van. Az Azure Cosmos-fiók két táblával fog rendelkezni, amelyek kulcstérszintű átviteli sebességű megosztást. |

## <a name="gremlin-api"></a>Gremlin API

|**Sablon**|**Leírás**|
|---| ---|
|[Hozzon létre egy Azure Cosmos-fiókot, adatbázist, grafikont](manage-gremlin-with-resource-manager.md#create-resource) | Ez a sablon egy Gremlin API-fiókot hoz létre két régióban, ahol a többfőkiszolgálós engedélyezve van. Az Azure Cosmos-fiók két gráfok, amelyek megosztják az adatbázis-szintű átviteli. |

## <a name="table-api"></a>Tábla API

|**Sablon**|**Leírás**|
|---| ---|
|[Hozzon létre egy Azure Cosmos-fiókot, tábla](manage-table-with-resource-manager.md#create-resource) | Ez a sablon egy table API-fiókot hoz létre két régióban, ahol a többfőkiszolgálós kapcsolat engedélyezve van. Az Azure Cosmos-fiók egyetlen táblával fog rendelkezni. |

> [!TIP]
> A table API használatakor a megosztott átviteli rendszer engedélyezéséhez engedélyezze a fiókszintű átviteli szintet az Azure Portalon.

Tekintse meg az [Azure Resource Manager referencia az Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) lapon a referencia dokumentációt.
