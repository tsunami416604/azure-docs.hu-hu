---
title: Bevezetés az Azure Cosmos DB MongoDB-hoz való API-jának
description: Megtudhatja, hogyan használhatja az Azure Cosmos DB-t nagy mennyiségű adat tárolására és lekérdezésére az Azure Cosmos DB MongoDB API-jával.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/1/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 670020d276b9d4a868f24eb4a3f522581060adca
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "72754979"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

Az [Azure Cosmos DB](introduction.md) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása az alapvető fontosságú alkalmazásokhoz. Az Azure Cosmos DB az [iparág legjobb szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/cosmos-db/) által biztosított [teljes körű, globális terjesztést](distribute-data-globally.md) kínál, valamint [a teljesítmény és a tárterület rugalmas méretezését](partition-data.md) világszerte, az esetek 99%-ában egyszámjegyű ezredmásodperces késéseket és garantált magas rendelkezésre állást. Az Azure Cosmos DB [automatikusan indexeli az adatokat](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) anélkül, hogy a felhasználónak sémákat és indexeket kellene kezelnie. Egy többmodelles szolgáltatásról van szó, amely támogatja a dokumentum, a kulcs-érték, a gráf és az oszlop típusú adatmodelleket. Alapértelmezés szerint az SQL API használatával kommunikálhat a Cosmos DB-vel. Emellett a Cosmos DB szolgáltatás megvalósítja a vezetékprotokollok közös NoSQL API-k, beleértve a Cassandra, MongoDB, Gremlin és az Azure Table Storage. Ez lehetővé teszi, hogy a jól ismert NoSQL kliens-illesztőprogramokat és eszközöket használja a Cosmos-adatbázis sal való interakcióhoz.

## <a name="wire-protocol-compatibility"></a>A vezetékprotokoll kompatibilitása

Az Azure Cosmos DB a gyakori NoSQL-adatbázisok , például a Cassandra, a MongoDB, a Gremlin és az Azure Tables Storage vezetékes protokolljait valósítja meg. Azáltal, hogy a vezetékes protokollok natív megvalósítását közvetlenül és hatékonyan belül Cosmos DB, lehetővé teszi a meglévő ügyfél SDK-k, illesztőprogramok és eszközök a NoSQL-adatbázisok, hogy kölcsönhatásba lépnek a Cosmos DB transzparens módon. A Cosmos DB nem használja az adatbázisok forráskódját a NoSQL-adatbázisok egyikéhez sem biztosít vezetékkompatibilis API-k biztosításához.

Alapértelmezés szerint az Azure Cosmos DB MongoDB API-jával létrehozott új fiókok kompatibilisek a MongoDB vezetékes protokoll 3.6-os verziójával. Minden Olyan MongoDB ügyfél-illesztőprogramnak, amely megérti ezt a protokollverziót, képesnek kell lennie a Cosmos DB-hez való natív csatlakozásra.

![MongoDB-hez készült Azure Cosmos DB API](./media/mongodb-introduction/cosmosdb-mongodb.png)

## <a name="key-benefits"></a>Főbb előnyök

A Cosmos DB mint teljes körűen felügyelt, globálisan elosztott adatbázis szolgáltatásként való ismertetése [itt](introduction.md)ismertetendő. Emellett a népszerű NoSQL API-k vezetékes protokolljainak natív megvalósításával a Cosmos DB a következő előnyöket nyújtja:

* Egyszerűen áttelepítheti az alkalmazást a Cosmos DB-be, miközben megőrzi az alkalmazáslogika jelentős részeit.
* Tartsa az alkalmazást hordozható, és továbbra is felhőalapú gyártó-független.
* A Cosmos DB által működtetett közös NoSQL API-k iparágvezető, pénzügyileg támogatott SLA-i.
* Rugalmasan skálázhatja a kiosztott átviteli és tárolási cosmos-adatbázisok igénye alapján, és csak a szükséges átviteli és tárolási fizetni. Ez jelentős költségmegtakarítást eredményez.
* Kulcsrakész, globális terjesztés több főkiszolgálós replikációval.

## <a name="cosmos-dbs-api-for-mongodb"></a>Cosmos DB API-ja a MongoDB-hoz

Kövesse a rövid útmutatókat egy Azure Cosmos-fiók létrehozásához és a meglévő MongoDB-alkalmazás áttelepítéséhez az Azure Cosmos DB használatához, vagy hozzon létre egy újat:

* [Meglévő MongoDB Node.js webalkalmazás áttelepítése](create-mongodb-nodejs.md).
* [Webalkalmazás létrehozása az Azure Cosmos DB MongoDB-hoz és a .NET SDK-hoz való API-jával](create-mongodb-dotnet.md)
* [Konzolalkalmazás létrehozása az Azure Cosmos DB MongoDB-hoz és Java SDK-hoz való API-jával](create-mongodb-java.md)

## <a name="next-steps"></a>További lépések

Íme, pár hivatkozás az első lépések megtételéhez:

* A [MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB-hez](connect-mongodb-account.md) című oktatóanyagból megtudhatja, hogyan szerezheti be a fiókkapcsolati karakterlánc adatait.
* A [Studio 3T használata az Azure Cosmos DB-vel](mongodb-mongochef.md) című oktatóanyagból megtudhatja, hogyan hozhat létre kapcsolatot a Cosmos-adatbázis és a MongoDB-alkalmazás között a Studio 3T-ben.
* Kövesse a [MongoDB-adatok importálása](mongodb-migrate.md) az Azure Cosmos DB oktatóanyagba az adatok cosmos-adatbázisba történő importálásához.
* Csatlakozzon egy Cosmos fiókhoz [a Robo 3T](mongodb-robomongo.md)használatával.
* További információ a [globálisan elosztott alkalmazások olvasási beállításainak konfigurálásáról.](../cosmos-db/tutorial-global-distribution-mongodb.md)

<sup>Megjegyzés: Ez a cikk az Azure Cosmos DB szolgáltatását ismerteti, amely a MongoDB-adatbázisokkal való vezetékes protokollkompatibilitást biztosítja. A Microsoft nem futtat MongoDB adatbázisokat a szolgáltatás biztosításához. Az Azure Cosmos DB nem áll kapcsolatban a MongoDB, Inc. vállalattal.</sup>
