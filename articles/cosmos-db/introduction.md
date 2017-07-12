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
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 49eb2e4f7d57de44a3b7a877dfdd138f4c374436
ms.contentlocale: hu-hu
ms.lasthandoff: 06/28/2017

---

<a id="welcome-to-azure-cosmos-db" class="xliff"></a>

# Üdvözli az Azure Cosmos DB

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázisa. Az Azure Cosmos DB segítségével egyetlen gombnyomással rugalmasan és függetlenül méretezhető az átviteli sebesség és a tárterület, akár több földrajzi Azure-régióra kiterjedően is. A rendszer az átviteli sebességre, a késére, a rendelkezésre állásra és a konzisztenciára vonatkozó garanciákat biztosít átfogó [szolgáltatói szerződésekkel](https://aka.ms/acdbsla) (SLA). Ilyet egyetlen másik adatbázis-szolgáltatás sem kínál.

![Az Azure Cosmos DB a Microsoft globálisan elosztott adatbázis-szolgáltatása rugalmas horizontális felskálázási képességgel, garantáltan alacsony késéssel, öt konzisztenciamodellel, valamint átfogó garantált SLA-kkal.](./media/introduction/azure-cosmos-db.png)

Az Azure Cosmos DB egy írásra optimalizált, erőforrás-vezérelt, sémafüggetlen adatbázismotort tartalmaz, amely több adatmodellt is natívan támogat: kulcs-érték, dokumentum, diagram vagy oszlopos. Több API-t is kibővíthető módon támogat az adatok eléréséhez, többek között a [MongoDB](mongodb-introduction.md), a [DocumentDB SQL](documentdb-introduction.md), a [Gremlin](graph-introduction.md) (előzetes verzió) és az [Azure-táblák](table-introduction.md) (előzetes verzió) modellt. 

Az Azure Cosmos DB 2010 második felétől vált elérhetővé, és a fejlesztők számára olyan problémás kérdésekre igyekezett választ nyújtani, amelyekkel a Microsoft berkein belül a nagy méretű alkalmazások szembesülnek. Mivel a globálisan elosztott alkalmazások készítésének problémája nem csupán a Microsoftnál jelentkezik, a szolgáltatást vállalaton kívül is elérhetővé tettük az összes Azure-fejlesztő számára az Azure DocumentDB formájában. Az Azure Cosmos DB a következő nagy ugrás a DocumentDB evolúciójában, és most ezt is elérhetővé tesszük felhasználóink számára. Az Azure Cosmos DB jelen kiadásának keretében a DocumentDB-ügyfelek (adataikkal egyetemben) automatikusan Azure Cosmos DB-ügyfelekké válnak. Az átállás zökkenőmentes, és így az ügyfelek mostantól az Azure Cosmos DB által nyújtott új képességek szélesebb palettájához férnek hozzá. 

<a id="capability-comparison" class="xliff"></a>

## Képességek összehasonlítása

Az Azure Cosmos DB a relációs és nem relációs adatbázisok legjobb képességeit nyújtja.

| Funkciók | Relációs adatbázisok | Nem relációs (NoSQL) adatbázisok |  Azure Cosmos DB |
| --- | --- | --- | --- |
| Globális terjesztés | x | x | ✓ Kulcsrakész, 30+ régió, többkiszolgálós |
| Horizontális skálázhatóság | x | ✓ | ✓ Tárolás és átviteli sebesség független skálázhatósága | 
| Késési garancia | x | ✓ | ✓ <10 ms olvasásra, <15 ms írásra, az esetek 99%-ában | 
| Magas rendelkezésre állás | x | ✓ | ✓ Mindig elérhető, PACELC kompromisszumok, automatikus és kézi feladatátvétel |
| Adatmodell és API | Relációs és SQL | Többmodelles és OSS API | Többmodelles és SQL, valamint OSS API (továbbiak hamarosan elérhetőek) |
| SLA-k | ✓ | x | ✓ Átfogó SLA-k késéshez, átviteli sebességhez, konzisztenciához, rendelkezésre álláshoz |

<a id="key-capabilities" class="xliff"></a>

## Főbb képességek
Globálisan elosztott adatbázis-szolgáltatásként az Azure Cosmos DB az alábbi képességekkel segíti elő, hogy skálázható, globálisan elosztott, gyors válaszidejű alkalmazásokat építhessen:

* [**Kulcsrakész globális terjesztés**](#global-distribution)
    * Alkalmazása mindenhol azonnal elérhető felhasználói számára. És ez most már az adataira is igaz lehet.
    * Nem kell aggódnia a hardverek, a csomópontok hozzáadása, a virtuális gépek vagy a magok miatt. Egy kattintással elérhetővé teheti az adatait. 

* [**Több adatmodell és népszerű API az adatok eléréséhez és lekérdezéséhez**](#data-models)
    * Több adatmodell támogatása, többek között: kulcs-érték, dokumentum, diagram és oszlop.
    * Bővíthető API-k a következőkhöz: Node.js, Java, .NET, .NET Core, Python és MongoDB.
    * SQL és Gremlin a lekérdezésekhez. 

* [**Igény szerinti rugalmas átviteli sebesség és tárterület, világszerte**](#horizontal-scale)
    * Az átviteli sebességet könnyedén méretezheti [másodpercalapú](request-units.md) vagy [percalapú](https://aka.ms/acdbrupm) granularitással, és igény szerint bármikor megváltoztathatja. 
    * A méretigények mindenkori kielégítéséhez a tárterületet [átláthatóan és automatikusan](partition-data.md) méretezheti.

* [**Gyors válaszidejű és alapvető fontosságú alkalmazásokat hozhat létre**](#low-latency) 
    * Az adatait az esetek 99 százalékában csupán ezredmásodpercnyi késéssel érheti el, bárhol a világon. 

* [**Always On rendelkezésre állás**](#high-availability)
    * 99,99%-os rendelkezésre állás egy adott régión belül.
    * Bármennyi [Azure-régiót](https://azure.microsoft.com/regions) üzembe helyezhet a magasabb rendelkezésre állás érdekében.
    * [Hibaszimuláció](regional-failover.md) egy vagy több régióban, adatvesztés elleni garanciával. 

* [**Globálisan terjesztett alkalmazások írása, a helyes módon**](#consistency)
    * [Az öt konzisztenciamodell](consistency-levels.md) az SQL által nyújtottakhoz hasonló konzisztenciától a NoSQL-hez hasonló végleges konzisztenciáig minden igényt képes lefedni. 
  
* [**Pénzvisszafizetési garancia**](#sla) 
    * Adatai gyorsan célba érnek, vagy visszafizetjük a pénzét. 
    * [Szolgáltatói szerződések](https://aka.ms/acdbsla) a rendelkezésre állásról, a késésről, átviteli sebességről és konzisztenciáról. 

* [**Nincs adatbázisséma vagy indexkezelés**](#schema-free)
    * Nem kell többé aggódnia amiatt, hogy adatbázissémája és az indexei szinkronban vannak-e az alkalmazása által használt sémával. Nálunk nincsenek sémák. 

* [**Alacsony tulajdonosi költségek**](#tco)
    * Öt-tízszer [költséghatékonyabb](https://aka.ms/documentdb-tco-paper), mint egy nem felügyelt megoldás.
    * Harmadannyiba kerül, mint a DynamoDB.

<a id="global-distribution"></a>

<a id="global-distribution" class="xliff"></a>

## Globális terjesztés
Az Azure Cosmos DB-tárolókat két szinten osztjuk el: 

1. Egy adott régión belül minden erőforrás vízszintesen, erőforrás-partíciók használatával van particionálva (helyi elosztás). 
2. Minden erőforrás-partícióból több földrajzi régióban készül másolat (globális elosztás). 

![Az ábra az Azure Cosmos DB globális elosztását ábrázolja](./media/introduction/azure-cosmos-db-global-distribution.png) 

Ha a tárterületet és az átviteli sebességet méretezni kell, a Cosmos DB átláthatóan elvégzi a partíciókezelési műveleteket minden régióban. A méretezéstől, a terjesztéstől, vagy a hibáktól függetlenül a Cosmos DB folyamatosan egyetlen rendszerképet biztosít a globálisan elérhető erőforrásokból. 

Az erőforrások globális elosztása mindig [kulcsrakész](distribute-data-globally.md) a Cosmos DB rendszerében. Néhány kattintással (vagy egyetlen API-hívással) bármikor társíthat bármennyi földrajzi régiót az adatbázisfiókjához. 

Az adatok mennyiségétől vagy a régiók számától függetlenül a Cosmos DB garantálja, hogy minden újonnan társított régió az esetek 99%-ában egy órán belül elkezdi feldolgozni az ügyfélkéréseket. Ez úgy érhető el, hogy a rendszer az összes forráserőforrás-partíció az újonnan társított régióba irányuló adat-összehangolását és adatmásolását párhuzamosan végzi. Az ügyfelek eltávolíthatnak egy létező régiót, vagy felvehetnek egy korábban az adatbázisfiókjukkal offline módon társított régiót.

<a id="data-models"></a>
<a id="multi-model-multi-api-support" class="xliff"></a>

## Több modell és több API támogatása
 Az Azure Cosmos DB natív módon több adatmodellt is támogat, beleértve a dokumentum, a kulcs-érték, a diagram és az oszlop típusúakat. A Cosmos DB adatbázismotorjának központi tartalommodellje az atom-rekord-szekvencia (ARS) modellen alapul. Az atomok olyan egyszerű típusok apró készleteiből állnak, mint a karakterlánc-, a logikai vagy a számérték. A rekordok ezekből a típusokból létrehozott struktúrák. A szekvenciák olyan tömbök, amelyek atomokból, rekordokból vagy szekvenciákból állnak. 
 
 Az adatbázismotor a különböző adatmodellek az ARS-alapú adatmodellre történő hatékony fordítására és kivetítésére is képes. A Cosmos DB központi adatmodellje natív módon elérhető dinamikusan gépelt programozási nyelvekből, valamint módosítás nélküli állapotában elérhetővé tehető JSON-ként is. 
 
 A szolgáltatás ezenkívül számos népszerű adatbázis-API-t támogat adatelérés és adatlekérdezés céljából. A Cosmos DB adatbázismotorja jelenleg a következőket támogatja: [DocumentDB SQL](documentdb-introduction.md), [MongoDB](mongodb-introduction.md), [Azure-táblák](table-introduction.md) (előzetes verzió) és [Gremlin](graph-introduction.md) (előzetes verzió). Továbbra is építhet alkalmazásokat népszerű OSS API-k használatával, és kihasználhatja az élesben tesztelt és teljes körűen felügyelt, globálisan elosztott adatbázis-szolgáltatás minden előnyét. 

<a id="horizontal-scale"></a>
<a id="horizontal-scaling-of-storage-and-throughput" class="xliff"></a>

## Tárterület és átviteli sebesség vízszintes méretezése
A Cosmos DB-tárolókban található minden adat (például egy dokumentumgyűjtemény, táblázat vagy diagram) vízszintesen particionált, és az erőforrás-partíciók átlátható felügyelete alá tartozik. Az erőforrás-partíció egy konzisztens és nagy rendelkezésre állású adattároló, melyet egy [ügyfélre szabott partíciós kulccsal particionálnak](partition-data.md). Egyetlen rendszerképet biztosít az általa kezelt erőforráskészlethez, és alapvető részét képezi a méretezhetőségnek és az elosztásnak. A Cosmos DB-t úgy tervezték, hogy rugalmasan méretezhesse az átviteli sebességet az alkalmazás különböző földrajzi régiókból nyert forgalmi mintázatai alapján. Ezzel megoldást nyújthat az ingadozó számítási feladatokra, amelyek földrajzi helytől és időtől függetlenül változhatnak. A szolgáltatás átláthatóan kezeli a partíciókat a Cosmos DB-tároló rendelkezésre állásának, konzisztenciájának, késésének vagy átviteli sebességének akadályozása nélkül.  
 
![Az Azure Cosmos DB vízszintesen méretezhető](./media/introduction/azure-cosmos-db-partitioning.png) 

Rugalmasan méretezheti egy Azure Cosmos DB-tároló átviteli teljesítményét, ha programozott módon osztja ki az átviteli sebességet, [másodpercenkénti kérelemegységek (RU/s)](request-units.md) használatával. A szolgáltatás belsőleg átláthatóan kezeli az erőforrás-partíciókat, hogy elérhetővé tegye az átviteli sebességet egy adott tárolón. A Cosmos DB gondoskodik róla, hogy az átviteli sebesség használatra kész legyen minden olyan régióban, amely hozzá van társítva a tárolóhoz. Az új átviteli sebesség a konfigurált érték változásától számított öt másodpercen belül elérhetővé válik. 

Mindkét módon, másodpercalapú és [percalapú (RU/m)](request-units-per-minute.md) granularitással is kioszthatja az átviteli sebességet egy Cosmos DB-tárolón. A percalapú granularitással kiosztott átviteli sebességet a számítási feladatban történő váratlan, másodpercalapú granularitású kiugrások kezelésére használja a rendszer. 

<a id="low-latency"></a>
<a id="low-latency-guarantees-at-the-99th-percentile" class="xliff"></a>

## Az esetek 99%-át lefedő alacsony késési garancia
Az SLA-k részeként a Cosmos DB az esetek 99%-ában alacsony végpontok közötti késést tud biztosítani az ügyfeleinek. Egy átlagos 1 KB-os elem esetében a Cosmos DB – ugyanabban az Azure-régióban – az esetek 99%-ában garantálja az olvasások 10 ezredmásodperc alatti, illetve az indexelt írások 15 ezredmásodperc alatti végpontok közötti késését. A késések átlagértékei lényegesen alacsonyabbak ennél (5 ezredmásodperc alattiak).  Mivel minden adatbázis-tranzakciónak van egy felső kérelemfeldolgozási határértéke, a Cosmos DB lehetővé teszi az ügyfeleknek, hogy egyértelműen megkülönböztethessék a nagy mértékű késéssel bíró tranzakciókat és azt, ha elérhetetlen az adatbázis.

<a id="high-availability"></a>
<a id="transparent-multi-homing-and-9999-high-availability" class="xliff"></a>

## Átlátható többkiszolgálós működés és 99,99%-os rendelkezésre állás
Dinamikusan társíthat „prioritásokat” az Azure Cosmos DB adatbázis-fiókjához társított régiókhoz. A prioritásokkal a megadott régiókhoz irányíthatók a kérések, ha regionális hiba történik. Abban a nem túl valószínű esetben, ha regionális katasztrófa történik, a Cosmos DB automatikusan feladatátvételt hajt végre, a prioritási sorrendet alapul véve.

Az alkalmazás végpontok közötti rendelkezésre állásának teszteléséhez [manuálisan kiválthat egy feladatátvételt](regional-failover.md) (egy órán belül két ilyen művelet engedélyezett). A Cosmos DB garantálja, hogy semmilyen adatvesztés nem történik a manuálisan aktivált regionális feladatátvétel közben. Ha regionális katasztrófa következik be, a rendszer által kezdeményezett automatikus feladatátvétel során a Cosmos DB egy adatvesztési felső határértéket garantál. Egy regionális feladatátvétel után nem kell ismét üzembe helyeznie alkalmazását, és az Azure Cosmos DB betartja a rendelkezésre állási SLA-kat. 

Egy ilyen forgatókönyv esetén a Cosmos DB lehetővé teszi, hogy logikai (régiófüggetlen) vagy fizikai (régióspecifikus) végpontok segítségével lépjen kapcsolatba az erőforrásokkal. Az előbbi révén az alkalmazást átlátható módon lehet több helyről is elérhetővé tenni feladatátvétel esetén. Az utóbbival precízen szabályozhatja az alkalmazás az olvasások és írások adott régiókba történő továbbítását. A Cosmos DB 99,99%-os rendelkezésre állási SLA-t garantál minden adatbázisfiókhoz. A rendelkezésre állási garanciák függetlenek a méretezéstől (kiosztott átviteli sebességtől és tárterülettől), a régiók számától, vagy az egy adott adatbázishoz társított régiók közötti földrajzi távolságtól. 

<a id="consistency"></a>
<a id="multiple-well-defined-consistency-models" class="xliff"></a>

## Több jól definiált konzisztenciamodell
A kereskedelmi forgalomban elérhető adatbázisok két kategóriába sorolhatók: az egyik egyáltalán nem kínál jól definiált és bizonyítható konzisztencialehetőségeket, a másik pedig két szélsőséges programozási lehetőséget nyújt (erős vagy végleges konzisztencia). Az előbbi replikációs protokolljainak részletessége komoly fejfájást okozhat az alkalmazásfejlesztőknek, ráadásul rajtuk múlik az egyensúlyi állapot megteremtése a konzisztencia, a rendelkezésre állás, a késés és az átviteli sebesség között. Az utóbbinál pedig komoly terhet jelent a választás a két véglet közül. A kutatási adatok bősége és a több mint 50 konzisztenciamodellre vonatkozó javaslatok ellenére az elosztott adatbázisok közössége eleddig nem volt képes az erős és végleges konzisztencián túl szabványosítani a konzisztenciaszinteket. 

A Cosmos DB választási lehetőséget kínál: [öt jól definiált konzisztenciamodellt](consistency-levels.md), amely lefedi a teljes konzisztenciaspektrumot – erős, kötött elavulás, [munkamenet](http://dl.acm.org/citation.cfm?id=383631), konzisztens előtag és végleges. 

![Az Azure Cosmos DB több jól definiált (enyhén korlátozott) konzisztenciamodellt is elérhetővé tesz](media/introduction/azure-cosmos-db-consistency-levels.png)

Az alábbi táblázat azt illusztrálja, hogy melyik konzisztenciaszint milyen garanciákat biztosít.
 
**Konzisztenciaszintek és garanciák**

| Konzisztenciaszint | Garanciák |
| --- | --- |
| Erős | Linearizálhatóság |
| Kötött elavulás | Konzisztens előtag. Az olvasások k verzióval vagy t időközzel követik az írásokat |
| Munkamenet   | Konzisztens előtag. Monoton olvasások, monoton írások, írás utáni visszaolvasás, beolvasás utáni írás |
| Konzisztens előtag | A visszaadott frissítések között az összes frissítés néhány egymást követő verziója szerepel, mindig megfelelő sorrendben |
| Végleges  | Nem sorrendben történő olvasások |

Konfigurálhatja az alapértelmezett konzisztenciaszintet Cosmos DB-fiókjában (és később felülbírálhatja a konzisztenciát egy adott olvasási kérésen). Belsőleg az alapértelmezett konzisztenciaszint a partíciókészleteken belüli azon adatokra vonatkozik, amelyek több régión is átnyúlhatnak. 


<a id="sla"></a>
<a id="guaranteed-service-level-agreements" class="xliff"></a>

## Szolgáltatói szerződésből fakadó garanciák

A Cosmos DB az első felügyelt adatbázis-szolgáltatás, amely 99,99%-os [SLA-garanciát](https://aka.ms/acdbsla) kínál a rendelkezésre állásra, az átviteli sebességre, az alacsony késésre és a konzisztenciára.
* Rendelkezésre állás: 99,99%-os rendelkezésre állási SLA minden adat- és vezérlősík-műveletnél.
* Átviteli sebesség: a kérések 99,99%-a sikeresen teljesül 
* Késés: az esetek 99%-ában a <10 ms késések aránya 99,99%-os.
* Konzisztencia: az olvasási kérések 100%-a megfelel az Ön által kért konzisztenciaszintnek megfelelő konzisztenciagaranciának.


<a id="schema-free"></a>
<a id="schema-free" class="xliff"></a>

## Sémamentesség

A relációs és a NoSQL-adatbázisokban kénytelen a séma- és indexkezeléssel, verziószámozással és migrálással vesződni. Ezek komoly kihívásokat jelenthetnek egy globálisan elosztott környezetben. Ne aggódjon, a Cosmos DB megszabadítja ettől a problémától! A Cosmos DB-vel nem kell sémákat és indexeket kezelnie, bajlódnia a séma verziószámozásával, vagy a sémák áttelepítésekor az alkalmazás működésének leállása miatt aggódnia. A Cosmos DB adatbázismotorja teljesen sémafüggetlen. Automatikusan indexel minden fogadott adatot bármiféle séma vagy index nélkül, és villámgyors lekérdezéseket kínál. 

<a id="tco"></a>
<a id="low-cost-of-ownership" class="xliff"></a>

## Alacsony tulajdonosi költségek

 Ha minden tulajdonosi költséget (TCO) figyelembe vesz, az olyan felügyelt felhőszolgáltatások, mint az Azure Cosmos DB, öt-tízszer költséghatékonyabbak lehetnek, mint a helyszíni vagy virtuális gépen futó OSS-megfelelőik. Ezenkívül a számítási feladatok nagy mennyisége esetén az Azure Cosmos DB a DynamoDB árának felébe, vagy akár harmadába kerül. További információkat a [TCO-tanulmányban](https://aka.ms/documentdb-tco-paper) találhat. 

<a id="next-steps" class="xliff"></a>

## Következő lépések
Az alábbi rövid útmutatókkal könnyedén elkezdheti az Azure Cosmos DB használatát:

* [Bevezetés az Azure Cosmos DB DocumentDB API-jának használatába](create-documentdb-dotnet.md)
* [Bevezetés az Azure Cosmos DB MongoDB API-jának használatába](create-mongodb-nodejs.md)
* [Bevezetés az Azure Cosmos DB Graph API-jának használatába](create-graph-dotnet.md)
* [Bevezetés az Azure Cosmos DB Table API-jának használatába](create-table-dotnet.md)

