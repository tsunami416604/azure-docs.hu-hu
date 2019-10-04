---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c68b56463625a09f9c8341881284706ab8250f46
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608227"
---
Az Azure Cosmos DB-kötések csak az SQL API-val használhatók. Az összes többi Azure Cosmos DB API-k, kell az adatbázis eléréséhez fog a funkció a statikus ügyfél segítségével az API-hoz, beleértve a [Azure Cosmos DB MongoDB API-](../articles/cosmos-db/mongodb-introduction.md), [Cassandra API](../articles/cosmos-db/cassandra-introduction.md), [ Gremlin API](../articles/cosmos-db/graph-introduction.md), és [Table API](../articles/cosmos-db/table-introduction.md).
