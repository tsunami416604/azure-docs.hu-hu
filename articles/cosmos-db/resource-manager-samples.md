---
title: Az Azure Cosmos DB az Azure Resource Manager-sablonok
description: Használja az Azure Resource Manager-sablonok létrehozása és konfigurálása az Azure Cosmos DB-hez.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: c907de019b64a6a9d03206514a1147fd43c78106
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078460"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Az Azure Cosmos DB az Azure Resource Manager-sablonok

A következő táblázat Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz az Azure Cosmos DB:

|**API-típus** | **minta mutató hivatkozás**| **Leírás** |
|---|---| ---|
|Core (SQL) API| [Hozzon létre egy Azure Cosmos DB-fiókhoz (több főkiszolgálós)](manage-sql-with-resource-manager.md) | Ez a sablon létrehoz egy SQL API-fiók engedélyezve van több főkiszolgálós két régióban. Az Azure Cosmos-fiók két tárolót használó adatbázisszintű átviteli sebesség lesz. |
| MongoDB API | [Hozzon létre egy Azure Cosmos DB-fiókhoz (több főkiszolgálós)](manage-mongodb-with-resource-manager.md) | Ez a sablon létrehoz egy fiókot az Azure Cosmos DB API használatával a mongodb-hez két régióban több főkiszolgálós engedélyezve van. Az Azure Cosmos-fiók két tárolót használó adatbázisszintű átviteli sebesség lesz. |
| Cassandra API | [Hozzon létre egy Azure Cosmos DB-fiókhoz (több főkiszolgálós)](manage-cassandra-with-resource-manager.md) | Ez a sablon létrehoz egy Cassandra API-fiók engedélyezve van több főkiszolgálós két régióban. Az Azure Cosmos-fiók lesz a két táblázat kulcstér szintű teljesítmény megosztani. |
| Gremlin API| [Hozzon létre egy Azure Cosmos DB-fiókhoz (több főkiszolgálós)](manage-gremlin-with-resource-manager.md) | Ez a sablon létrehoz egy Gremlin API-fiók engedélyezve van több főkiszolgálós két régióban. Az Azure Cosmos-fiók lesz a két grafikon, amelyek adatbázisszintű átviteli sebességet. |
| Tábla API | [Hozzon létre egy Azure Cosmos DB-fiókhoz (több főkiszolgálós)](manage-table-with-resource-manager.md) | Ez a sablon létrehoz egy Table API-fiókot két régióban több főkiszolgálós engedélyezve van. Az Azure Cosmos-fiók lesz az egyetlen táblában. |

> [!TIP]
> Megosztott átviteli Table API használatakor engedélyezéséhez fiókszintű átviteli sebesség az Azure Portalon.

Lásd: [ARM-referencia az Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) a referencia-dokumentáció oldalán.