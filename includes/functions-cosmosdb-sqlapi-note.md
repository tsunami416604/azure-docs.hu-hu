---
title: fájl belefoglalása
description: fájl belefoglalása
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f7495c52e36bdc207145f17ec72eb57b7ebf1784
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279280"
---
Az Azure Cosmos DB-kötések csak az SQL API-val használhatók. Az összes többi Azure Cosmos DB API esetében az API-hoz készült statikus ügyfél használatával kell elérnie az adatbázist a függvényből, beleértve a [Azure Cosmos db API](../articles/cosmos-db/mongodb-introduction.md)-ját a MongoDB, a [Cassandra API](../articles/cosmos-db/cassandra-introduction.md), a [Gremlin API](../articles/cosmos-db/graph-introduction.md)és a [Table API](../articles/cosmos-db/table-introduction.md)számára.
