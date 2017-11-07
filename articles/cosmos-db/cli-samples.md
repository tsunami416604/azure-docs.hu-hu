---
title: "Az Azure CLI-példák Azure Cosmos DB |} Microsoft Docs"
description: "Az Azure CLI minták – Azure Cosmos DB fiókok, adatbázisok, tárolók, régiók és tűzfalak létrehozása és kezelése."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 11/02/2017
ms.author: mimig
ms.openlocfilehash: 989ef9915028c42a4da817bf2dd3aa5ad2beb2ef
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Az Azure Cosmos DB Azure CLI-minták

A következő táblázat az Azure Cosmos DB Azure CLI mintaparancsfájlok hivatkozásokat tartalmaz. Az összes Azure Cosmos DB parancssori felület parancsait hivatkozás lapok érhetők el a [Azure CLI 2.0 hivatkozás](https://docs.microsoft.com/cli/azure/cosmosdb).

| |  |
|---|---|
|**Azure Cosmos DB fiók, az adatbázis és a tárolók létrehozása**||
|[A DocumentDB API-fiók létrehozása](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| A DocumentDB API hoz létre egyetlen Azure Cosmos DB API-fiók, adatbázis és tárolóhoz való használatra. |
| [MongoDB API-fiók létrehozása](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy egyetlen Azure Cosmos DB MongoDB API fiók, adatbázis és gyűjtemény. |
|**Az Azure Cosmos DB méretezése**||
| [Skála tároló átviteli sebesség](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Módosítja a kiosztott througput tárolóba.|
|[Azure Cosmos DB adatbázisfiók több régióba replikálja, és feladatátvételi prioritások beállítása](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Egy adott feladatátvevő prioritású több területekre globálisan replikálja a fiók adatait.|
|**Az Azure Cosmos DB biztonságos**||
| [Fiók kulcsok beszerzése](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Lekérdezi a fő írási elsődleges és másodlagos kulcsok és a fiók elsődleges és másodlagos csak olvasható kulcsok.|
| [MongoDB kapcsolati karakterlánc beolvasása](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | A kapcsolati karakterláncot a MongoDB alkalmazás csatlakoztatása az Azure Cosmos DB fiók lekérése.|
|[Fiók kulcsok újragenerálása](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|A fiók a fő vagy csak olvasható kulcsának újragenerálása.|
|[Hozzon létre egy tűzfal](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy bejövő IP hozzáférés-vezérlési szabályzat egy jóváhagyott gépek csoportja tartalmazza a fiókja korlátozhatják és/vagy a felhőalapú szolgáltatások.|
|**Magas rendelkezésre állású, katasztrófa utáni helyreállítás, biztonsági mentése és visszaállítása**||
|[Feladatátvételi házirend konfigurálása](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Minden egyes régió, ahol a fiók replikálódik feladatátvételi prioritását állítja.|
|**Csatlakozás Azure Cosmos DB erőforrások**||
|[Webes alkalmazás csatlakoztatása az Azure Cosmos-Adatbázishoz](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Hozzon létre, és csatlakoztassa egy Azure Cosmos DB adatbázis és az Azure-webalkalmazás.|
|||
