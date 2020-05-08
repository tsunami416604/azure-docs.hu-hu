---
title: A változási hírcsatorna elérése Azure Cosmos DB Azure Cosmos DB
description: Ez a cikk különböző lehetőségeket ismertet a Azure Cosmos DBban az olvasási és a hozzáférés-módosítási adatcsatorna számára.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 266a13b7702f567e69129c0b4e92c4bd7c0f29ef
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982496"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Azure Cosmos DB-változáscsatorna olvasása

A következő lehetőségek bármelyikével dolgozhat a Azure Cosmos DB változási hírcsatornával:

* Az Azure Functions használata
* Az adatcsatorna módosításának használata
* Az Azure Cosmos DB SQL API SDK használata
* A Change feed pull Model (előzetes verzió) használata

## <a name="using-azure-functions"></a>Az Azure Functions használata

Azure Functions a legegyszerűbb és ajánlott lehetőség. Ha Cosmos DBhoz hoz létre Azure Functions-triggert, kiválaszthatja a csatlakoztatni kívánt tárolót, és az Azure-függvény akkor aktiválódik, amikor módosul a tároló. Az eseményindítók a Azure Functions portál, a Azure Cosmos DB portál vagy az SDK-k programozott használatával hozhatók létre. A Visual Studio és a VS Code támogatást nyújt a Azure Functions írásához, és az Azure Functions CLI-t is használhatja a platformok közötti fejlesztéshez. A kódot megírhatja és hibakeresést végezhet az asztalon, majd egyetlen kattintással telepítheti a függvényt. További információért tekintse meg a [kiszolgáló nélküli adatbázis-számítástechnika Azure functions](serverless-computing-database.md) és a [change feed with Azure functions](change-feed-functions.md)) cikkeket.

## <a name="using-the-change-feed-processor"></a>Az adatcsatorna módosításának használata

A módosítási hírcsatorna processzora elrejti az összetettséget, és továbbra is teljes körű irányítást biztosít a változási hírcsatornában. A könyvtár a megfigyelő mintát követi, ahol a feldolgozó függvényt a könyvtár hívja meg. Ha nagy átviteli sebességű változási csatornával rendelkezik, több ügyfelet is létrehozhat, hogy beolvassa a változási csatornát. Mivel a Change feed Processor Library-t használja, az automatikusan elosztja a terhelést a különböző ügyfelek között anélkül, hogy ezt a logikát kellene megvalósítani. Az összes összetettséget a könyvtár kezeli. További információkért lásd: [a hírcsatorna-kezelő processzor használata](change-feed-processor.md). A Change feed processzor a [Azure Cosmos db SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3)részét képezi.

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Az Azure Cosmos DB SQL API SDK használata

Az SDK-val a változási hírcsatorna alacsony szintű vezérlését kapja meg. Kezelheti az ellenőrzőpontot, elérheti az adott logikai partíció kulcsát stb. Ha több olvasóval rendelkezik, a használatával `ChangeFeedOptions` terjesztheti az olvasási terhelést különböző szálak vagy különböző ügyfelek számára.

## <a name="using-the-change-feed-pull-model"></a>A hírcsatorna-lekérési modell használata

A [hírcsatorna lekéréses modellje](change-feed-pull-model.md) lehetővé teszi, hogy a változási csatornát a saját tempójában használja fel, és integrálással a változások feldolgozását a FeedRanges-mel. A FeedRange a partíciós kulcs értékeinek egy tartományát öleli fel. Egy adott partíciós kulcs módosításait is könnyen feldolgozhatja a Change feed pull modell használatával.

> [!NOTE]
> A hírcsatorna-váltási lekérési modell jelenleg csak előzetes verzióban érhető [el a Azure Cosmos db .net SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) -ban. Az előzetes verzió még nem érhető el más SDK-verziókhoz.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Adatcsatorna módosítása a Cassandra és a MongoDB API-khoz

A MongoDB API-ban és a Cassandra API-ben predikátummal végzett lekérdezésekben a változási csatorna funkciói módosulnak. Ha többet szeretne megtudni a MongoDB API megvalósítási részleteiről, tekintse meg a [streamek módosítása Azure Cosmos db a MONGODB API-ban](mongodb-change-streams.md)című témakört.

A natív Apache Cassandra lehetővé teszi az adatváltozások rögzítését (CDC), egy olyan mechanizmust, amely az archiváláshoz adott táblákat jelöl, valamint elutasítja az írásokat az adott táblákba, amint a CDC-naplóhoz konfigurálható méretű lemez van elérve. A Cassandra Azure Cosmos DB API-ban az adatcsatorna módosítása funkció javítja a CQL-on keresztüli predikátumok lekérdezésének lehetőségét. További információ a megvalósítás részleteiről: [a Azure Cosmos db API használata a Cassandra-ben](cassandra-change-feed.md)című témakörben.

## <a name="next-steps"></a>További lépések

A következő cikkekben további tudnivalókat talál a hírcsatornák változásáról:

* [A hírcsatorna változásának áttekintése](change-feed.md)
* [A Change feed használata a Azure Functions](change-feed-functions.md)
* [A hírcsatorna-feldolgozó függvénytárának módosítása](change-feed-processor.md)
