---
title: Bevezetés az Azure Cosmos DB Cassandra API használatába | Microsoft Docs
description: Ez a cikk azt ismerteti, hogy miként használható az Azure Cosmos DB meglévő alkalmazások áthelyezésére és új alkalmazások létrehozására a Cassandra API segítségével a már ismert Cassandra-illesztőprogramok és CQL használatával.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: na
ms.topic: overview
ms.date: 11/20/2017
ms.author: govindk
ms.openlocfilehash: f2f2257d3ddc22a80600882d2078210ac27cb15d
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43284922"
---
# <a name="introduction-to-azure-cosmos-db-apache-cassandra-api"></a>Bevezetés az Azure Cosmos DB használatába: az Apache Cassandra API

Az Azure Cosmos DB a Cassandra API-t (előzetes verzió) biztosítja az Apache Cassandrához írt olyan alkalmazások számára, amelyekhez a következő prémium képességek szükségesek:

* [Skálázható tárhelyméret és teljesítmény](partition-data.md).
* [Kulcsrakész globális disztribúció](distribute-data-globally.md)
* Az esetek 99%-ában egyszámjegyű ezredmásodperces késés.
* [Öt jól definiált konzisztenciaszint](consistency-levels.md)
* [Az adatok automatikus indexelése](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) anélkül, hogy a felhasználónak sémákat és indexeket kellene kezelnie. 
* [Az iparág legjobb szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/cosmos-db/) által biztosított garantáltan magas szintű rendelkezésre állás

## <a name="what-is-the-azure-cosmos-db-apache-cassandra-api"></a>Mi az Azure Cosmos DB Apache Cassandra API?

