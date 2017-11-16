---
title: "Bevezetés az Azure Cosmos DB használatába | Microsoft Docs"
description: "Az Azure Cosmos DB ismertetése. Ez a globálisan elosztott, többmodelles adatbázis az alacsony késés, a rugalmas skálázhatóság és a magas rendelkezésre állás jegyében készült."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: f34790ad670b488159e945be9bf4ba378cc5e94a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="welcome-to-azure-cosmos-db"></a>Üdvözli az Azure Cosmos DB

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázisa. Az Azure Cosmos DB segítségével egyetlen gombnyomással rugalmasan és függetlenül méretezhető az átviteli sebesség és a tárterület, akár több földrajzi Azure-régióra kiterjedően is. A rendszer az átviteli sebességre, a késére, a rendelkezésre állásra és a konzisztenciára vonatkozó garanciákat biztosít átfogó [szolgáltatói szerződésekkel](https://aka.ms/acdbsla) (SLA). Ilyet egyetlen másik adatbázis-szolgáltatás sem kínál. Is [Azure Cosmos DB szabad próbálja](https://azure.microsoft.com/try/cosmosdb/) díjmentesen és kötelezettségvállalás ingyenes Azure-előfizetéssel, nélkül.

![Azure Cosmos-adatbázis a Microsoft globálisan elosztott adatbázis-szolgáltatás rugalmas kibővített, alacsony késleltetés, öt konzisztencia modellek és garantált garantált szolgáltatási szintek átfogó garantált a](./media/introduction/azure-cosmos-db.png)

> [!div class="nextstepaction"]
> [Azure Cosmos DB szabad próbálja](https://azure.microsoft.com/try/cosmosdb/)

## <a name="key-capabilities"></a>Főbb képességek
Globálisan elosztott adatbázis-szolgáltatásként az Azure Cosmos DB az alábbi képességekkel segíti elő, hogy skálázható, gyors válaszidejű alkalmazásokat építhessen:

* **Kulcsrakész globális terjesztés**
    * Tetszőleges számú [Azure-régióban](https://azure.microsoft.com/regions/) [terjesztheti az adatait](distribute-data-globally.md) [egyetlen gombnyomással](tutorial-global-distribution-documentdb.md). Ezáltal ott helyezheti el az adatokat, ahol a felhasználói vannak, így a lehető legkisebb késést garantálhatja a felhasználóknak. 
    * Azure Cosmos DB használatával többhelyű API-k, az alkalmazás mindig tudja, ahol a legközelebbi régiót, és kérelmek küldése a legközelebbi adatközpont. Mindez lehetséges konfigurációs módosítások nélküli. Megadta az írási-régiót szeretné, hogy annyi olvasás-régiók, ha Ön és a többi kell kezelni.

* **Több adatmodell és népszerű API az adatok eléréséhez és lekérdezéséhez**
    * Az atom-rekord-szekvencián (ARS) alapuló adatmodell, amelyre az Azure Cosmos DB épült, natív módon támogat többféle adatmodellt, többek között a dokumentumokat, a diagramokat, a kulcs-értékeket, a táblákat és az oszlopos adatmodelleket.
    * Az alábbi adatmodellekhez készült API-kat több nyelven elérhető SDK-k támogatják:
        * [A DocumentDB API](documentdb-introduction.md): egy séma nélküli JSON adatbázismotor SQL lekérdezési képességeket.
        * [MongoDB API](mongodb-introduction.md): A MongoDB adatbázis-szolgáltatás Cosmos DB platformra épül. Kompatibilis a meglévő MongoDB szalagtárak, illesztőprogramok, eszközök és alkalmazások.
        * [Tábla API](table-introduction.md): prémium szintű képességeket biztosít Azure Table storage alkalmazások beépített egy kulcs-érték dokumentumadatbázis-szolgáltatás.
        * [Graph API (Gremlin)](graph-introduction.md): egy grafikonon adatbázis-szolgáltatás, amely következő a [Apache TinkerPop specification](http://tinkerpop.apache.org/).
        * [Cassandra API](cassandra-introduction.md): A kulcs-érték tároló épülő a [Apache Cassandra](https://cassandra.apache.org/) végrehajtására. 
        * További adatok modellek hamarosan!

* **Igény szerinti rugalmas átviteli sebesség és tárterület, világszerte**
    * Könnyedén méretezhető, adatbázis-átviteli egy [másodpercenként](request-units.md) granularitási, és bármikor módosíthatja. 
    * A méretigények mindenkori kielégítéséhez a tárterület méretét [átláthatóan és automatikusan](partition-data.md) méretezheti.

* **Gyors válaszidejű és alapvető fontosságú alkalmazásokat hozhat létre**
    * Az Azure Cosmos DB az esetek 99%-ában alacsony végpontok közötti késést tud biztosítani az ügyfeleinek. 
    * Egy átlagos 1 KB-os elem esetében a Cosmos DB – ugyanabban az Azure-régióban – az esetek 99%-ában garantálja az olvasások 10 ezredmásodperc alatti, illetve az indexelt írások 15 ezredmásodperc alatti végpontok közötti késését. A késések átlagértékei lényegesen alacsonyabbak ennél (5 ezredmásodperc alattiak).

* **Always On rendelkezésre állás**
    * SLA rendelkezésre állás 99,99 % fiókokhoz egyetlen régión és laza konzisztencia, 99.999 % fiókokhoz több területi olvassa el az összes fiókot a több területi adatbázis rendelkezésre állásának.
    * Bármennyi [Azure-régiót](https://azure.microsoft.com/regions) üzembe helyezhet a magasabb rendelkezésre állás érdekében.
    * [Hibaszimuláció](regional-failover.md) egy vagy több régióban, adatvesztés elleni garanciával. 

* **Globálisan terjesztett alkalmazások írása, a helyes módon**
    * [Az öt konzisztenciamodellből](consistency-levels.md) álló kínálat az SQL által nyújtottakhoz hasonló konzisztenciától a NoSQL-hez hasonló végleges konzisztenciáig minden igényt képes lefedni. 
  
* **Pénzvisszafizetési garancia**
    * Adatai gyorsan célba érnek, vagy visszafizetjük a pénzét. 
    * [Szolgáltatói szerződések](https://aka.ms/acdbsla) a rendelkezésre állásról, a késésről, átviteli sebességről és konzisztenciáról. 

* **Nincs adatbázisséma vagy indexkezelés**
    * Nem kell többé aggódnia amiatt, hogy adatbázissémája és az indexei szinkronban vannak-e az alkalmazása által használt sémával. Nálunk nincsenek sémák. 
    * Az Azure Cosmos DB adatbázismotorja teljesen sémafüggetlen. Automatikusan indexel minden fogadott adatot bármiféle séma vagy index nélkül, és villámgyors lekérdezéseket kínál. 

* **Alacsony tulajdonosi költségek**
    * A 10-szer öt [költséghatékonyabb](https://aka.ms/cosmos-db-tco-paper) mint egy nem kezelt megoldás.
    * Harmadannyiba kerül, mint a DynamoDB.

## <a name="capability-comparison"></a>Képességek összehasonlítása

Az Azure Cosmos DB a relációs és nem relációs adatbázisok legjobb képességeit nyújtja.

| Funkciók | Relációs adatbázisok   | Nem relációs (NoSQL-) adatbázisok |    Azure Cosmos DB |
| --- | --- | --- | --- |
| Globális terjesztés | Nem | Nem | Igen, kulcsrakész terjesztés 30-nál is több régióban, többkiszolgálós API-kkal|
| Horizontális skálázhatóság | Nem | Igen | Igen, a tárolás és átviteli sebesség függetlenül skálázható | 
| Késési garancia | Nem | Igen | Igen, az esetek 99%-ában az olvasások 10 ezredmásodperc, az írások 15 ezredmásodperc alatt | 
| Magas rendelkezésre állás | Nem | Igen | Igen, a Cosmos DB mindig elérhető, PACELC kompromisszumokkal rendelkezik, valamint automatikus és kézi feladatátvételi beállításokat biztosít|
| Adatmodell és API | Relációs és SQL | Többmodelles és OSS API | Többmodelles és SQL, valamint OSS API (továbbiak hamarosan elérhetőek) |
| SLA-k | Igen | Nem | Igen, késésre, átviteli sebességre, konzisztenciára, rendelkezésre állásra vonatkozó átfogó SLA-k |

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Az Azure Cosmos DB előnyeit kihasználó megoldások

Bármely olyan [webes, mobil-, játék és IoT-alkalmazás](use-cases.md) esetén, amelynek nagy mennyiségű írási és olvasási műveletet kell kezelnie [globálisan](distribute-data-globally.md), és rövid válaszidőt kell biztosítani a különféle adatok kezelésekor, előnyt jelent az Azure Cosmos DB [garantált](https://azure.microsoft.com/support/legal/sla/cosmos-db/) rendelkezésre állása, magas átviteli sebessége, kis késése és beállítható konzisztenciája. Megtudhatja, hogyan alkalmazható CosmosDB kapcsolatos [IoT telematikára](use-cases.md#iot-and-telematics), [kereskedelmi és marketing](use-cases.md#retail-and-marketing), [játékok](use-cases.md#gaming) és [webes és mobilalkalmazásokhoz](use-cases.md#web-and-mobile-applications) .

## <a name="next-steps"></a>Következő lépések
Az alábbi rövid útmutatókkal könnyedén elkezdheti az Azure Cosmos DB használatát:

* [Bevezetés az Azure Cosmos DB DocumentDB API-jának használatába](create-documentdb-dotnet.md)
* [Bevezetés az Azure Cosmos DB MongoDB API-jának használatába](create-mongodb-nodejs.md)
* [Bevezetés az Azure Cosmos DB Graph API-jának használatába](create-graph-dotnet.md)
* [Bevezetés az Azure Cosmos DB Table API-jának használatába](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB szabad próbálja](https://azure.microsoft.com/try/cosmosdb/)
