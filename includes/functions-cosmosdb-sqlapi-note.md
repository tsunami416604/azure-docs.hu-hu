---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c54aa861a47b11756f05e003e9b944df6c5b0e28
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178905"
---
Az Azure Cosmos DB-kötések csak az SQL API-val használhatók. Az összes többi Azure Cosmos DB API-k, kell az adatbázis eléréséhez fog a funkció a statikus ügyfél segítségével az API-hoz, beleértve a [Azure Cosmos DB MongoDB API-](../articles/cosmos-db/mongodb-introduction.md), [Cassandra API](../articles/cosmos-db/cassandra-introduction.md), [ Gremlin API](../articles/cosmos-db/graph-introduction.md), és [Table API](../articles/cosmos-db/table-introduction.md).
