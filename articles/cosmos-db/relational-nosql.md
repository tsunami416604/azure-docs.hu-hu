---
title: Az Azure Cosmos DB NoSQL és a relációs adatbázisok közötti különbségek ismertetése
description: Ez a cikk felsorolja a NoSQL és a relációs adatbázisok közötti különbségeket
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896622"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>A NoSQL és a relációs adatbázisok közötti különbségek megértése

Ez a cikk a NoSQL-adatbázisok relációs adatbázisokhoz kapcsolódó legfontosabb előnyeinek egy részét számba veszi. A NoSQL-rel való együttműködés néhány kihívását is megvitatjuk. A létező különböző adattárak alapos áttekintéséhez tekintse meg [cikkünket a megfelelő adattár kiválasztásáról.](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview)

## <a name="high-throughput"></a>Magas teljesítmény

Az egyik legnyilvánvalóbb kihívás, amikor fenntartja a relációs adatbázis-rendszer, hogy a legtöbb relációs motorok alkalmazni zárak és zárak érvényesítésére szigorú [ACID szemantika](https://en.wikipedia.org/wiki/ACID). Ez a megközelítés előnyökkel jár az adatbázison belüli konzisztens adatállapot biztosítása szempontjából. Azonban vannak nehéz kompromisszumok tekintetében egyidejűség, késés és a rendelkezésre állás tekintetében. Ezek miatt az alapvető architekturális korlátozások miatt a nagy tranzakciós kötetek manuálisan shard adatokat eredményezhet. Végrehajtási kézi szilánkok lehet időigényes és fájdalmas gyakorlat.

Ezekben a forgatókönyvekben [az elosztott adatbázisok](https://en.wikipedia.org/wiki/Distributed_database) skálázhatóbb megoldást kínálnak. Azonban a karbantartás továbbra is költséges és időigényes feladat lehet. Előfordulhat, hogy a rendszergazdáknak további munkát kell végezniük annak érdekében, hogy a rendszer elosztott jellege átlátható legyen. Előfordulhat, hogy figyelembe kell venniük az adatbázis "leválasztott" jellegét is.

[Az Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) leegyszerűsíti ezeket a kihívásokat azáltal, hogy világszerte üzembe helyezi az összes Azure-régióban. A partíciótartományok dinamikusan oszthatók fel, hogy zökkenőmentesen növeljék az adatbázist az alkalmazással összhangban, miközben fenntartják a magas rendelkezésre állást. A részletes többbérleti és szigorúan ellenőrzött, natív erőforrások kalkulációja [elképesztő késleltetési garanciákat](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency) és kiszámítható teljesítményt nyújt. A particionálás teljes mértékben felügyelt, így a rendszergazdáknak nem kell kódot írniuk vagy partíciókat kezelniük.

Ha a tranzakciós kötetek elérik a szélsőséges szintet, például több ezer tranzakció másodpercenként, érdemes egy elosztott NoSQL-adatbázis. Fontolja meg az Azure Cosmos DB maximális hatékonyság, a könnyű karbantartás, és a csökkentett teljes tulajdonlási költség.

![Háttér](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>Hierarchikus adatok

Jelentős számú olyan használati eset van, amelyben az adatbázis tranzakciói sok szülő-gyermek kapcsolatot tartalmazhatnak. Ezek a kapcsolatok is jelentősen növekszik az idő múlásával, és nehéz kezelni. A [hierarchikus adatbázisok](https://en.wikipedia.org/wiki/Hierarchical_database_model) formái az 1980-as években jelentek meg, de a tárolás hatékonysága miatt nem voltak népszerűek. Ők is elvesztették vontatási Ted [Codd relációs modell](https://en.wikipedia.org/wiki/Relational_model) lett a de facto szabvány által használt szinte minden mainstream adatbázis-kezelő rendszerek.

Ma azonban a dokumentumszerű adatbázisok népszerűsége jelentősen megnőtt. Ezek az adatbázisok a hierarchikus adatbázis-paradigma újragondolásának tekinthetők, amelyet most nem gátlagásolnak a lemezen tárolt adatok kalkulálásának költségei. Ennek eredményeképpen a relációs adatbázisban számos összetett szülő-gyermek entitás kapcsolat fenntartása most már a modern dokumentumorientált megközelítésekhez képest anti-mintának tekinthető.

Az [objektumorientált tervezés](https://en.wikipedia.org/wiki/Object-oriented_design)megjelenése és a relációs modellekkel való kombináláskor felmerülő [impedancia-eltérés](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) szintén kiemeli a relációs adatbázisok anti-mintázatát bizonyos használati esetekben. Ennek eredményeképpen rejtett, de gyakran jelentős karbantartási költségek merülhetnek fel. Bár [az ORM megközelítések](https://en.wikipedia.org/wiki/Object-relational_mapping) fejlődtek, hogy részben enyhítse ezt, a dokumentum-orientált adatbázisok mégis sokkal jobban egyesülnek az objektumorientált megközelítésekkel. Ezzel a megközelítéssel a fejlesztők nem kényszerülnek arra, hogy elkötelezettek legyenek az ORM-illesztőprogramok, illetve a testre szabott nyelvspecifikus [OO-adatbázis-motorok számára.](https://en.wikipedia.org/wiki/Object_database) Ha az adatok sok szülő-gyermek kapcsolatot és mély hierarchiaszintet tartalmaznak, érdemes lehet egy NoSQL-dokumentum-adatbázist, például az [Azure Cosmos DB SQL API-t](https://docs.microsoft.com/azure/cosmos-db/introduction)használni.

![OrderDetails (Rendelésrészletei)](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>Összetett hálózatok és kapcsolatok

Ironikus módon, mivel a nevüket, relációs adatbázisok jelentenek kevésbé optimális megoldást modellezése mély és összetett kapcsolatok. Ennek az az oka, hogy az entitások közötti kapcsolatok valójában nem léteznek relációs adatbázisban. Ezeket futásidőben kell kiszámítani, és a descartes-i illesztéseket igénylő összetett kapcsolatokat a lekérdezések használatával történő leképezés engedélyezéséhez. Ennek eredményeképpen a műveletek exponenciálisan drágábbak lesznek a kapcsolatok növekedésével a számítások szempontjából. Bizonyos esetekben az ilyen entitások kezelésére próbáló relációs adatbázis használhatatlanná válik.

A "Network" adatbázisok különböző formái jelentek meg a relációs adatbázisok megjelenésekor, de mint a hierarchikus adatbázisok, ezek a rendszerek is egyre népszerűbbek. Lassú elfogadása volt köszönhető, hogy a használati esetek hiánya abban az időben, és a tárolási hiányosságok. Ma, graph adatbázis-motorok lehet tekinteni újbóli megjelenése a hálózati adatbázis paradigma. Ezeknek a rendszereknek a legfontosabb előnye, hogy a kapcsolatokat "első osztályú állampolgárokként" tárolják az adatbázisban. Így, áthaladó kapcsolatok lehet tenni állandó időben, ahelyett, hogy növeli az idő összetettségét minden új illesztés vagy kereszt termék.

Ha az adatbázisban kapcsolatok összetett hálózatát tartja fenn, érdemes megfontolnia egy gráf-adatbázist, például az [Azure Cosmos DB Gremlin API-t](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) az adatok kezeléséhez.

![Graph](./media/relational-or-nosql/graph.png)

Az Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely api-vetületet kínál az összes főbb NoSQL modelltípushoz; Oszlopcsalád, Dokumentum, Grafikon és Kulcsérték. A [Gremlin (graph)](https://docs.microsoft.com/azure/cosmos-db/gremlin-support) és az SQL (Core) Document API-rétegek teljes mértékben interoperábilisak. Ez előnyökkel jár a különböző modellek közötti váltáshoz a programozhatóság szintjén. A gráftárolók lekérdezhetők mind az összetett hálózati bejárások, mind az ugyanabban a tárolóban lévő dokumentumrekordokként modellezett tranzakciók tekintetében.

## <a name="fluid-schema"></a>Folyadékséma

A relációs adatbázisok egy másik különleges jellemzője, hogy a sémákat a tervezés időpontjában kell meghatározni. Ennek előnye van a hivatkozási integritás és az adatok megfelelősége szempontjából. Azonban az alkalmazás növekedésével korlátozó is lehet. A séma változásaira adott válasz az azonos táblával vagy adatbázis-definícióval megegyező, logikailag különálló modellek között idővel bonyolulttá válhat. Az ilyen használati esetek gyakran részesülnek abból, hogy a séma az alkalmazásra van átruházva, és rekordonként kezeli. Ehhez az adatbázisnak "sémafüggetlennek" kell lennie, és lehetővé kell tennie, hogy a rekordok "önleírják" a bennük lévő adatokat.

Ha olyan adatokat kezel, amelyek struktúrái folyamatosan nagy mértékben változnak, különösen akkor, ha a tranzakciók külső forrásokból származhatnak, ahol nehéz a megfelelőség kényszerítése az adatbázisban, érdemes megfontolni egy séma-agnosztikusabb megközelítést. felügyelt NoSQL adatbázis-szolgáltatás, például az Azure Cosmos DB használatával.

## <a name="microservices"></a>Mikroszolgáltatások

A [mikroszolgáltatások](https://en.wikipedia.org/wiki/Microservices) mintája jelentősen nőtt az elmúlt években. Ez a minta a [szolgáltatásorientált architektúrában gyökerezik.](https://en.wikipedia.org/wiki/Service-oriented_architecture) Ezekben a modern mikroszolgáltatási architektúrákban az adatátvitel de facto szabványa a [JSON,](https://en.wikipedia.org/wiki/JSON)amely történetesen a dokumentumorientált NoSQL-adatbázisok túlnyomó többségének tárolóeszköze is. Ez sokkal zökkenőmentesebbé teszi a NoSQL-dokumentum tárolóit mind a perzisztencia, mind a szinkronizálás hoz [(eseményforrás-minták](https://en.wikipedia.org/wiki/Event-driven_architecture)használatával) az összetett mikroszolgáltatás-implementációk között. A hagyományosrelációs adatbázisok fenntartása sokkal összetettebb lehet ezekben az architektúrákban. Ez annak köszönhető, hogy az állapotés az API-k közötti szinkronizálás nagyobb mértékű átalakításra van szükség. Az Azure Cosmos DB különösen számos olyan funkcióval rendelkezik, amelyek még gördülékenyebbé teszik a JSON-alapú mikroszolgáltatások architektúráihoz, mint sok NoSQL-adatbázis:

* tiszta JSON adattípusok választéka
* az adatbázisba beépített JavaScript motor és [lekérdezési API.](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api)
* egy korszerű [változási hírcsatorna,](https://docs.microsoft.com/azure/cosmos-db/change-feed) amelyre az ügyfelek előfizethetnek, hogy értesítést kapjanak egy tároló módosításairól.

## <a name="some-challenges-with-nosql-databases"></a>Néhány kihívás a NoSQL adatbázisokkal

Bár a NoSQL adatbázisok megvalósítása során vannak egyértelmű előnyei, vannak olyan kihívások is, amelyeket érdemes figyelembe venni. Ezek nem lehetnek ugyanolyan mértékben jelen, amikor a relációs modellel dolgoznak:

* több kapcsolattal rendelkező tranzakciók, amelyek ugyanarra az entitásra mutatnak.
* a teljes adatkészlet enkonzisztenciáját igénylő tranzakciók.

Az első kihívást tekintve a NoSQL-adatbázisokban a hüvelykujj szabálya általában denormalizáció, amely a korábbi megfogalmazásszerint hatékonyabb olvasásokat eredményez egy elosztott rendszerben. Vannak azonban olyan tervezési kihívások, amelyek ezzel a megközelítéssel szerepet játszanak. Vegyünk egy példát egy olyan termékre, amely egy kategóriához és több címkéhez kapcsolódik:

![Illesztések](./media/relational-or-nosql/many-joins.png)

A NoSQL dokumentum-adatbázisban az ajánlott eljárás az lenne, ha a kategórianevét és a címkeneveket közvetlenül egy "termékdokumentumban" denormalizálná. A kategóriák, címkék és termékek szinkronizálása érdekében azonban az ezt megkönnyítő tervezési lehetőségek növelték a karbantartás összetettségét, mivel az adatok a termék több rekordjában is megkettőződnek, ahelyett, hogy egy egyszerű frissítés lenne egy "egy-a-többhöz" kapcsolatot, és egy illesztést az adatok beolvasásához. 

A kompromisszum az, hogy az olvasások hatékonyabbak a denormalizált rekordban, és egyre hatékonyabbá válnak a fogalmilag összekapcsolt entitások számának növekedésével. Azonban, ahogy az olvasási hatékonyság növekszik a növekvő számú összekapcsolt entitások egy denormalize rekord, így is a karbantartási összetettségét entitások szinkronban tartása. Ennek a kompromisszumnak az egyik módja egy [hibrid adatmodell](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models)létrehozása.

Bár a NoSQL adatbázisokban nagyobb rugalmasság áll rendelkezésre a kompromisszumok kezeléséhez, a nagyobb rugalmasság több tervezési döntést is hozhat. Tekintse meg cikkünket, [hogyan modellezi és particionálja az adatokat az Azure Cosmos DB egy valós példa segítségével,](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)amely magában foglalja a megközelítést a [denormalizált felhasználói adatok szinkronban,](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames) ahol a felhasználók nem csak ülni a különböző partíciókat, de a különböző tárolókban.

Az erős konzisztencia tekintetében ritkán fordul elő, hogy erre a teljes adatkészletben szükség lesz. Azokban az esetekben azonban, ahol ez szükséges, kihívást jelent az elosztott adatbázisokban. Az erős konzisztencia érdekében az adatokat szinkronizálni kell az összes replikák és régiók között, mielőtt az ügyfelek olvasni. Ez növelheti az olvasások késését.

Az Azure Cosmos DB ismét nagyobb rugalmasságot biztosít, mint a relációs adatbázisok a különböző kompromisszumok, amelyek relevánsak itt, de a kis léptékű megvalósítások, ez a megközelítés további tervezési szempontokat is hozzáadhat. Ebben a témában további részletekért tekintse meg a [konzisztenciáról, a rendelkezésre állásról és a teljesítményről szóló cikkünket.](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan kezelheti az Azure Cosmos-fiókot és egyéb fogalmakat:

* [Az Azure Cosmos-fiók kezelése](how-to-manage-database-account.md)
* [Globális terjesztés](distribute-data-globally.md)
* [Konzisztenciaszintek](consistency-levels.md)
* [Az Azure Cosmos-tárolók és -elemek közös munkálatai](databases-containers-items.md)
* [VNET-szolgáltatásvégpont az Azure Cosmos-fiókhoz](vnet-service-endpoint.md)
* [IP-tűzfal az Azure Cosmos-fiókhoz](firewall-support.md)
* [Azure-régiók hozzáadása és eltávolítása az Azure Cosmos-fiókhoz](how-to-manage-database-account.md)
* [Az Azure Cosmos DB SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
