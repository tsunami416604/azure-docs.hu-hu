---
title: Bevezetés az Azure Cosmos DB bemutatása
description: Az Azure Cosmos DB ismertetése. Ez a globálisan elosztott, többmodelles adatbázis az alacsony késés, a rugalmas skálázhatóság és a magas rendelkezésre állás jegyében készült, valamint natív támogatást biztosít a NoSQL-adatokhoz.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: 2a09d5af013e804f33327855fb7b9f2104bc225c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240393"
---
# <a name="welcome-to-azure-cosmos-db"></a>Üdvözli az Azure Cosmos DB

A mai alkalmazásoknak rendkívül érzékenynek és mindig online kell lenniük. Az alacsony késés és a magas rendelkezésre állás elérése érdekében ezen alkalmazások példányait olyan adatközpontokban kell telepíteni, amelyek közel állnak a felhasználókhoz. Az alkalmazásoknak valós időben kell reagálniuk a csúcsidőben bekövetkező nagy használati változásokra, egyre növekvő mennyiségű adatot kell tárolniuk, és ezredmásodpercben elérhetővé kell tenniük ezeket az adatokat a felhasználók számára.

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Egyetlen kattintással a Cosmos DB lehetővé teszi, hogy rugalmasan és függetlenül skálázhatja az átviteli és tárolási átviteli kapacitást és tárhelyet tetszőleges számú Azure-régióban világszerte. Rugalmasan skálázhatja az átviteli és tárolási, és kihasználhatja a gyors, egyszámjegyű-ezredmásodperces adathozzáférés segítségével a kedvenc API,például: SQL, MongoDB, Cassandra, táblázatok, vagy Gremlin. A Cosmos DB átfogó [szolgáltatásiszint-szerződéseket](https://aka.ms/acdbsla) (SL-eket) biztosít az átviteli, késési, rendelkezésre állási és konzisztencia-garanciákhoz, amit más adatbázis-szolgáltatás nem kínál.

Az [Azure Cosmos DB ingyenesen kipróbálható](https://azure.microsoft.com/try/cosmosdb/) Azure-előfizetés nélkül, ingyenesen és kötelezettségvállalások nélkül, vagy használhatja az [Azure Cosmos DB ingyenes szintjét](optimize-dev-test.md#azure-cosmos-db-free-tier) az első 400 RU/s-os és 5 GB-os tárterülettel rendelkező fiók megszerzéséhez.

> [!div class="nextstepaction"]
> [Próbálja ki ingyen az Azure Cosmos DB-t](https://azure.microsoft.com/try/cosmosdb/)

![Az Azure Cosmos DB a Microsoft globálisan elosztott adatbázis-szolgáltatása rugalmas horizontális felskálázási képességgel, garantáltan alacsony késéssel, öt konzisztenciamodellel, valamint átfogó garantált SLA-kkal.](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Főbb előnyök

### <a name="turnkey-global-distribution"></a>Kulcsrakész globális elosztás

Cosmos DB lehetővé teszi, hogy rendkívül érzékeny és magas rendelkezésre állású alkalmazások világszerte. A Cosmos DB transzparens módon replikálja az adatokat, bárhol is legyenek a felhasználók, így a felhasználók a hozzájuk legközelebb eső adatok replikáját is kezelhetik.

Cosmos DB lehetővé teszi, hogy bármikor hozzáadja vagy eltávolítja az Azure-régiók bármelyikét a Cosmos-fiókhoz, egyetlen kattintással. A Cosmos DB zökkenőmentesen replikálja az adatokat a Cosmos-fiókhoz társított összes régióban, miközben az alkalmazás továbbra is magas rendelkezésre állású, a szolgáltatás *több-helymeghatározó* képességeinek köszönhetően. További információt a [globális disztribúciós](distribute-data-globally.md) cikkben talál.

### <a name="always-on"></a>Mindig bekapcsolva

Az Azure-infrastruktúrával való mély integráció és az [átlátható többfőes replikáció](global-dist-under-the-hood.md)miatt a Cosmos DB [99,999%-os magas rendelkezésre állást](high-availability.md) biztosít olvasáshoz és íráshoz egyaránt. Cosmos DB is lehetővé teszi, hogy programozott (vagy portalon keresztül) a Cosmos-fiók regionális feladatátvétel. Ez a képesség segít biztosítani, hogy az alkalmazás célja, hogy a feladatátvétel esetén a regionális katasztrófa.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Az átviteli és tárolási kapacitás rugalmas méretezhetősége világszerte

Az átlátszó horizontális particionálással és több főkiszolgálós replikációval tervezett Cosmos DB soha nem látott rugalmas méretezhetőséget kínál az írásokhoz és olvasásokhoz, a világ minden táján. Rugalmasan skálázhatja fel a több ezer-száz millió kérelmek /sec világszerte egyetlen API-hívás, és csak a szükséges átviteli kapacitásért (és tárolási). Ez a funkció segít a számítási feladatok váratlan kiugrásai nak kezelésében anélkül, hogy túl kell jenedia csúcsot szolgálna. További információ: [particionálás a Cosmos DB,](partitioning-overview.md) [kiépített átviteli a tárolók és adatbázisok,](set-throughput.md)és [a kiosztott átviteli rendszer globális skálázás.](scaling-throughput.md)

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Garantáltan alacsony késleltetés 99%-os, világszerte

A Cosmos DB használatával rendkívül érzékeny, bolygóméretű alkalmazásokat hozhat létre. Az új, több főkiszolgálós replikációs protokolljával és a reteszmentes és [írásra optimalizált adatbázismotorral](index-policy.md)a Cosmos DB kevesebb, mint 10 ms-os késést garantál mindkettőszámára, olvasás (indexelt) és írja a 99 percentilist, a világ minden táján. Ez a funkció lehetővé teszi az adatok tartós betöltését és a villámgyors lekérdezéseket a rendkívül gyors alkalmazások hoz.

### <a name="precisely-defined-multiple-consistency-choices"></a>Pontosan meghatározott, többszörös konzisztenciaválasztási lehetőségek

Amikor globálisan elosztott alkalmazásokat hoz létre a Cosmos DB-ben, többé nem kell extrém [kompromisszumokat kötnie a konzisztencia, a rendelkezésre állás, a késés és az átviteli képesség között.](consistency-levels-tradeoffs.md) A Cosmos DB többfős replikációs protokollja gondosan van kialakítva, hogy [öt jól meghatározott konzisztencia-választást](consistency-levels.md) - kínáljon*erős,* *határolt állság,* *munkamenet*, *konzisztens előtag*és *végleges* – egy intuitív programozási modell hez, alacsony késleltetéssel és magas rendelkezésre állással a globálisan elosztott alkalmazás számára.

### <a name="no-schema-or-index-management"></a>Nincs séma- vagy indexkezelés

Az adatbázisséma és az indexek szinkronizálása az alkalmazás sémájával különösen fájdalmas a globálisan elosztott alkalmazások számára. A Cosmos DB, nem kell foglalkozni a séma vagy index kezelés. Az adatbázis-motor teljesen séma-agnosztikus.  Mivel nincs szükség séma- és indexkezelésre, a sémák áttelepítése során nem kell aggódnia az alkalmazás állásideje miatt. A Cosmos DB [automatikusan indexeli az összes adatot,](index-policy.md) és gyorsan kiszolgálja a lekérdezéseket.

### <a name="battle-tested-database-service"></a>Csata tesztelt adatbázis szolgáltatás

A Cosmos DB egy alapvető szolgáltatás az Azure-ban. A Cosmos DB-t közel egy évtizede használják a Microsoft számos terméke a kritikus fontosságú alkalmazások globális szinten történő fejlesztéséhez, beleértve a Skype-ot, az Xboxot, az Office 365-öt, az Azure-t és még sok mást. Napjainkban a Cosmos DB az Egyik leggyorsabban növekvő azure-szolgáltatás, amelyet számos külső ügyfél és kritikus fontosságú alkalmazás használ, amelyek rugalmas skálázást, kulcsrakész globális terjesztést, több főkiszolgálós replikációt igényelnek az alacsony késleltetés és a magas rendelkezésre állás érdekében. olvas és ír.

### <a name="ubiquitous-regional-presence"></a>Mindenütt jelen lévő regionális jelenlét

A Cosmos DB világszerte az összes Azure-régióban elérhető, beleértve a nyilvános felhőben található 54+ régiót, az [Azure China 21Vianetet](https://www.azure.cn/en-us/), az Azure Germany-t, az Azure Governmentet és az Azure Government for Department of Defense (DoD) régiót. Lásd [a Cosmos DB regionális jelenlétét.](regional-presence.md)

### <a name="secure-by-default-and-enterprise-ready"></a>Alapértelmezés szerint biztonságos és vállalati használatra kész

A Cosmos DB a [megfelelőségi szabványok széles skálájának megfelelő tanúsítvánnyal rendelkezik.](compliance.md) Emellett a Cosmos DB összes adata titkosítva van in-t és mozgásban. A Cosmos DB sorszintű engedélyezést biztosít, és szigorú biztonsági szabványoknak felel meg.

### <a name="significant-tco-savings"></a>Jelentős TCO-megtakarítás

Mivel a Cosmos DB egy teljes körűen felügyelt szolgáltatás, már nem kell kezelnie és működtetnie az adatbázis-szoftverek összetett többadatközpontos központi telepítéseit és frissítéseit, fizetnie kell a támogatásért, a licencelésért vagy a műveletekért, vagy ki kell építenie az adatbázist a maximális számítási feladatokhoz. További információ: [Cost with Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Iparágvezető átfogó SLA-k

A Cosmos DB az első és egyetlen olyan szolgáltatás, amely [iparágvezető átfogó SLA-kat](https://azure.microsoft.com/support/legal/sla/cosmos-db/) kínál, amely 99,999%-os magas rendelkezésre állást, olvasási és írási késleltetést foglal magában a 99 százalékos, garantált átviteli sebességgel és konzisztenciával.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Globálisan elosztott működési analitika és a a kl a natív anatív ban beépített Apache Spark

A [Spark](spark-connector.md) közvetlenül futtatható a Cosmos DB-ben tárolt adatokon. Ez a funkció lehetővé teszi, hogy alacsony késleltetésű, működési elemzéseket globális szinten anélkül, hogy közvetlenül a Cosmos DB-n működő tranzakciós számítási feladatokat végezne. További információ: [Globaldistributed operational analytics](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Alkalmazások fejlesztése a Cosmos DB-n a népszerű nyílt forráskódú szoftver (OSS) API-k használatával

Cosmos DB api-k széles választékát kínálja a Cosmos-adatbázisban tárolt adatokkal való együttműködésre. Alapértelmezés szerint az SQL (egy alapvető API) a Cosmos-adatbázis [lekérdezéséhez.](how-to-sql-query.md) A Cosmos DB a [Cassandra,](cassandra-introduction.md) [a MongoDB,](mongodb-introduction.md)a [Gremlin](graph-introduction.md) és az [Azure Table Storage](table-introduction.md)API-kat is megvalósít. A gyakran használt NoSQL (pl. MongoDB, Cassandra, Gremlin) ügyfélillesztőprogramjait (és eszközeit) közvetlenül a Cosmos-adatbázisba irányíthatja. A gyakran használt NoSQL API-k vezetékes protokolljainak támogatásával a Cosmos DB lehetővé teszi a következőket:

* Egyszerűen áttelepítheti az alkalmazást a Cosmos DB-be, miközben megőrzi az alkalmazáslogika jelentős részeit.
* Tartsa az alkalmazást hordozható, és továbbra is felhőalapú gyártó-független.
* A közös NoSQL API-k piacvezető, pénzügyileg támogatott SL-ekkel ellátott, teljes körűen felügyelt felhőszolgáltatás. 
* Rugalmasan skálázhatja a kiosztott átviteli és tárolási adatbázisok igénye alapján, és csak a szükséges átviteli és tárolási fizetni. Ez jelentős költségmegtakarítást eredményez.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Az Azure Cosmos DB előnyeit kihasználó megoldások

Minden [olyan webes, mobil-, játék- és IoT-alkalmazás,](use-cases.md) amelynek nagy mennyiségű adatot kell kezelnie, olvasást és írást [globális szinten, közel](distribute-data-globally.md) valós válaszidővel a különböző adatok számára, élvezheti a Cosmos DB [garantáltan magas rendelkezésre állása](https://azure.microsoft.com/support/legal/sla/cosmos-db/), nagy átviteli sebesség, alacsony késleltetésű és hangolható konzisztencia előnyeit. Ismerje meg, hogyan használható az Azure Cosmos DB az [IoT és a telematika](use-cases.md#iot-and-telematics), [a kiskereskedelmi és marketing,](use-cases.md#retail-and-marketing) [a játék,](use-cases.md#gaming) valamint [a webes és mobilalkalmazások](use-cases.md#web-and-mobile-applications)létrehozásához.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a Cosmos DB [alapfogalmairól, amelyek kulcsrakész globális elosztást](distribute-data-globally.md) és [particionálást](partitioning-overview.md) és [kiosztott átviteli csúcsot tesznek le.](request-units.md)

Az alábbi rövid útmutatókkal könnyedén elkezdheti az Azure Cosmos DB használatát:

* [Bevezetés az Azure Cosmos DB SQL API használatába](create-sql-api-dotnet.md)
* [Ismerkedés az Azure Cosmos DB MongoDB-hoz való API-jával](create-mongodb-nodejs.md)
* [Bevezetés az Azure Cosmos DB Cassandra API használatába](create-cassandra-dotnet.md)
* [Bevezetés az Azure Cosmos DB Gremlin API használatába](create-graph-dotnet.md)
* [Bevezetés az Azure Cosmos DB Table API használatába](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB ingyenes kipróbálása](https://azure.microsoft.com/try/cosmosdb/)