Az Apache Cassandra API használatával az Azure Cosmos DB használható adattárként az [Apache Cassandrára](https://cassandra.apache.org/) írt alkalmazásokhoz. Ez azt jelenti, hogy a meglévő [Apache-licencelt, CQLv4-kompatibilis illesztők](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) segítségével a Cassandrára írt alkalmazások mostantól kommunikálhatnak az Azure Cosmos DB Cassandra API-val. Sok esetben egy kapcsolati sztring egyszerű módosításával válthat az Apache Cassandra használatáról az Azure Cosmos DB Apache Cassandra API-jának használatára. A funkció használatával könnyedén hozhat létre és futtathat Cassandra API adatbázis-alkalmazásokat az Azure felhőben az Azure Cosmos DB globális elosztási képességeivel és [iparágvezető átfogó szolgáltatói szerződéseivel](https://azure.microsoft.com/support/legal/sla/cosmos-db), miközben továbbra is alkalmazhatja a Cassandra API használata során megszerzett ismereteit és megszokott eszközeit.

![Azure Cosmos DB Cassandra API](./media/cassandra-introduction/cosmosdb-cassandra.png)

A Cassandra API segítségével a már ismert Cassandra Query Language-alapú eszközök (például a CQLSH) és Cassandra-ügyfél illesztőprogramok segítségével kezelheti az Azure Cosmos DB-ben tárolt adatokat. 

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Milyen előnyökkel jár az Azure Cosmos DB az Apache Cassandra API-n keresztüli használata?

**Nincs szükség üzemeltetésre**: Valóban teljesen felügyelt szolgáltatásként az Azure Cosmos DB biztosítja, hogy a Cassandra API-rendszergazdáknak nem kell aggódniuk az operációs rendszerben, a JVM-ben és a yaml-fájlokban lévő számtalan beállítás és azok összjátékának felügyeletét és monitorozását illetően. Az Azure Cosmos DB biztosítja a teljesítmény, a késés, a tárolás és a rendelkezésre állás, valamint a konfigurálható riasztások monitorozását. 

**Teljesítménykezelés**: Az Azure Cosmos DB az SLA által garantált alacsony késésű olvasási és írási teljesítményt biztosít az esetek 99%-ában. A felhasználóknak nem kell aggódniuk a magas szintű olvasási és írási teljesítmény biztosításához szükséges végeláthatatlan felügyeleti tevékenységek miatt. Ezek rendszerint a tömörítés ütemezését, a törlendő elemek kezelését, a Bloom-szűrők beállítását és a replikakésések kezelését jelentik. Az Azure Cosmos DB segítségével ezekkel a problémákkal nem kell törődnie, és így az alkalmazás megvalósítására összpontosíthat.

**Automatikus indexelés**: Az Azure Cosmos DB automatikusan indexeli a Cassandra API-adatbázisban lévő táblák összes oszlopát. Az Azure Cosmos DB-ben nem szükséges másodlagos indexeket létrehozni a lekérdezések meggyorsítására. Alacsony késleltetésű olvasási és írási teljesítményt, valamint automatikus konzisztens indexelést biztosít. 

**A meglévő kódok és eszközök használata**: Az Azure Cosmos DB protokollszintű kompatibilitást biztosít a meglévő SDK-kkal és eszközökkel. A kompatibilitásnak köszönhetően meglévő kódbázisát minimális változtatásokkal használhatja az Azure Cosmos DB Cassandra API-jával.

**Rugalmas teljesítmény és tárolás**: Az Azure Cosmos platform rugalmas garantált átviteli teljesítményt biztosít a régiók között egyszerű portál-, PowerShell- és CLI-műveletek segítségével. Ahogy az alkalmazás növekszik, kiszámítható teljesítmény mellett, rugalmasan és zökkenőmentesen méretezheti az Azure Cosmos DB tábláit. Az Azure Cosmos DB támogatja a Cassandra API-táblákat, amelyek szinte korlátlanul méretezhető tárterületet biztosítanak. 

**Globális elosztás és rendelkezésre állás**: Az Azure Cosmos DB segítségével az adatokat eloszthatja a különböző Azure-régiókba, így a felhasználók alacsony késéssel és nagy rendelkezésre állás mellett férhetnek azokhoz. Az Azure Cosmos DB a régión belül 99,99%-os, a régiók között 99,999%-os rendelkezésre állást biztosít felügyeleti kötelezettségek nélkül. Az Azure Cosmos DB több mint 30 [Azure-régióban](https://azure.microsoft.com/regions/services/) érhető el. További információ: [Globális adatterjesztés](distribute-data-globally.md). 

**Megválasztható konzisztenciaszint**: Az Azure Cosmos DB-vel öt jól meghatározott konzisztenciaszint közül választhat a konzisztencia és a teljesítmény közötti optimális kompromisszum elérése érdekében. A konzisztenciaszintek: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. Ezek a részletes, jól meghatározott konzisztenciaszintek lehetővé teszik a fejlesztő számára, hogy ésszerű kompromisszumot alakítson ki a konzisztencia, a rendelkezésre állás és a késleltetés között. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a konzisztenciaszintek használatával](consistency-levels.md). 

**Nagyvállalati szint**: Az Azure Cosmos DB [megfelelőségi tanúsítványainak](https://www.microsoft.com/trustcenter) köszönhetően a felhasználók biztosak lehetnek benne, hogy a platform használata biztonságos. Az Azure Cosmos DB emellett biztosítja az adatok titkosítását a továbbítás és a tárolás során, valamint egy IP-tűzfalat is tartalmaz, illetve auditnaplókat a vezérlősík tevékenységeinek felügyelete érdekében.  

<a id="sign-up-now"></a>
## <a name="sign-up-now"></a>Regisztráljon most 

Ha már van Azure-előfizetése, a Cassandra API (előzetes verzió) programhoz való csatlakozásra az [Azure Portalon](https://aka.ms/cosmosdb-cassandra-signup) regisztrálhat.  Ha még csak most ismerkedik az Azure-ral, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/free), amelynek keretében 12 hónapig ingyen használhatja az Azure Cosmos DB-t. Az alábbi lépések végrehajtásával regisztrálhat a Cassandra API (előzetes verzió) programra.

1. Az [Azure Portalon](https://portal.azure.com) kattintson az **Erőforrás létrehozása** > **Adatbázisok** > **Azure Cosmos DB** elemre. 

2. Az Új fiók lapon válassza a **Cassandra** lehetőséget az API mezőben. 

3. Az **Előfizetés** mezőben válassza ki a fiókhoz használni kívánt Azure-előfizetést.

4. Kattintson az **Iratkozzon fel az előzetes verzióra még ma** elemre.

    ![Azure Cosmos DB Cassandra API](./media/cassandra-introduction/cassandra-sign-up.png)

3. Az Iratkozzon fel az előzetes verzióra még ma panelen kattintson az **OK** gombra. 

    Miután beküldte a kérelmet, a **Jóváhagyás függőben** állapotúra vált az Új fiók panelen. 

Miután beküldte a kérelmet, várja meg, amíg e-mailben értesítést kap kérelme jóváhagyásáról. A kérelmek nagy mennyisége miatt egy hét is eltelhet, amíg az e-mail megérkezik. Ezekhez a kérelmekhez nem kell támogatási jegyet leadnia. A kérelmeket azok beérkezési sorrendjében bíráljuk el. 

## <a name="how-to-get-started"></a>Első lépések
Miután csatlakozott az előzetes verziót tesztelő programhoz, a Cassandra API gyors útmutatóit követve hozzon létre egy alkalmazást az API használatával:

* [Gyors útmutató: Cassandra webalkalmazás felépítése a Node.js és az Azure Cosmos DB használatával](create-cassandra-nodejs.md)
* [Gyors útmutató: Cassandra webalkalmazás felépítése a Java és az Azure Cosmos DB használatával](create-cassandra-java.md)
* [Gyors útmutató: Cassandra webalkalmazás felépítése a .NET és az Azure Cosmos DB használatával](create-cassandra-dotnet.md)
* [Gyors útmutató: Cassandra webalkalmazás felépítése a Python és az Azure Cosmos DB használatával](create-cassandra-python.md)

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB Cassandra API-val kapcsolatos információk az Azure Cosmos DB általános dokumentáció részét képezik, azonban íme néhány hivatkozás az első lépések megtételéhez:

* A [Gyors útmutatók](create-cassandra-nodejs.md) utasításait követve hozzon létre egy új fiókot és egy új alkalmazást egy Git minta használatával
* Az [Oktatóanyag](tutorial-develop-cassandra-java.md) utasításait követve hozzon létre programozott módon egy új alkalmazást.
* A [Cassandra-adatok importálását bemutató oktatóanyagot](cassandra-import-data.md) követve importálja meglévő adatait az Azure Cosmos DB-be.
* Tekintse át a [gyakori kérdéseket](faq.md#cassandra).
