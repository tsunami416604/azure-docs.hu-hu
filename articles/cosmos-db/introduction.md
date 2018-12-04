---
title: Alapvető ismeretek az Azure Cosmos DB-ről
description: Az Azure Cosmos DB ismertetése. Ez a globálisan elosztott, többmodelles adatbázis az alacsony késés, a rugalmas skálázhatóság és a magas rendelkezésre állás jegyében készült, valamint natív támogatást biztosít a NoSQL-adatokhoz.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: overview
ms.date: 04/08/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 89f5ae27e7ce3ec3155fd3fdbf42bb0f4322aa10
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835063"
---
# <a name="welcome-to-azure-cosmos-db"></a>Üdvözli az Azure Cosmos DB

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázisa. Az Azure Cosmos DB segítségével egyetlen gombnyomással rugalmasan és függetlenül méretezhető az átviteli sebesség és a tárterület, akár több földrajzi Azure-régióra kiterjedően is. A rendszer az átviteli sebességre, a késére, a rendelkezésre állásra és a konzisztenciára vonatkozó garanciákat biztosít átfogó [szolgáltatói szerződésekkel](https://aka.ms/acdbsla) (SLA). Ilyet egyetlen másik adatbázis-szolgáltatás sem kínál. Az [Azure Cosmos DB-t kipróbálhatja ingyenesen](https://azure.microsoft.com/try/cosmosdb/), Azure-előfizetés, díjfizetés és elköteleződés nélkül.

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB ingyenes kipróbálása](https://azure.microsoft.com/try/cosmosdb/)

![Az Azure Cosmos DB a Microsoft globálisan elosztott adatbázis-szolgáltatása rugalmas horizontális felskálázási képességgel, garantáltan alacsony késéssel, öt konzisztenciamodellel, valamint átfogó garantált SLA-kkal.](./media/introduction/azure-cosmos-db.png)

## <a name="key-capabilities"></a>Főbb képességek
Globálisan elosztott adatbázis-szolgáltatásként az Azure Cosmos DB segítségével leegyszerűsíthető a skálázható, gyors válaszidejű alkalmazások globális szintű létrehozása:

* **Kulcsrakész globális terjesztés**
    * Tetszőleges számú [Azure-régióban](https://azure.microsoft.com/regions/) [terjesztheti az adatait](distribute-data-globally.md) [egyetlen gombnyomással](tutorial-global-distribution-sql-api.md). Ezáltal ott helyezheti el az adatokat, ahol a felhasználói vannak, így a lehető legkisebb késést garantálhatja a felhasználóknak. 
    * Az Azure Cosmos DB többkiszolgálós API felületeivel az alkalmazás mindig tudni fogja, hol található a legközelebbi régió, és a legközelebbi adatközpontnak küldi el a kérelmeket. Mindehhez nem kell módosítania a konfigurációt. Megadhatja az írási régiót és tetszőleges számú olvasási régiót, a többit automatikusan elvégzi a rendszer.
    * A többkiszolgálós API-knak köszönhetően az alkalmazásokat nem kell újból üzembe kell helyeznie, és továbbra is magas rendelkezésre állásúak maradnak, amikor régiókat ad hozzá vagy távolít el az Azure Cosmos DB-adatbázisból.

* **Több adatmodell és népszerű API az adatok eléréséhez és lekérdezéséhez**
    * Az atom-rekord-szekvencián (ARS) alapuló adatmodell, amelyre az Azure Cosmos DB épült, natív módon támogat többféle adatmodellt, többek között a dokumentumokat, a diagramokat, a kulcs-értékeket, a táblákat és az oszlopcsalád-adatmodelleket.
    * Az alábbi adatmodellekhez készült API-kat több nyelven elérhető SDK-k támogatják:
        * [SQL API](sql-api-introduction.md): Egy séma nélküli JSON-adatbázismotor részletes SQL-lekérdezési képességekkel.
        * [MongoDB API](mongodb-introduction.md): Nagymértékben méretezhető, *szolgáltatásként elérhető MongoDB*, amely az Azure Cosmos DB platformra épül. Kompatibilis a meglévő MongoDB-kódtárakkal, -illesztőkkel, -eszközökkel és -alkalmazásokkal.
        * [Cassandra API](cassandra-introduction.md): Globálisan elosztott, szolgáltatásként elérhető Cassandra, amely az Azure Cosmos DB platformra épül. Kompatibilis a meglévő [Apache Cassandra](https://cassandra.apache.org/)-kódtárakkal, -illesztőkkel, -eszközökkel és -alkalmazásokkal.
        * [Gremlin API](graph-introduction.md): Teljes mértékben felügyelt, vízszintesen méretezhető gráfadatbázis-szolgáltatás, amellyel egyszerűen készíthet és futtathat olyan alkalmazásokat, amelyek az Open Gremlin API-kat támogató (az Apache Gremlin [Apache TinkerPop-specifikáció](http://tinkerpop.apache.org/) alapján), magas szinten csatlakoztatott adatkészletekkel működnek együtt.
        * [Table API](table-introduction.md): Egy kulcs/érték adatbázis-szolgáltatás, amely prémium szintű képességeket (például automatikus indexelést, garantáltan alacsony késést és globális terjesztést) biztosít a meglévő Azure Table Storage-alkalmazások számára anélkül, hogy az alkalmazásokat módosítani kellene.
        * Hamarosan további adatmodellek és API-k is elérhetővé válnak!

* **Az átviteli sebesség és a tárterület rugalmas és független méretezése igény szerint, világszerte**
    * Az adatbázis átviteli sebességét könnyedén méretezheti [másodpercalapú](request-units.md) részletességgel, és igény szerint bármikor megváltoztathatja. 
    * A méretigények mindenkori kielégítéséhez a tárterület méretét [átláthatóan és automatikusan](partition-data.md) méretezheti.

* **Gyors válaszidejű és alapvető fontosságú alkalmazásokat hozhat létre**
    * Az Azure Cosmos DB az esetek 99%-ában alacsony végpontok közötti késést tud biztosítani az ügyfeleinek. 
    * Egy átlagos 1 KB-os elem esetében a Cosmos DB garantálja a végpontok közötti késés olvasások 10 ezredmásodperc alatti és az indexelt írások az esetek 99 ugyanazon Azure-régióban található 10 ezredmásodperc alatti. A késések átlagértékei lényegesen alacsonyabbak ennél (5 ezredmásodperc alattiak).

* **Always On rendelkezésre állás**
    * 99,99%-os rendelkezésre állású SLA minden egyrégiós adatbázisfiókhoz, valamint 99,999%-os olvasási rendelkezésre állás minden többrégiós adatbázisfiókhoz.
    * Bármennyi [Azure-régiót](https://azure.microsoft.com/regions) üzembe helyezhet a magasabb rendelkezésre állás és a nagyobb teljesítmény elérése érdekében.
    * Dinamikus régióprioritás-beállítást és [hibaszimulációt](high-availability.md) végezhet el egy vagy több régióban, adatvesztés elleni garanciával a teljes alkalmazás végpontok közötti (nem csak az adatbázishoz kapcsolódó) rendelkezésre állásának teszteléséhez. 

* **Globálisan terjesztett alkalmazások írása, a helyes módon**
    * Az öt jól definiált, célszerűen alkalmazható és intuitív [konzisztenciamodellből](consistency-levels.md) álló kínálat az SQL által nyújtottakhoz hasonló konzisztenciától az enyhén korlátozott NoSQL-hez hasonló végleges konzisztenciáig minden igényt képes lefedni. 
  
* **Pénzvisszafizetési garancia**
    * Iparágvezető, pénzügyi felelősséggel vállalt, átfogó [szolgáltatói szerződések](https://aka.ms/acdbsla) (SLA-k) az alapvető fontosságú adatok rendelkezésre állásáról, késéséről, átviteli sebességéről és konzisztenciájáról. 

* **Nincs adatbázisséma vagy indexkezelés**
    * Gyors alkalmazásséma-iteráció anélkül, hogy aggódnia kellene az adatbázisséma és/vagy az indexkezelés miatt.
    * Az Azure Cosmos DB adatbázismotorja teljesen sémafüggetlen. Automatikusan indexel minden fogadott adatot bármiféle séma vagy index nélkül, és villámgyors lekérdezéseket kínál. 

* **Alacsony tulajdonosi költségek**
    * Öt-tízszer költséghatékonyabb, mint egy nem felügyelt vagy egy helyszíni NoSQL-megoldás.
    * Harmadannyiba kerül, mint az AWS DynamoDB vagy a Google Spanner.

## <a name="capability-comparison"></a>Képességek összehasonlítása

Az Azure Cosmos DB a hagyományos relációs és a nem relációs adatbázisok legjobb képességeit nyújtja.

| Funkciók | Relációs adatbázisok   | Nem relációs (NoSQL-) adatbázisok |    Azure Cosmos DB |
| --- | --- | --- | --- |
| Globális terjesztés | Nem | Nem | Igen, kulcsrakész terjesztés 30-nál is több régióban, többkiszolgálós API-kkal|
| Horizontális skálázhatóság | Nem | Igen | Igen, a tárolás és átviteli sebesség függetlenül skálázható | 
| Késési garancia | Nem | Igen | Igen, az olvasási 99 %-át < 10 ms, az írások < 10 ms | 
| Magas rendelkezésre állás | Nem | Igen | Igen, az Azure Cosmos DB mindig elérhető, jól meghatározott PACELC kompromisszumokkal rendelkezik, valamint automatikus és kézi feladatátvételi beállításokat is lehetővé tesz|
| Adatmodell és API | Relációs és SQL | Többmodelles és OSS API | Többmodelles és SQL, valamint OSS API |
| SLA-k | Igen | Nem | Igen, késésre, átviteli sebességre, konzisztenciára, rendelkezésre állásra vonatkozó átfogó SLA-k |

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Az Azure Cosmos DB előnyeit kihasználó megoldások

Bármely olyan [webes, mobil-, játék és IoT-alkalmazás](use-cases.md) esetén, amelynek nagy mennyiségű adatot, illetve írási és olvasási műveletet kell kezelnie [globálisan](distribute-data-globally.md), és csaknem valós válaszidőt kell biztosítania a különféle adatok kezelésekor, előnyt jelent az Azure Cosmos DB [garantált](https://azure.microsoft.com/support/legal/sla/cosmos-db/) magas szintű rendelkezésre állása, magas átviteli sebessége, kis késése és beállítható konzisztenciája. Megtudhatja, hogyan alkalmazható az Azure Cosmos DB az [IoT és telematika](use-cases.md#iot-and-telematics), a [kiskereskedelem és marketing](use-cases.md#retail-and-marketing), a [játékok](use-cases.md#gaming) és a [webes és mobilalkalmazások](use-cases.md#web-and-mobile-applications) területén.

## <a name="next-steps"></a>További lépések
Az alábbi rövid útmutatókkal könnyedén elkezdheti az Azure Cosmos DB használatát:

* [Bevezetés az Azure Cosmos DB SQL API használatába](create-sql-api-dotnet.md)
* [Bevezetés az Azure Cosmos DB MongoDB API használatába](create-mongodb-nodejs.md)
* [Bevezetés az Azure Cosmos DB Cassandra API használatába](create-cassandra-dotnet.md)
* [Bevezetés az Azure Cosmos DB Gremlin API használatába](create-graph-dotnet.md)
* [Bevezetés az Azure Cosmos DB Table API használatába](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB ingyenes kipróbálása](https://azure.microsoft.com/try/cosmosdb/)
