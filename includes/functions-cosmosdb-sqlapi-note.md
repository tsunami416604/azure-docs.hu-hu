---
title: fájlbefoglalás
description: fájlbefoglalás
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f7495c52e36bdc207145f17ec72eb57b7ebf1784
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "76279280"
---
Az Azure Cosmos DB-kötések csak az SQL API-val használhatók. Az összes többi Azure Cosmos DB API esetében az API-hoz készült statikus ügyfél használatával kell elérnie az adatbázist a függvényből, beleértve a [Azure Cosmos db API](../articles/cosmos-db/mongodb-introduction.md)-ját a MongoDB, a [Cassandra API](../articles/cosmos-db/cassandra-introduction.md), a [Gremlin API](../articles/cosmos-db/graph-introduction.md)és a [Table API](../articles/cosmos-db/table-introduction.md)számára.
