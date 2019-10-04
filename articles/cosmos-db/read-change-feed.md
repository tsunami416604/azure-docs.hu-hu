---
title: A változási hírcsatorna elérése Azure Cosmos DB Azure Cosmos DB
description: Ez a cikk a Azure Cosmos DB-Azure Cosmos DB az olvasási és hozzáférés-módosítási adatcsatornák különböző lehetőségeit ismerteti.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.openlocfilehash: 3d52ba1abc22aae6121ea6a36f943851dfcca7a0
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467670"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Azure Cosmos DB változási csatorna olvasása

A következő lehetőségek bármelyikével dolgozhat a Azure Cosmos DB változási hírcsatornával:

* Azure Functions használata
* A hírcsatorna-feldolgozó függvénytárának módosítása
* A Azure Cosmos DB SQL API SDK használata

## <a name="using-azure-functions"></a>Azure Functions használata

Azure Functions a legegyszerűbb és ajánlott lehetőség. Ha Cosmos DBhoz hoz létre Azure Functions-triggert, kiválaszthatja a csatlakoztatni kívánt tárolót, és az Azure-függvény akkor aktiválódik, amikor módosul a tároló. Az eseményindítók a Azure Functions portál, a Azure Cosmos DB portál vagy az SDK-k programozott használatával hozhatók létre. A Visual Studio és a VS Code támogatást nyújt a Azure Functions írásához, és az Azure Functions CLI-t is használhatja a platformok közötti fejlesztéshez. A kódot megírhatja és hibakeresést végezhet az asztalon, majd egyetlen kattintással telepítheti a függvényt. További információért tekintse meg a [kiszolgáló nélküli adatbázis-számítástechnika Azure functions](serverless-computing-database.md) és a [change feed with Azure functions](change-feed-functions.md)) cikkeket.

## <a name="using-the-change-feed-processor-library"></a>A hírcsatorna-feldolgozó függvénytárának módosítása

A módosítási hírcsatorna processzor-könyvtára elrejti az összetettséget, és továbbra is teljes körű irányítást biztosít a változási hírcsatornában. A könyvtár a megfigyelő mintát követi, ahol a feldolgozó függvényt a könyvtár hívja meg. Ha nagy átviteli sebességű változási csatornával rendelkezik, több ügyfelet is létrehozhat, hogy beolvassa a változási csatornát. Mivel a Change feed Processor Library-t használja, az automatikusan elosztja a terhelést a különböző ügyfelek között anélkül, hogy ezt a logikát kellene megvalósítani. Az összes összetettséget a könyvtár kezeli. Ha azt szeretné, hogy saját terheléselosztó legyen, akkor `IPartitionLoadBalancingStrategy` a módosítási adatcsatorna feldolgozásához egyéni partíciós stratégiát alkalmazhat. További információ: [a hírcsatorna-feldolgozó függvénytárának módosítása](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>A Azure Cosmos DB SQL API SDK használata

Az SDK-val a változási hírcsatorna alacsony szintű vezérlését kapja meg. Kezelheti az ellenőrzőpontot, elérheti az adott logikai partíció kulcsát stb. Ha több olvasóval rendelkezik, a használatával `ChangeFeedOptions` terjesztheti az olvasási terhelést különböző szálak vagy különböző ügyfelek számára. 

## <a name="next-steps"></a>További lépések

Folytassa további információ a változáscsatorna az alábbi cikkeket:

* [A hírcsatorna változásának áttekintése](change-feed.md)
* [Az Azure Functions használatával módosítási hírcsatorna](change-feed-functions.md)
* [Feldolgozói kódtára használatával módosítási hírcsatorna](change-feed-processor.md)
