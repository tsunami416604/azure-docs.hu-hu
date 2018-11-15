---
title: Az Azure Cosmos DB Azure Cosmos DB elérése a módosítási hírcsatorna
description: Ez a cikk ismerteti a különböző lehetőségekről, és az Azure Cosmos DB Azure Cosmos DB változáscsatorna eléréséről.
author: rafats
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rafats
ms.openlocfilehash: 3168593e30a7f5acf49960c8479da75529860a65
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684545"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Információk az Azure Cosmos DB-módosítási hírcsatorna

Az Azure Cosmos DB módosítási hírcsatorna használatával a következő lehetőségek bármelyikét használhatja:

* Az Azure Functions használatával
* Használatával a módosítási hírcsatorna feldolgozói kódtára
* Az Azure Cosmos DB SQL API SDK használatával

## <a name="using-azure-functions"></a>Az Azure Functions használatával

Az Azure Functions, a legegyszerűbb és ajánlott lehetőség. Ha egy Azure Functions-alkalmazást hoz létre egy Azure Cosmos DB-eseményindítóval, kiválaszthatja, hogy a tárolóhoz való csatlakozáshoz, és az Azure-függvény beolvassa aktivált, minden alkalommal, amikor megváltozik a tárolóhoz. Eseményindítók az Azure Functions portálon, az Azure Cosmos DB-portál használatával vagy programozott módon az SDK-k hozható létre. A Visual Studio és a VS Code nyújtanak támogatást az Azure Functions írása, és használhatja az Azure Functions CLI a platformfüggetlen fejlesztésekhez. Írási és a kódok hibakeresése az Ön asztalán, és telepíteni a függvény egyetlen kattintással. Lásd: [az Azure Functions szolgáltatással kiszolgáló nélküli adatbázis-használat](serverless-computing-database.md) és [az Azure Functions használatával módosítási hírcsatorna](change-feed-functions.md)) további cikkek.

## <a name="using-the-change-feed-processor-library"></a>Használatával a módosítási hírcsatorna feldolgozói kódtára

A változáscsatorna feldolgozói kódtára elrejti az összetettséget, és továbbra is a változáscsatorna, egy teljes körű felügyeletet biztosít. A könyvtár megfigyelő mintát követi, ahol a feldolgozási függvényt hívja meg, a könyvtárban. Ha egy nagy átviteli sebességű változáscsatorna, olvassa el a módosítási hírcsatorna több ügyféllel példányosítható. A változáscsatorna feldolgozói kódtára használja, mert azt automatikusan felosztja a terhelést a különböző ügyfelek között anélkül, hogy kellene a logikát alkalmazzák. A könyvtár összes összetettsége kezeli. Ha saját terheléselosztóját legyen, majd valósíthat meg `IPartitionLoadBalancingStrategy` egy egyéni partíció stratégia feldolgozni a módosítási hírcsatorna. További tudnivalókért lásd: [használatával módosítási hírcsatorna feldolgozói kódtára](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Az Azure Cosmos DB SQL API SDK használatával

Az SDK-val kap a változáscsatorna alacsony szintű irányítását. Az ellenőrzőpont kezelése, eléréséhez egy adott logikai partíciós kulcs, stb. Ha több olvasók, használhat `ChangeFeedOptions` a olvasási terheléselosztásban, különböző szálakon vagy különböző ügyfelek részére. 

## <a name="next-steps"></a>További lépések

Folytassa további információ a változáscsatorna az alábbi cikkeket:

* [Módosítási hírcsatorna áttekintése](change-feed.md)
* [Az Azure Functions használatával módosítási hírcsatorna](change-feed-functions.md)
* [Feldolgozói kódtára használatával módosítási hírcsatorna](change-feed-processor.md)