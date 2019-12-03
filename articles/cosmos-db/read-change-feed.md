---
title: A változási hírcsatorna elérése Azure Cosmos DB Azure Cosmos DB
description: Ez a cikk a Azure Cosmos DB-Azure Cosmos DB az olvasási és hozzáférés-módosítási adatcsatornák különböző lehetőségeit ismerteti.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688116"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Azure Cosmos DB változási csatorna olvasása

A következő lehetőségek bármelyikével dolgozhat a Azure Cosmos DB változási hírcsatornával:

* Azure Functions használata
* A hírcsatorna-feldolgozó függvénytárának módosítása
* A Azure Cosmos DB SQL API SDK használata

## <a name="using-azure-functions"></a>Azure Functions használata

Azure Functions a legegyszerűbb és ajánlott lehetőség. Ha Cosmos DBhoz hoz létre Azure Functions-triggert, kiválaszthatja a csatlakoztatni kívánt tárolót, és az Azure-függvény akkor aktiválódik, amikor módosul a tároló. Az eseményindítók a Azure Functions portál, a Azure Cosmos DB portál vagy az SDK-k programozott használatával hozhatók létre. A Visual Studio és a VS Code támogatást nyújt a Azure Functions írásához, és az Azure Functions CLI-t is használhatja a platformok közötti fejlesztéshez. A kódot megírhatja és hibakeresést végezhet az asztalon, majd egyetlen kattintással telepítheti a függvényt. További információért tekintse meg a [kiszolgáló nélküli adatbázis-számítástechnika Azure functions](serverless-computing-database.md) és a [change feed with Azure functions](change-feed-functions.md)) cikkeket.

## <a name="using-the-change-feed-processor-library"></a>A hírcsatorna-feldolgozó függvénytárának módosítása

A módosítási hírcsatorna processzor-könyvtára elrejti az összetettséget, és továbbra is teljes körű irányítást biztosít a változási hírcsatornában. A könyvtár a megfigyelő mintát követi, ahol a feldolgozó függvényt a könyvtár hívja meg. Ha nagy átviteli sebességű változási csatornával rendelkezik, több ügyfelet is létrehozhat, hogy beolvassa a változási csatornát. Mivel a Change feed Processor Library-t használja, az automatikusan elosztja a terhelést a különböző ügyfelek között anélkül, hogy ezt a logikát kellene megvalósítani. Az összes összetettséget a könyvtár kezeli. Ha azt szeretné, hogy saját terheléselosztó legyen, akkor a módosítási adatcsatorna feldolgozásához egyéni partíciós stratégiát alkalmazhat `IPartitionLoadBalancingStrategy`. További információ: [a hírcsatorna-feldolgozó függvénytárának módosítása](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>A Azure Cosmos DB SQL API SDK használata

Az SDK-val a változási hírcsatorna alacsony szintű vezérlését kapja meg. Kezelheti az ellenőrzőpontot, elérheti az adott logikai partíció kulcsát stb. Ha több olvasóval rendelkezik, a `ChangeFeedOptions` használatával terjesztheti az olvasási terhelést különböző szálokra vagy különböző ügyfelekre.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Adatcsatorna módosítása a Cassandra és a MongoDB API-khoz

A MongoDB API-ban és a Cassandra API-ben predikátummal végzett lekérdezésekben a változási csatorna funkciói módosulnak. Ha többet szeretne megtudni a MongoDB API megvalósítási részleteiről, tekintse meg a [streamek módosítása Azure Cosmos db a MONGODB API-ban](mongodb-change-streams.md)című témakört.

A natív Apache Cassandra lehetővé teszi az adatváltozások rögzítését (CDC), egy olyan mechanizmust, amely az archiváláshoz adott táblákat jelöl, valamint elutasítja az írásokat az adott táblákba, amint a CDC-naplóhoz konfigurálható méretű lemez van elérve. A Cassandra Azure Cosmos DB API-ban az adatcsatorna módosítása funkció javítja a CQL-on keresztüli predikátumok lekérdezésének lehetőségét. További információ a megvalósítás részleteiről: [a Azure Cosmos db API használata a Cassandra-ben](cassandra-change-feed.md)című témakörben.

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat talál a hírcsatornák változásáról:

* [A hírcsatorna változásának áttekintése](change-feed.md)
* [A Change feed használata a Azure Functions](change-feed-functions.md)
* [A hírcsatorna-feldolgozó függvénytárának módosítása](change-feed-processor.md)
