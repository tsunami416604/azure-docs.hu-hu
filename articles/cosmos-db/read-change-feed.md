---
title: Módosítási hírcsatorna elérése az Azure Cosmos DB Azure Cosmos DB-ban
description: Ez a cikk ismerteti a különböző lehetőségek et az Azure Cosmos DB Azure Cosmos DB olvasásához és eléréséhez elérhető különböző lehetőségek.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688116"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Azure Cosmos DB-változáscsatorna olvasása

Az Azure Cosmos DB-féle módosítási hírcsatornával az alábbi lehetőségek bármelyikével dolgozhat:

* Az Azure Functions használata
* A változáscsatorna feldolgozó kódtárának használata
* Az Azure Cosmos DB SQL API SDK használata

## <a name="using-azure-functions"></a>Az Azure Functions használata

Az Azure Functions a legegyszerűbb és ajánlott megoldás. Amikor létrehoz egy Azure Functions-eseményindítót a Cosmos DB számára, kiválaszthatja a csatlakozni kívánt tárolót, és az Azure-függvény aktiválódik, amikor a tároló módosul. Eseményindítók hozhat létre az Azure Functions portál, az Azure Cosmos DB portál vagy programozott sdk-k. A Visual Studio és a VS Code támogatást nyújt az Azure Functions írásához, és akár az Azure Functions CLI-t is használhatja a platformok közötti fejlesztéshez. Írhat és debug a kódot az asztalon, majd telepítse a funkciót egy kattintással. További információ: [Kiszolgáló nélküli adatbázis-számítástechnika az Azure Functions használatával](serverless-computing-database.md) és a [változáscsatorna használata az Azure Functions funkcióval)](change-feed-functions.md)cikkek.

## <a name="using-the-change-feed-processor-library"></a>A változáscsatorna feldolgozó kódtárának használata

A módosítási hírcsatorna-processzorkönyvtár elrejti a bonyolultságot, és továbbra is teljes mértékben szabályozhatja a módosítási hírcsatornát. A könyvtár követi a megfigyelőmintát, ahol a feldolgozási függvényt a könyvtár nevezi meg. Ha nagy átviteli sebességű változás csatorna, több ügyfelet is létrehozhat a módosítási hírcsatorna olvasásához. Mivel módosítási hírcsatorna-processzorkönyvtárat használ, automatikusan felosztja a terhelést a különböző ügyfelek között anélkül, hogy ezt a logikát végre kellene hajtania. Az összes összetettséget a könyvtár kezeli. Ha azt szeretné, hogy a saját terheléselosztó, majd valósíthatja meg `IPartitionLoadBalancingStrategy` egy egyéni partíció stratégia folyamata változás csatorna. További információ: [A hírcsatorna-feldolgozókönyvtár módosítása](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Az Azure Cosmos DB SQL API SDK használata

Az SDK-val alacsony szintű vezérlést kap a módosítási hírcsatorna felett. Kezelheti az ellenőrzőpontot, hozzáférhet egy adott logikai partíciókulcshoz stb. Ha több olvasóval rendelkezik, olvasási `ChangeFeedOptions` terhelés tosztással terjesztheti különböző szálakra vagy különböző ügyfelekre.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>A Cassandra és a MongoDB API-kban való módosítása

A változáscsatorna-funkció változásfolyamként kerül felszínre a MongoDB API-ban és a Queryben a Cassandra API predikátumával. Ha többet szeretne megtudni a MongoDB API megvalósítási részleteiről, tekintse meg a [Streamek módosítása az Azure Cosmos DB API-ban a MongoDB-hoz című témakört.](mongodb-change-streams.md)

A natív Apache Cassandra biztosítja a változási adatok rögzítését (CDC), egy olyan mechanizmust, amely meghatározott táblákat jelöl meg archiváláshoz, valamint elutasítja az írásokat ezekre a táblákra, amint a CDC-napló konfigurálható méretű lemeze eléri. A cassandrai Azure Cosmos DB API változáscsatorna-szolgáltatása növeli a módosítások lekérdezésének lehetőségét a CQL-en keresztüli predikátummal. Ha többet szeretne megtudni a megvalósítás részleteiről, olvassa [el a Hírcsatorna módosítása az Azure Cosmos DB API cassandra.](cassandra-change-feed.md)

## <a name="next-steps"></a>További lépések

Most a következő cikkekben olvashat bővebben a változáscsatornáról:

* [A módosítási hírcsatorna áttekintése](change-feed.md)
* [Módosítási hírcsatorna használata az Azure Functions funkcióval](change-feed-functions.md)
* [A módosítási hírcsatorna-kódtár használata](change-feed-processor.md)
