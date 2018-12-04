---
title: Az Azure Cosmos DB Cassandra API bemutatása
description: Ez a cikk azt ismerteti, hogy miként használható az Azure Cosmos DB meglévő alkalmazások áthelyezésére és új alkalmazások létrehozására a Cassandra API segítségével a már ismert Cassandra-illesztőprogramok és CQL használatával.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 79c131be14de05e84eb21d6d53eefd1aa224df16
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835006"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Az Azure Cosmos DB Cassandra API bemutatása

Az Azure Cosmos DB Cassandra API az [Apache Cassandrára](https://cassandra.apache.org/) írt alkalmazások adattáraként használható. Ez azt jelenti, hogy a meglévő CQLv4-kompatibilis [Apache-illesztőprogramok](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) segítségével a Cassandrára írt alkalmazások mostantól kommunikálhatnak az Azure Cosmos DB Cassandra API-val. A legtöbb esetben az Apache Cassandra használatáról egyetlen kapcsolati sztring módosításával átválthat az Azure Cosmos DB Cassandra API használatára. 

A Cassandra API segítségével a már ismert Cassandra Query Language (CQL), a Cassandra-alapú eszközök (például a cqlsh) és a Cassandra-ügyfélillesztőprogramok használatával kezelhetőek az Azure Cosmos DB-ben tárolt adatok.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Milyen előnyökkel jár az Azure Cosmos DB az Apache Cassandra API-n keresztüli használata?

**Nincs szükség üzemeltetésre**: Teljesen felügyelt felhőszolgáltatásként az Azure Cosmos DB Cassandra API szükségtelenné teszi az operációs rendszerben, a JVM-ben és a yaml-fájlokban lévő számtalan beállítás és azok interakciójának felügyeletét és monitorozását. Az Azure Cosmos DB lehetővé teszi a teljesítmény, a késés, a tárolás és a rendelkezésre állás monitorozását, és konfigurálható riasztásokat is biztosít.

**Teljesítménykezelés**: Az Azure Cosmos DB a szolgáltatói szerződés által garantált alacsony késésű olvasási és írási teljesítményt biztosít az esetek 99%-ában. A felhasználóknak nem kell üzemeltetési többletterheléstől tartaniuk a nagy teljesítmény és a kis késleltetésű olvasás és írás biztosítása érdekében. Vagyis nem kell kézileg tömörítést ütemezniük, törlésjelzőket kezelniük, bloom szűrőket és replikákat beállítaniuk. Az Azure Cosmos DB használatával ezekkel a problémákkal nem kell foglalkoznia, ezek helyett nyugodtan koncentrálhat az alkalmazáslogikára.

**A meglévő kódok és eszközök használata**: Az Azure Cosmos DB protokollszintű kompatibilitást biztosít a meglévő Cassandra SDK-kkal és eszközökkel. A kompatibilitásnak köszönhetően meglévő kódbázisát minimális változtatásokkal az Azure Cosmos DB Cassandra API-val is használhatja.

**Rugalmas teljesítmény és tárolás**: Az Azure Cosmos DB garantált átviteli teljesítményt biztosít a régiók között, a kiosztott átvitel pedig egyszerű Azure Portal-, PowerShell- és CLI-műveletek segítségével skálázható. A táblák tárterülete és az átviteli sebesség rugalmasan, igény szerint skálázható, kiszámítható teljesítmény mellett.

**Globális terjesztés és rendelkezésre állás**: Az Azure Cosmos DB lehetővé teszi az adatok összes Azure-régióba történő globális terjesztését, és helyi kiszolgálását, miközben közel valós idejű adatelérést és magas rendelkezésre állást is biztosít. Az Azure Cosmos DB a régión belül 99,99%-os magas rendelkezésre állást, a régiók között pedig 99,999%-os olvasási és írási rendelkezésre állást biztosít felügyeleti kötelezettségek nélkül. További információ a [Globális adatterjesztés](distribute-data-globally.md) című cikkben található. 

**Megválasztható konzisztenciaszint**: Az Azure Cosmos DB-vel öt jól meghatározott konzisztenciaszint közül választhat a konzisztencia és a teljesítmény közötti optimális kompromisszum elérése érdekében. A konzisztenciaszintek: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. Ezek a részletes, jól meghatározott konzisztenciaszintek lehetővé teszik a fejlesztő számára, hogy ésszerű kompromisszumot alakítson ki a konzisztencia, a rendelkezésre állás és a késés között. További információ a [konzisztenciaszintek](consistency-levels.md) című cikkben található. 

**Nagyvállalati szint**: Az Azure Cosmos DB [megfelelőségi tanúsítványainak](https://www.microsoft.com/trustcenter) köszönhetően a felhasználók biztosak lehetnek benne, hogy a platform használata biztonságos. Az Azure Cosmos DB emellett biztosítja az adatok titkosítását a továbbítás és a tárolás során, valamint egy IP-tűzfalat is tartalmaz, illetve auditnaplókat a vezérlősík tevékenységeinek felügyelete érdekében.

## <a name="next-steps"></a>További lépések

* A következő nyelvspecifikus alkalmazások összeállításával gyorsan hozzákezdhet a Cassandra API-adatok létrehozásához és kezeléséhez:
  - [Node.js-alkalmazás](create-cassandra-nodejs.md)
  - [.NET-alkalmazás](create-cassandra-dotnet.md)
  - [Python-alkalmazás](create-cassandra-python.md)

* Ismerkedés a [Cassandra API-fiókok, -adatbázisok és -táblák létrehozásával](create-cassandra-api-account-java.md) Java-alkalmazás használatával.

* [Mintaadatokat betöltése a Cassandra API táblába](cassandra-api-load-data.md) Java-alkalmazás használatával.

* [Adatok lekérdezése a Cassandra API-fiókból](cassandra-api-query-data.md) Java-alkalmazás használatával.

* További információ az Azure Cosmos DB Cassandra API által támogatott Apache Cassandra-funkciókról a [Cassandra-támogatás](cassandra-support.md) cikkben található.

* Tekintse meg a [gyakori kérdéseket](faq.md#cassandra).
