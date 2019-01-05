---
title: Az Azure Cosmos DB Cassandra API bemutatása
description: Ez a cikk azt ismerteti, hogy miként használható az Azure Cosmos DB meglévő alkalmazások áthelyezésére és új alkalmazások létrehozására a Cassandra API segítségével a már ismert Cassandra-illesztőprogramok és CQL használatával.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9d948164177b7acd2df722078ae590f409c8e795
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036511"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Az Azure Cosmos DB Cassandra API bemutatása

Az Azure Cosmos DB Cassandra API az [Apache Cassandrára](https://cassandra.apache.org/) írt alkalmazások adattáraként használható. Ez azt jelenti, hogy a meglévő CQLv4-kompatibilis [Apache-illesztőprogramok](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) segítségével a Cassandrára írt alkalmazások mostantól kommunikálhatnak az Azure Cosmos DB Cassandra API-val. A legtöbb esetben az Apache Cassandra használatáról egyetlen kapcsolati sztring módosításával átválthat az Azure Cosmos DB Cassandra API használatára. 

A Cassandra API segítségével a már ismert Cassandra Query Language (CQL), a Cassandra-alapú eszközök (például a cqlsh) és a Cassandra-ügyfélillesztőprogramok használatával kezelhetőek az Azure Cosmos DB-ben tárolt adatok.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Milyen előnyökkel jár az Azure Cosmos DB az Apache Cassandra API-n keresztüli használata?

**Nincs az operations management**: Azure Cosmos DB Cassandra API egy teljes körűen felügyelt felhőszolgáltatás, mentesíti a terhelés kezeléséhez, és az operációs rendszer, a JVM, és a yaml-fájlok és a kapcsolati beállításokat a számtalan figyelésére. Az Azure Cosmos DB lehetővé teszi a teljesítmény, a késés, a tárolás és a rendelkezésre állás monitorozását, és konfigurálható riasztásokat is biztosít.

**Alkalmazásteljesítmény-felügyelet**: Azure Cosmos DB biztosítja a garantált alacsony késésű Olvasás és írás 99 százalékon, biztonsági másolatot készít az SLA-k. A felhasználóknak nem kell üzemeltetési többletterheléstől tartaniuk a nagy teljesítmény és a kis késleltetésű olvasás és írás biztosítása érdekében. Vagyis nem kell kézileg tömörítést ütemezniük, törlésjelzőket kezelniük, bloom szűrőket és replikákat beállítaniuk. Az Azure Cosmos DB használatával ezekkel a problémákkal nem kell foglalkoznia, ezek helyett nyugodtan koncentrálhat az alkalmazáslogikára.

**Meglévő kódot és az eszközök**: Az Azure Cosmos DB az átviteli protokoll kompatibilitási szint és a meglévő Cassandra SDK-kat és eszközöket biztosít. A kompatibilitásnak köszönhetően meglévő kódbázisát minimális változtatásokkal az Azure Cosmos DB Cassandra API-val is használhatja.

**Átviteli sebesség és tárterület rugalmasságra**: Azure Cosmos DB biztosítja, garantált átviteli minden régióban és skálázhatja a kiosztott átviteli sebesség az Azure portal, PowerShell vagy parancssori felület műveletek. A táblák tárterülete és az átviteli sebesség rugalmasan, igény szerint skálázható, kiszámítható teljesítmény mellett.

**Globális terjesztés és a rendelkezésre állási**: Az Azure Cosmos DB lehetővé teszi globálisan adatokat terjeszteni az összes Azure-régióban és az adatok helyi közel valós idejű adatelérés és magas rendelkezésre állást biztosítva. Az Azure Cosmos DB a régión belül 99,99%-os magas rendelkezésre állást, a régiók között pedig 99,999%-os olvasási és írási rendelkezésre állást biztosít felügyeleti kötelezettségek nélkül. További információ a [Globális adatterjesztés](distribute-data-globally.md) című cikkben található. 

**Konzisztencia többféle**: Az Azure Cosmos DB öt jól definiált konzisztenciaszintet elérése érdekében a konzisztencia és a teljesítmény közötti optimális kompromisszumot kínál a kiválasztott biztosít. A konzisztenciaszintek: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. Ezek a részletes, jól meghatározott konzisztenciaszintek lehetővé teszik a fejlesztő számára, hogy ésszerű kompromisszumot alakítson ki a konzisztencia, a rendelkezésre állás és a késés között. További információ a [konzisztenciaszintek](consistency-levels.md) című cikkben található. 

**Nagyvállalati szintű**: Az Azure cosmos DB biztosít [megfelelőségnek](https://www.microsoft.com/trustcenter) annak érdekében, hogy a felhasználók biztonságosan használhatják a platformot. Az Azure Cosmos DB emellett biztosítja az adatok titkosítását a továbbítás és a tárolás során, valamint egy IP-tűzfalat is tartalmaz, illetve auditnaplókat a vezérlősík tevékenységeinek felügyelete érdekében.

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
