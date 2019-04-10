---
title: Alapvető ismeretek az Azure Cosmos DB-ről
description: Az Azure Cosmos DB ismertetése. Ez a globálisan elosztott, többmodelles adatbázis az alacsony késés, a rugalmas skálázhatóság és a magas rendelkezésre állás jegyében készült, valamint natív támogatást biztosít a NoSQL-adatokhoz.
author: rimman
ms.service: cosmos-db
ms.topic: overview
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: a965db4334e88d032f767bd7b855dea6f3ff174f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280944"
---
# <a name="welcome-to-azure-cosmos-db"></a>Üdvözli az Azure Cosmos DB

A napjaink alkalmazásai válaszidejű, és mindig online szükségesek. Alacsony késéssel és magas rendelkezésre állás elérése érdekében ezeket az alkalmazásokat példányát kell üzembe helyezni a felhasználók közelében lévő adatközpontokban. Alkalmazások kell válaszolni a valós idejű használati nagy változások csúcsidőszakban, egyre növekvő mennyiségű adat tárolására, és az adatok elérhetővé tétele a felhasználók számára (MS).

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Egyetlen kattintással a Cosmos DB lehetővé teszi, hogy rugalmasan és egymástól függetlenül méretezhető teljesítményt és tárolókapacitást tetszőleges mennyiségű Azure-régiókban világszerte. Rugalmasan méretezhető teljesítményt és tárolókapacitást, és kihasználhatja a kedvenc, beleértve az SQL, a MongoDB, a Cassandra, a táblák vagy a Gremlin API-val gyors, számjegy-ezredmásodperces adatelérési. A cosmos DB kínál átfogó [szolgáltatói szerződések](https://aka.ms/acdbsla) (SLA) átviteli sebesség, a késés, a rendelkezésre állás és a konzisztencia megvalósulásának, valami más adatbázis-szolgáltatást kínál.

Az [Azure Cosmos DB-t kipróbálhatja ingyenesen](https://azure.microsoft.com/try/cosmosdb/), Azure-előfizetés, díjfizetés és elköteleződés nélkül.

> [!div class="nextstepaction"]
> [Próbálja ki ingyen az Azure Cosmos DB-t](https://azure.microsoft.com/try/cosmosdb/)

![Az Azure Cosmos DB a Microsoft globálisan elosztott adatbázis-szolgáltatása rugalmas horizontális felskálázási képességgel, garantáltan alacsony késéssel, öt konzisztenciamodellel, valamint átfogó garantált SLA-kkal.](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Főbb előnyök

### <a name="turnkey-global-distribution"></a>Kulcsrakész globális disztribúció

A cosmos DB lehetővé teszi, hogy hozhat létre a rendkívül rugalmas és magas rendelkezésre állású globális alkalmazások. A cosmos DB transzparensen replikálja az adatait, ahol a felhasználók is, így a felhasználók kezelhessék azokat legközelebb van az adatok replikáját.

A cosmos DB teszi lehetővé hozzáadása vagy eltávolítása az Azure-régiók és a Cosmos-fiók bármelyike bármikor, egyetlen kattintással. A cosmos DB zökkenőmentesen replikálja az adatokat a Cosmos-fiókjához társított, miközben az alkalmazás továbbra is magas rendelkezésre állású, Köszönjük minden régióban az *többkiszolgálós* a szolgáltatás funkcióit. További információkért lásd: a [globális terjesztés](distribute-data-globally.md) cikk.

### <a name="always-on"></a>Folyamatos üzem

Szoros integráció az Azure infrastruktúra-hez tartozó és [transzparens több főkiszolgálós replikációs](global-dist-under-the-hood.md), Cosmos DB biztosítja [99,999 %-os rendelkezésre állással](high-availability.md) mindkét olvasási és írási. A cosmos DB is lehetőséget nyújt a programozott módon (vagy a portálon keresztül) meghívni a regionális feladatátvétel Cosmos-fiókja. Ez a funkció biztosítja, hogy az alkalmazás célja, hogy feladatátvétel esetén a regionális vészhelyreállítás.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Rugalmasan méretezhető átviteli sebesség és tárterület, világszerte

A transzparens horizontális particionálást és több főkiszolgálós replikációt, Cosmos DB nyújt a páratlan rugalmas méretezhetőség az írási és olvasási, az összes világszerte. Vertikális felskálázás több száz vonatkozó kérelmek/másodperc, a világ minden pontján, egyetlen API hívással több millió, több ezer eszközről rugalmasan, és csak azért fizet az átviteli sebesség (és tárolási) van szüksége. Ez a képesség segít csúcsértéke a túltervezés nélkül a számítási feladatok nem várt kiugrások kezelésére. További információkért lásd: [a Cosmos DB particionálási](partitioning-overview.md), [kiosztott átviteli sebesség a tárolók és adatbázisok](set-throughput.md), és [kiosztott átviteli kapacitás méretezése globálisan](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Garantált alacsony késés 99 százalékon, világszerte

Cosmos DB használatával hozhat létre válaszidejű, globális méretű alkalmazások. Az új több főkiszolgálós replikációs protokollal és zárolástól mentes és [írásra optimalizált adatbázismotort](index-policy.md), Cosmos DB garantálja a kevesebb mint 10 ms késések is, olvassa be és 99 százalékon, a világ minden tájáról írási műveletek (indexelt) . Ez a funkció lehetővé teszi tartósan adatfeldolgozást és villámgyors lekérdezéseket válaszidejű alkalmazások.

### <a name="precisely-defined-multiple-consistency-choices"></a>Pontosan definiált konzisztenciaválaszték több

Globálisan elosztott alkalmazások létrehozása a Cosmos dB-ben, amikor már nem rendelkezik, hogy rendkívül [kompromisszumot kínál a konzisztencia, elérhetőség, késés és átviteli sebesség között](consistency-levels-tradeoffs.md). Cosmos DB több főkiszolgálós replikációs protokoll gondosan célja, hogy ajánlat [öt jól definiált konzisztenciaszintet](consistency-levels.md) - *erős*, *korlátozott frissesség*, *munkamenet*, *konzisztens előtag*, és *végleges* – kínál egy intuitív programozási modellhez, alacsony késéssel és magas rendelkezésre állás az a globálisan elosztott alkalmazás.

### <a name="no-schema-or-index-management"></a>Nincs séma vagy index kezelése

Adatbázissémája és az indexek tartja szinkronban maradjon az alkalmazási sémának különösen nehéznek globálisan elosztott alkalmazások esetében. A Cosmos DB használatával nem kell sémákkal vagy indexkezeléssel foglalkozik. A adatbázismotorja teljesen sémafüggetlen.  Nincs sémákat és indexeket felügyeleti nem szükséges, mivel is nem kell aggódnia az alkalmazások állásidő sémák áttelepítésekor. A cosmos DB [automatikusan indexeli az összes adatot](index-policy.md) és gyors lekérdezéseket szolgál.

### <a name="battle-tested-database-service"></a>Adatbázis-szolgáltatás többen tesztelték a gyakorlatban

A cosmos DB eligazodást szolgáltatása az Azure-ban. Majdnem egy évtizede nyújt védelmet, a Cosmos DB rendelkezik használja számos Microsoft-termékek, az üzletmenet szempontjából kritikus fontosságú alkalmazások kínáljon globális léptékben, beleértve a Skype, Xbox, az Office 365, Azure és sok más. Ma Cosmos DB az egyik leggyorsabb egyre bővülő szolgáltatás számos külső ügyfelek esetében, és kis késésű és magas rendelkezésre állású mindkét rugalmasan méretezhető, kulcsrakész globális disztribúciót, több főkiszolgálós replikációs igénylő kritikus fontosságú alkalmazások által használt Azure-ban olvasási és írási műveletek.

### <a name="ubiquitous-regional-presence"></a>Széles körben használt területi jelenlét

A cosmos DB érhető el az összes Azure-régiókban világszerte, beleértve a nyilvános felhő, 54 + régióval [Azure China 21Vianet](https://www.azure.cn/en-us/), Azure Germany, az Azure Government és az Azure Government a védelmi Minisztérium (DoD). Lásd: [Cosmos DB regionális jelenlét](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Alapértelmezés szerint és készen áll a vállalati védelme

A cosmos DB minősítéssel egy [számos megfelelőségi szabvány](compliance.md). Inaktív állapotban, és a mozgásban lévő Cosmos DB-ben minden adatokat is titkosítja. A cosmos DB szolgáltatás sor szintű hitelesítést, és szigorú biztonsági szabványoknak megfelelő.

### <a name="significant-tco-savings"></a>Teljes bekerülési Költséget jelentős megtakarítást

Mivel a Cosmos DB egy teljes körűen felügyelt szolgáltatás, nem kell többé kezelése és összetett több adatközpontban üzemelő példányok és az adatbázis-szoftver frissítései, a támogatás, a licencelési vagy a műveletek kell fizetnie, illetve csúcsértéke az adatbázis kiépítése a. További információkért lásd: [optimalizálhatja költségeit a Cosmos DB-vel](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Iparágvezető átfogó SLA-k

A cosmos DB, az első és az csak szolgáltatást kínáló [iparágvezető átfogó SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/) felölelő 99,999 %-os rendelkezésre állással, olvasási és írási késés 99 százalékon, átviteli sebesség és a konzisztencia garantált.

### <a name="globally-distributed-operational-analytics-with-spark"></a>Globálisan elosztott működési elemzések a Spark segítségével

Futtathat [Spark](spark-connector.md) közvetlenül a Cosmos DB-ben tárolt adatokon. Ez a funkció lehetővé teszi a kis késésű, a üzemeltetési analytics globális méretekben tranzakciós munkaterhelések kezelésére közvetlenül a Cosmos DB szemben működő befolyásolása nélkül. További információkért lásd: [globálisan elosztott működési elemzések](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-nosql-apis"></a>A Cosmos DB, népszerű nosql-alapú API-k használata alkalmazások fejlesztéséhez

A cosmos DB API-k az a Cosmos-adatbázisban tárolt adatok közül választhat kínál. Alapértelmezés szerint [használhatja az SQL](how-to-sql-query.md) (a core API-t) a Cosmos-adatbázis lekérdezéséhez. A cosmos DB is megvalósul az API-k [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) és [Azure Table Storage](table-introduction.md). Mutathat ügyfél illesztőprogramok (és eszközök) számára a gyakran használt nosql-alapú (például Cassandra, MongoDB, Gremlin) közvetlenül az a Cosmos database. A gyakran használt nosql-alapú API-k átviteli protokollok támogatása révén, Cosmos DB teszi lehetővé:

* Könnyen migrálhatja az alkalmazás a Cosmos DB-hez az alkalmazáslogika jelentős részeinek megőrzése mellett.
* Hordozható tartani az alkalmazást, és továbbra is a felhőben gyártófüggetlen.
* Egy teljes körűen felügyelt felhőszolgáltatás iparági vezető, pénzügyi felelősséggel vállalt szolgáltatói szerződésekkel kaphat a gyakori nosql-alapú API-k. 
* Rugalmasan skálázhatja a kiosztott átviteli sebesség és a tárolás igény szerint az adatbázisok, és csak azért fizet az átviteli sebesség és a storage van szüksége. Ez jelentős költségmegtakarítást vezet.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Az Azure Cosmos DB előnyeit kihasználó megoldások

Bármely [webes, mobil-, játék és IoT-alkalmazást](use-cases.md) , amelyek nagy mennyiségű adat, olvasási, kezelnie kell, és az írási műveleteknél egy [globális méretezhetőség](distribute-data-globally.md) csaknem valós válasz ideje a különféle adatok számára, Cosmos DB [garantált magas rendelkezésre állás](https://azure.microsoft.com/support/legal/sla/cosmos-db/), nagy átviteli sebességet, közel valós idejű és hangolható konzisztencia. Ismerje meg hogyan Azure Cosmos DB segítségével hozhat létre [IoT és telematika](use-cases.md#iot-and-telematics), [kiskereskedelem és marketing](use-cases.md#retail-and-marketing), [játék](use-cases.md#gaming) és [webes és mobil alkalmazások](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>További lépések

Tudjon meg többet a Cosmos DB alapvető fogalmak [kulcsrakész globális disztribúció](distribute-data-globally.md) és [particionálás](partitioning-overview.md) és [kiosztott átviteli sebesség](request-units.md).

Az alábbi rövid útmutatókkal könnyedén elkezdheti az Azure Cosmos DB használatát:

* [Azure Cosmos DB SQL API használatának első lépései](create-sql-api-dotnet.md)
* [Ismerkedés az Azure Cosmos DB API a mongodb-hez](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API használatának első lépései](create-cassandra-dotnet.md)
* [Azure Cosmos DB Gremlin API használatának első lépései](create-graph-dotnet.md)
* [Az Azure Cosmos DB Table API használatának első lépései](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB ingyenes kipróbálása](https://azure.microsoft.com/try/cosmosdb/)
