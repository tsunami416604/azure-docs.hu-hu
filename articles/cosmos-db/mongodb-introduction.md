---
title: Bevezetés az Azure Cosmos DB API a MongoDB-hez
description: Ismerje meg, hogyan használhatja az Azure Cosmos DB való tárolására és lekérdezésére nagy mennyiségű adat Azure Cosmos DB API használatával a mongodb-hez.
keywords: Az Azure Cosmos DB MongoDB API-jaival
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: overview
ms.date: 12/26/2018
ms.author: rimman
ms.openlocfilehash: f17d8f46bc14297c70c21919abe236fc544facfa
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53787613"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>Az Azure Cosmos DB MongoDB API-jaival

Az [Azure Cosmos DB](introduction.md) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása az alapvető fontosságú alkalmazásokhoz. Az Azure Cosmos DB az [iparág legjobb szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/cosmos-db/) által biztosított [teljes körű, globális terjesztést](distribute-data-globally.md) kínál, valamint [a teljesítmény és a tárterület rugalmas méretezését](partition-data.md) világszerte, az esetek 99%-ában egyszámjegyű ezredmásodperces késéseket és garantált magas rendelkezésre állást. Az Azure Cosmos DB [automatikusan indexeli az adatokat](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) anélkül, hogy a felhasználónak sémákat és indexeket kellene kezelnie. Többmodelles szolgáltatás, amely támogatja a dokumentumokat, a kulcs-értékeket, a diagramokat és az oszlopos adatmodelleket. Alapértelmezés szerint a Cosmos DB SQL API használatával is kezelheti. Ezenkívül a Cosmos DB szolgáltatás közös, például Cassandra, MongoDB, Gremlin és Azure Table Storage nosql-alapú API átviteli protokollt valósítja meg. Ez lehetővé teszi, hogy a jól ismert NoSQL-ügyfélillesztők és -eszközök használata a Cosmos-adatbázis.

## <a name="wire-protocol-compatibility"></a>Átviteli protokoll kompatibilitása

Az Azure Cosmos DB közös NoSQL-adatbázisok, például Cassandra, MongoDB, Gremlin és Azure Storage-táblák átviteli protokollt valósítja meg. Azáltal, hogy a natív megvalósításhoz, az átviteli protokollokat közvetlenül és hatékonyan Cosmos dB-ben, lehetővé teszi meglévő ügyfél SDK-k, illesztőprogramok és eszközök a NoSQL-adatbázisok a Cosmos DB transzparens módon kezelheti. A cosmos DB nem használja az adatbázisok forrás kód bármely, a NoSQL-adatbázisok, amelyek biztosítják az átviteli-kompatibilis API-k.

Alapértelmezés szerint az Azure Cosmos DB MongoDB API a MongoDB átviteli protokoll 3.2-es verziójával kompatibilis. A protokoll 3.4-es verziójában hozzáadva lekérdezési operátorokkal vagy szolgáltatások jelenleg előzetes verzióként érhető el. Bármely MongoDB ügyfélen lévő meghajtó, amely együttműködik a protokollverziók Cosmos DB natív módon csatlakozni képesnek kell lennie.

![Az Azure Cosmos DB MongoDB API-jaival](./media/mongodb-introduction/cosmosdb-mongodb.png) 

## <a name="key-benefits"></a>Főbb előnyök

Fő előnyei a Cosmos DB egy teljes körűen felügyelt, globálisan elosztott adatbázis-szolgáltatás leírása [Itt](introduction.md). Továbbá által natív módon végrehajtási átviteli protokollokat, népszerű nosql-alapú API-t, Cosmos DB az alábbi előnyöket nyújtja:

* Könnyen migrálhatja az alkalmazás a Cosmos DB-hez az alkalmazáslogika jelentős részeinek megőrzése mellett.
* Hordozható tartani az alkalmazást, és továbbra is a felhőben gyártófüggetlen.
* Iparági vezető, pénzügyi felelősséggel vállalt szolgáltatói szerződésekkel kaphat a gyakori nosql-alapú API-k Cosmos DB segítségével.
* A kiosztott átviteli sebesség rugalmasan méretezhető, és a Cosmos-adatbázis tárolási igény szerint, és csak azért fizet az átviteli sebesség és a storage van szüksége. Ez jelentős költségmegtakarítást vezet.
* Kulcsrakész, globális, több főkiszolgálós replikációs terjesztési.

## <a name="cosmos-dbs-api-for-mongodb"></a>Cosmos DB MongoDB API-jaival

A rövid útmutatók, hozzon létre egy Cosmos-fiókot, és a meglévő MongoDB-alkalmazások Azure Cosmos dB-ben való áttelepítéséhez hajtsa végre, vagy hozhat létre egy újat:

* [Egy meglévő MongoDB Node.js-webalkalmazás migrálása](create-mongodb-nodejs.md).
* [-Webalkalmazás létrehozása Azure Cosmos DB API a mongodb-hez és a .NET SDK használatával](create-mongodb-dotnet.md)
* [Azure Cosmos DB API használatával a MongoDB-hez és a Java SDK Konzolalkalmazás létrehozása](create-mongodb-java.md)

## <a name="next-steps"></a>További lépések

Íme, pár hivatkozás az első lépések megtételéhez:

* Kövesse a [egy Azure Cosmos DB MongoDB-alkalmazás csatlakoztatása](connect-mongodb-account.md) oktatóanyagból megtudhatja, hogyan tehet szert a fiók kapcsolati karakterlánc adatait.
* Kövesse a [az Azure Cosmos DB használatát a Studio 3T](mongodb-mongochef.md) oktatóanyaggal, amelyből megismerheti a Studióban 3-T. az, Cosmos-adatbázis és a MongoDB-alkalmazások közötti kapcsolat létrehozása
* Kövesse a [importálhatja MongoDB-adatok az Azure Cosmos DB-be](mongodb-migrate.md) oktatóanyag az adatok importálása egy Cosmos-adatbázis.
* Csatlakozhat egy Cosmos fiók használatával [Robo 3T](mongodb-robomongo.md).
* Ismerje meg, hogyan [konfigurálása olvashatók beállítások globálisan elosztott alkalmazások](../cosmos-db/tutorial-global-distribution-mongodb.md).

<sup>Megjegyzés: Ez a cikk ismerteti az Azure Cosmos DB által biztosított átviteli protokoll kompatibilitás MongoDB adatbázisok funkciója. A Microsoft nem fut a szolgáltatás a MongoDB-adatbázisokat. Az Azure Cosmos DB sincs társítva a MongoDB, Inc.</sup>
