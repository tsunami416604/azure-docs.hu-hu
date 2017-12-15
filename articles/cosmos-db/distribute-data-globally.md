---
title: "Globális adatok Azure Cosmos DB terjesztése |} Microsoft Docs"
description: "Ismerje meg a globális adatbázisokat az Azure Cosmos Adatbázisból, egy globálisan elosztott, mutli-modell dokumentumadatbázis-szolgáltatás segítségével bolygónk méretű georeplikáció, a feladatátvételt és az adatok helyreállítás."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: arramac
ms.openlocfilehash: 0be81802996f27a4c063e4e728a3c95ad757bea0
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Globális adatok Azure Cosmos DB terjesztése
Azure a széles körű – folyamatosan bővülő, és egy globális erőforrásigényét tért 30 + földrajzi régiók között. A globális jelenlét, az Azure felajánlja, hogy a fejlesztők differenciált képességeit egyik létre, telepíthetnek és könnyen globálisan elosztott alkalmazások kezelésére képes. 

Az [Azure Cosmos DB](../cosmos-db/introduction.md) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása az alapvető fontosságú alkalmazásokhoz. Azure Cosmos DB biztosít kulcsrakész globális terjesztési, [átviteli sebesség és tárterület a rugalmas méretezést](../cosmos-db/partition-data.md) világszerte, egyjegyű ezredmásodperces késések fordulnak elő, a 99th PERCENTILIS [öt jól meghatározott konzisztenciaszintek](consistency-levels.md), és magas rendelkezésre állás érdekében minden biztonsági mentés által garantált [iparágvezető SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Az Azure Cosmos DB [automatikusan indexeli az adatokat](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) anélkül, hogy a felhasználónak sémákat és indexeket kellene kezelnie. Többmodelles szolgáltatás, amely támogatja a dokumentumokat, a kulcs-értékeket, a diagramokat és az oszlopos adatmodelleket. A felhő született szolgáltatásként Azure Cosmos DB van gondosan fejthetők vissza a több-bérlős és a globális terjesztési egészen az alapoktól fel.

**Egyetlen Azure Cosmos DB gyűjtemény particionálta és elosztva a több Azure-régiók**

![Az Azure Cosmos DB gyűjtemény particionálta és elosztva három régiók](./media/distribute-data-globally/global-apps.png)

Ahogy rendelkezik Azure Cosmos DB felépítésekor, globális terjesztési nem lehet utólag – nem lehet "menetes-on" egy "egyszeri" adatbázis helyrendszer elveire. A globálisan elosztott adatbázis által biztosított képességeket span felett marad, hogy a hagyományos földrajzi katasztrófa földrajzi--helyreállítási által kínált "egyhelyes" adatbázisok. Egyetlen hely adatbázisok ajánlat földrajzi-vész-Helyreállítási képességet globálisan elosztott adatbázisok szigorú részhalmazai. 

Azure Cosmos DB kulcsrakész globális terjesztési, a fejlesztőknek nem kell hozhat létre a saját replikációs állványok alkalmazó vagy a Lambda mintát (például [AWS DynamoDB replikációs](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) az adatbázis naplója vagy különféle régiókban "dupla írások" végrehajtásával. Nem javasoljuk ezen módszerek óta helyességét ilyen szempontok biztosítása, és adja meg a hang SLA-k nem lehetséges. 

Ebben a cikkben nyújtunk Azure Cosmos DB globális terjesztési funkcióiról. Azt is ismertetik Azure Cosmos DB egyedi megközelítése átfogó SLA-kat biztosít. 

## <a id="EnableGlobalDistribution"></a>Kulcsrakész globális terjesztési engedélyezése
Azure Cosmos-adatbázis egyszerű megírásához bolygónk méretű alkalmazások engedélyezéséhez a következő lehetőségeket biztosítja. Ezek a képességek érhetők el az Azure Cosmos DB erőforrás-szolgáltató alapú [REST API-k](https://docs.microsoft.com/rest/api/documentdbresourceprovider/) továbbá az Azure-portálon.

### <a id="RegionalPresence"></a>A széles körű regionális jelenléte 
Azure folyamatosan nő a földrajzi jelenléte hozásával [új régiók](https://azure.microsoft.com/regions/) online. Alapértelmezés szerint minden új Azure régiók Cosmos. Azure-adatbázis nem áll rendelkezésre. Ez lehetővé teszi, hogy rendelje hozzá az Azure Cosmos DB adatbázisfiók egy földrajzi régiót, amint Azure megnyílik az új üzleti régióját.

### <a id="UnlimitedRegionsPerAccount"></a>Az Azure Cosmos DB adatbázisfiók régiók korlátlan számú társítása
Azure Cosmos DB lehetővé teszi tetszőleges számú Azure-régiók társítása Azure Cosmos DB adatbázis fiókját. Geokerítések korlátozások (például a kínai, a németországi) kívül vannak, amely Azure Cosmos DB adatbázis fiókhoz tartozó régiók száma nincs korlátozva. Az alábbi ábrán egy eloszthatja 25 Azure-régiók konfigurált adatbázis-fiók.  

**A bérlő Azure Cosmos-adatbázis adatbázis-fiók feszítőfa 25 Azure-régiók**

![25 Azure-régiók átfedés Azure Cosmos DB adatbázisfiók](./media/distribute-data-globally/spanning-regions.png)

### <a id="PolicyBasedGeoFencing"></a>Csoportházirend-alapú geokerítések
Azure Cosmos DB célja, hogy a csoportházirend-alapú geokerítések képességek állnak a rendelkezésére. Geokerítések adatok irányítási és a megfelelőségi korlátozások biztosításához fontos összetevője, és előfordulhat, hogy egy adott területre társít a fiókját. Példák a geokerítések (többek korlátozni), hatókör a globális terjesztési a régiók szuverén felhő (például a kínai és a németországi), vagy a kormányzati adózás határ (például Ausztrália) belül. A házirendek az Azure-előfizetéséhez, a metaadatok segítségével szabályozza.

### <a id="DynamicallyAddRegions"></a>Dinamikus hozzáadása és eltávolítása a régiók
Azure Cosmos DB lehetővé teszi (társítható) hozzáadása vagy eltávolítása (leválasztani) adatbázis fiókjába a eddigi régiók (lásd: [előző ábra](#UnlimitedRegionsPerAccount)). Címtár adatok replikálása partíció párhuzamosan, Azure Cosmos DB biztosítja, hogy egy új régió online állapotba kerül, ha Azure Cosmos DB elérhető 30 percen belül bárhol a világon a legfeljebb 100 több TB-nyi. 

### <a id="FailoverPriorities"></a>Feladatátvételi prioritások
Regionális feladatátvétel pontos sorrendjét szabályozhatja, hogy mikor van több regionális kimaradás, Azure Cosmos DB lehetővé teszi, hogy társítja a prioritás a különböző régiókban az adatbázishoz tartozó fiók (lásd a következő ábra). Az Azure Cosmos DB biztosítja, hogy az automatikus feladatátvételre kerül sor a megadott prioritási sorrendben. Regionális feladatátvétel kapcsolatos további információkért lásd: [az üzletmenet folytonossága érdekében az Azure Cosmos Adatbázisba automatikus regionális feladatátvétel](regional-failover.md).

**A bérlő Azure Cosmos-adatbázis is konfigurálhat a feladatátvételi prioritási sorrendben (jobb oldali ablaktábla) egy adatbázis-fiókjához társított régiók**

![Feladatátvételi prioritások konfigurálása az Azure Cosmos DB](./media/distribute-data-globally/failover-priorities.png)

### <a id="ConsistencyLevels"></a>Több, jól meghatározott konzisztencia modellek globálisan replikált adatbázisok
Azure Cosmos-adatbázis közzététele [több jól meghatározott konzisztenciaszintek](consistency-levels.md) SLA-k által támogatott. Kiválaszthatja, hogy egy adott konzisztencia modell (a rendelkezésre álló lehetőségek listájában) attól függően, hogy a munkaterhelés forgatókönyveket. 

### <a id="TunableConsistency"></a>Globálisan replikált adatbázisok hangolható konzisztencia
Azure Cosmos-adatbázis programozott és az alapértelmezett konzisztencia választás / kérés alapon futásidőben enyhíteni teszi lehetővé. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Dinamikusan konfigurálható olvasási és írási régiók
Azure Cosmos DB "Olvasás", "write" vagy "olvasási/írási" régiók lehetővé teszi a régiók (az adatbázishoz tartozó) konfigurálását. 

### <a id="ElasticallyScaleThroughput"></a>Azure-régiók közötti rugalmasan méretezési átviteli sebesség
Rugalmasan méretezhető egy Azure Cosmos DB gyűjtemény által létesítési átviteli programozott módon. Az átviteli sebesség terjesztése a gyűjtemény összes régiók vonatkozik.

### <a id="GeoLocalReadsAndWrites"></a>Földrajzi helyi olvasása és írása
Globálisan elosztott adatbázis fő előnye, hogy kis késésű hozzáférést bárhol a világon adataihoz. Azure Cosmos DB kínál a kis késleltetésű garanciák, P99 különböző adatbázis-műveleteknél. Biztosítja, hogy az összes olvasási a legközelebbi helyi olvasási régió legyenek átirányítva. Az olvasási kérelem kiszolgálására, a helyi a régióban, amelyben az olvasás kiadott kvórum szolgál; Ugyanez vonatkozik az írási műveletek. Az írási elfogadott, csak azt követően replikák többsége tartósan véglegesítette a írási helyileg, de éppen által kezdeményezett távoli replikákon lehet visszaigazolni az írási műveletek nélkül. A replikációs protokoll Azure Cosmos-adatbázis alatt azt feltételezi, hogy az olvasási és írási határozatképességére mindig helyi az olvasási és írási, régiók, amelyben a kérés kiadása másképp Put vagy működik.

### <a id="ManualFailover"></a>Manuális kezdeményezése a regionális feladatátvétel
Azure Cosmos-adatbázis segítségével elindíthatja a feladatátvételt, az adatbázis-fiók érvényesítése a *végpont* (meghaladja az adatbázis) a teljes alkalmazás rendelkezésre állásának tulajdonságait. Mivel a hiba észlelése és a vezető választás a biztonsági és a liveness tulajdonságainak garantáltan, biztosítja, hogy az Azure Cosmos DB *nulla adatvesztés* kézi feladatátvételre bérlői által kezdeményezett művelet.

### <a id="AutomaticFailover"></a>Automatikus feladatátvétel
Azure Cosmos-adatbázis támogatja az automatikus feladatátvételt egy vagy több regionális kimaradások esetén. Egy regionális feladatátvétel során Azure Cosmos DB olvasási késése, hasznos üzemidő elérhetőségét, konzisztencia, és a szolgáltatásiszint-szerződések átviteli tart fenn. Azure Cosmos-adatbázis egy automatikus feladatátvételi művelet elvégzéséhez időtartama biztosít egy felső határa. Ez a az ablak a lehetséges adatvesztést a regionális kimaradás során.

### <a id="GranularFailover"></a>Másik feladatátvevő Granularitás van tervezve
Az automatikus és manuális feladatátvételi lehetőségeket jelenleg az adatbázis-fiók részletességű érhetők el. Vegye figyelembe, belső Azure Cosmos DB úgy lett kialakítva *automatikus* feladatátvételi egyeztetését részletességű adatbázis, gyűjtemény vagy akár partíciójának (a gyűjtemény tulajdonos a kulcsokat számos). 

### <a id="MultiHomingAPIs"></a>Az Azure Cosmos DB többhelyű API-k
Azure Cosmos DB teszi lehetővé az adatbázis segítségével együttműködhet logikai (régió független) vagy a fizikai (régióspecifikus) végpontok. A logikai végpontok biztosítja, hogy az alkalmazás átlátható módon lehet többhelyű feladatátvétel esetén is. Az utóbbi, fizikai végpontok, adja meg, az alkalmazásnak, hogy átirányítsa a részletesebb vezérlés olvas, és írja az adott régióban.

Olvasási beállításainak konfigurálásával kapcsolatos információkért a [SQL API](../cosmos-db/tutorial-global-distribution-sql-api.md), [Graph API](../cosmos-db/tutorial-global-distribution-graph.md), [tábla API](../cosmos-db/tutorial-global-distribution-table.md), és [MongoDB API](../cosmos-db/tutorial-global-distribution-mongodb.md) a saját megfelelő csatolt cikkeket.

### <a id="TransparentSchemaMigration"></a>Átlátható és konzisztens legyen az adatbázis-séma- és index áttelepítése 
Azure Cosmos-adatbázis található teljesen [sémát független](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Az adatbázismotor egyedi kialakításának lehetővé teszi, hogy automatikusan, és szinkron módon index összes, anélkül, hogy semmilyen sémát, illetve másodlagos indexek, az Ön ingests adat. Ez lehetővé teszi, hogy az globálisan elosztott alkalmazás gyorsan többször aggódni az adatbázisban séma- és index áttelepítési vagy összehangolása a sémamódosítások több fázisban alkalmazás végrehajtása nélkül. Azure Cosmos DB garantálja, hogy Ön kifejezetten tett házirendek indexelő módosításait nem lesz a teljesítmény vagy a rendelkezésre állási teljesítménycsökkenése.  

### <a id="ComprehensiveSLAs"></a>Átfogó SLA-k (túl csak magas rendelkezésre állás)
Globálisan elosztott adatbázis szolgáltatásként Azure Cosmos DB kínál jól meghatározott SLA **adatvesztés**, **rendelkezésre állási**, **P99 várakozási**, **átviteli** és **konzisztencia** egészére, függetlenül az adatbázishoz tartozó régiók az adatbázishoz.  

## <a id="LatencyGuarantees"></a>Késés garanciák
Egy globálisan elosztott adatbázis-szolgáltatás, például Azure Cosmos DB fő előnye, hogy az adatokhoz bárhol a világon kis késésű hozzáférést. Azure Cosmos-adatbázis biztosít garantált kis késleltetésű P99, különböző adatbázis-műveleteknél. A replikációs protokoll Azure Cosmos DB által biztosítja, hogy a Helyadatbázis-műveletek (ideális esetben olvas és ír) mindig megtörténik-e az ügyfél helyi régióban. A várakozási SLA-t az Azure Cosmos DB P99 egyaránt tartalmazza olvasások, (szinkron) indexelt írási műveletek és a kérelem-válasz különböző méretű lekérdezések. A késés garantálja az írási műveletek közé tartozik a tartós többsége kvórum véglegesíti a helyi adatközpontban.

### <a id="LatencyAndConsistency"></a>Késleltetés a kapcsolat konzisztencia 
Szinkron módon replikálja az írási műveletek kell biztosítani az erős konzisztencia globálisan elosztott beállítása globálisan elosztott szolgáltatás, vagy a szinkron világos sebessége és a nagy kiterjedésű hálózat megbízhatóságát, amely erős előírják kereszt-régió olvasások – hajtsa végre konzisztencia nagyobb késéseket és adatbázis-művelet romlik a rendelkezésre állás eredményez. Ezért annak érdekében, hogy garantált P99, kis késleltetést és rendelkezésre állás 99,99 % fiókokhoz egyetlen régión és fiókokhoz több területi laza konzisztencia és 99.999 %, olvassa el az összes fiókot a több területi adatbázis rendelkezésre állása, a szolgáltatás kell alkalmazni aszinkron replikációját. Ez szolgálna megköveteli, hogy a szolgáltatás is kell-e kínál [jól meghatározott, laza konzisztencia choice(s)](consistency-levels.md) – gyengébb (az alacsony késleltetés és a rendelkezésre állási garanciát nyújtanak) erős és ideális erősebb, mint a "végleges" konzisztencia (a felajánlott egy egyszerűen elsajátítható programozási modell).

Azure Cosmos DB biztosítja, hogy egy olvasási művelet nem igényel képes biztosítani az adott konzisztencia szintű garancia különféle régiókban replikák kapcsolódni. Hasonlóképpen biztosítja, hogy egy írási művelet nem get tiltsa le közben (azaz írási műveleteket aszinkron módon replikálódnak régiók) régiókra replikálódnak, a az adatokat. Több területi adatbázis fiókok több laza konzisztenciaszintek érhetők el. 

### <a id="LatencyAndAvailability"></a>Rendelkezésre állási várakozási ideje a kapcsolattal 
Késleltetés és a rendelkezésre állási az azonos érme két oldala. Döntésről bővebben várakozási ideje a stabil állapotot és a rendelkezésre állási állásuk sikertelen művelet. Az alkalmazás szempontból lassan futó adatbázis-művelet nem különböztethetők meg egy adatbázist, amely nem érhető el. 

Megkülönböztetni a nagy késleltetésű elérhetetlensége, Azure Cosmos DB biztosít egy abszolút felső határa késés különböző adatbázis-művelet. Ha az adatbázis-művelet tovább tart, mint a felsőé befejeződik, az Azure Cosmos DB időtúllépési hiba adja vissza. Az Azure Cosmos DB rendelkezésre állási SLA biztosítja, hogy a időtúllépések elleni SLA-elérhetőséget bájtjai számítanak. 

### <a id="LatencyAndThroughput"></a>Átviteli várakozási ideje a kapcsolattal
Azure Cosmos-adatbázis nem tesz, válassza ki a késés és átviteli között. Hogy eleget tegyen az SLA mindkét P99 és kézbesítése, hogy létrehozta az átviteli várakozási. 

## <a id="ConsistencyGuarantees"></a>Konzisztencia biztosítja
Amíg a [az erős konzisztencia modell](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) az arany szabvány programozhatóság, a nagyobb késleltetéssel járhat (stabil állapotban) elsajátíthatják a használatát áron származik, és csökkenteni a rendelkezésre állási (állásuk hibák). 

Azure Cosmos DB jól meghatározott programozási modellt biztosít Önnek a replikált adatok konzisztencia kapcsolatos OK. Ahhoz, hogy lehetővé teszik a többhelyű alkalmazásait, a konzisztencia-modellek Azure Cosmos Database által elérhetővé tett úgy tervezték, hogy a terület-független, és nem függ a régió, ahol az olvasási és írási is lehetséges a kiszolgálása. 

Azure Cosmos-adatbázis konzisztencia SLA garantálja, hogy az olvasási kérések 100 %-os megfelelnek-e az Ön (az alapértelmezett konzisztencia szint az adatbázis-fiók) vagy a kérésre felülbírált érték által kért konzisztenciaszint konzisztencia növekvő. Egy olvasási kérést tekinti a konzisztencia SLA-t kell teljesíteni, ha teljesülnek a konzisztenciaszint társított konzisztencia biztosítja. A következő táblázat a konzisztencia biztosítja, hogy Azure Cosmos DB által kínált meghatározott konzisztenciaszintek rögzíti.

**Az Azure Cosmos Adatbázisba adott konzisztenciaszint társított konzisztencia biztosítja**

<table>
    <tr>
        <td><strong>Konzisztenciaszint</strong></td>
        <td><strong>Konzisztencia-jellemzők</strong></td>
        <td><strong>SLA</strong></td>
    </tr>
    <tr>
        <td rowspan="3">Munkamenet</td>
        <td>Olvassa el a saját írási</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Monoton olvasása</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Egységes előtagja</td>
        <td>100%</td>
    </tr>
    <tr>
        <td rowspan="3">A kötött elavulási</td>
        <td>Monoton (belül régiónként) olvasása</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Egységes előtagja</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Kötött elavulási &lt; K, T</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Egységes előtagja</td>
        <td>Egységes előtagja</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Erős</td>
        <td>Linearizable</td>
        <td>100%</td>
    </tr>
</table>

### <a id="ConsistencyAndAvailability"></a>Rendelkezésre állási konzisztencia a kapcsolattal
A [lehetséges eredmény](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) , a [CAP tétel](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) bizonyul, a rendszer elérhetők maradnak, és a hibák állásuk linearizable konzisztencia nyújtsanak valóban nem lehetséges. Az adatbázis-szolgáltatás kell választania a CP vagy AP - CP-rendszerek linearizable konzisztencia helyett rendelkezésre állási leírtnál kisebb, míg a hozzáférési pont rendszerek leírtnál kisebb [linearizable konzisztencia](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) rendelkezésre állási helyett. Azure Cosmos DB soha nem sérti a kért konzisztenciaszint, így a hivatalos CP rendszer. Azonban a gyakorlatban konzisztencia nincs egy mindent vagy semmit felkínált – több jól meghatározott konzisztencia modellek mentén a konzisztencia pontszámot linearizable és a végleges konzisztencia között van. Az Azure Cosmos Adatbázisba azt próbált használatával azonosíthatók a laza konzisztencia modellek számos valós alkalmazhatósági és egy egyszerűen elsajátítható programozási modellt. Azure Cosmos-adatbázis a konzisztencia-rendelkezésre állási mellékhatásokkal navigál felajánlásával egy [több enyhíteni még jól meghatározott konzisztenciaszintek](consistency-levels.md) és a rendelkezésre állás 99,99 % az összes egyetlen régión és az összes több területi fiókok konzisztencia enyhíteni, és 99.999 %, olvassa el az összes fiókot a több területi adatbázis rendelkezésre állását. 

### <a id="ConsistencyAndAvailability"></a>Késés konzisztencia a kapcsolattal
Prof. Daniel Abadi által javasolt egy átfogóbb változata kap, és azt nevezzük [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), amely is késleltetés és a konzisztencia mellékhatásokkal stabil állapotban fiókjainak. Azt jelzi, hogy a stabil állapotot, az adatbázis-rendszer ki kell választania konzisztencia és a késleltetés között. Több laza konzisztencia modellek (biztonsági aszinkron replikációját, valamint a helyi olvasási, írási határozatképességére) Azure Cosmos DB biztosítja, hogy minden olvasási és írási helyi, az Olvasás, és régiók rendre írása.  Ez lehetővé teszi az Azure Cosmos DB tesz a kis késleltetésű biztosítja, hogy a konzisztenciaszintek a régión belül.  

### <a id="ConsistencyAndThroughput"></a>Átviteli sebesség konzisztencia a kapcsolattal
Mivel egy adott konzisztencia modell végrehajtásának függ a választott egy [kvórum típus](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), az átviteli sebesség is függ a választott konzisztencia. Például az Azure Cosmos Adatbázisba, erősen következetes olvasás RU díját nagyjából, hogy idővel konzisztenssé a olvassa be. Ebben az esetben szüksége lesz kiépítése kettős a RUs a gyűjteményen ugyanazon eléréséhez.
 
**Olvasási kapacitást biztosít az Azure Cosmos Adatbázisba meghatározott konzisztenciaszint kapcsolat**

![Konzisztencia- és átviteli közötti kapcsolat](./media/distribute-data-globally/consistency-and-throughput.png)

## <a id="ThroughputGuarantees"></a>Biztosítja, hogy az átviteli sebesség 
Cosmos. Azure-adatbázis lehetővé teszi a skála átviteli sebesség (, valamint, tárolás), rugalmasan attól függően, hogy az igény szerinti különböző régiókban között. 

**Egyetlen Azure Cosmos DB gyűjtemény (között három szilánkok) particionált, és ezután elosztva három Azure-régiók**

![Elosztott Cosmos. Azure-adatbázis és a particionált gyűjtemények](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

Egy Azure Cosmos DB gyűjteményt lekérdezi terjesztéséhez két dimenzió – régión belül és régiók között. Ezt a következőképpen teheti meg: 

* Egyetlen régión belül egy Azure Cosmos DB gyűjteményt van horizontálisan felskálázott erőforrás partíciók tekintetében. Mindegyik erőforrás partíció kulcsok készlete kezeli, és erős következetes és magas rendelkezésre állású virtuálisgép-replikációt állapot replikák egy készlete közötti címtár. Azure Cosmos-adatbázis egy olyan teljes szabályozott erőforrás rendszer, ahol erőforrás-partíció felelős kiveheti a részét a költségvetési lefoglalt rendszererőforrást átviteli kézbesítéséhez. Egy Azure Cosmos DB gyűjteményt skálázás az teljes mértékben transzparens – Azure Cosmos DB erőforrás partíciók kezeli, és felosztja a, és egyesíti az igény szerint. 
* Minden olyan erőforrás partíció majd több régióba pontjain. Erőforrás-partíciók ugyanazokkal a kulcsokkal rendelkező különböző régiókban partíció készletet alkotnak (lásd: [előző ábra](#ThroughputGuarantees)).  Erőforrás-partíció a partíción belül található koordinált állapot virtuálisgép-replikációt használ a különféle régiókban. Attól függően, hogy a konzisztenciaszint konfigurálva, az erőforrás-partíciók a partíción belül dinamikusan használatával konfigurálhatók különböző topológiával (például csillag, lánckapcsolású-lánc, fa stb.). 

Egy válaszidejű partíció felügyeleti, terheléselosztás és szigorú erőforrás irányítás Azure Cosmos DB teszi rugalmasan méretezési átviteli egy Azure Cosmos DB gyűjteményt a több Azure-régiók között. Egy gyűjtemény változó átviteli sebességet egy futtatási művelet az Adatbázisba az Azure Cosmos - hasonlít az egyéb adatbázis műveletek Azure Cosmos DB biztosítja, hogy a kérését az átviteli várakozási ideje a abszolút felső határa. Tegyük fel az alábbi ábrán egy felhasználói gyűjteményt a igény szerint rugalmasan kiosztott átviteli (beállításnak 1M - 10M kérelmek/másodperc közötti két régióban is).
 
**Egy felhasználói gyűjteménybe, és rugalmasan kiosztott átviteli sebesség (1M - 10M kérelmek/másodperc)**

![Az Azure Cosmos DB rugalmasan kiosztott átviteli sebesség](./media/distribute-data-globally/elastic-throughput.png)

### <a id="ThroughputAndConsistency"></a>Átviteli sebesség a kapcsolat konzisztencia 
Ugyanaz, mint a [konzisztencia tartozó kapcsolat átviteli](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Átviteli sebesség a kapcsolatot a rendelkezésre állást
Azure Cosmos-adatbázis továbbra is fennáll, ha meg szeretné őrizni az elérhetőségét a módosításai az átviteli sebesség. Azure Cosmos DB transzparens módon kezeli a partíciók (például megosztott, egyesítési, klónozási műveletek), és biztosítja, hogy a műveletek nem csökkentheti a teljesítményt és rendelkezésre állás, amíg az alkalmazás rugalmasan növeli vagy csökkenti a teljesítményt. 

## <a id="AvailabilityGuarantees"></a>Rendelkezésre állási garanciák
Azure Cosmos-adatbázis egy 99,99 % SLA-elérhetőséget fiókokhoz egyetlen régión és fiókokhoz több területi nyújt laza konzisztencia, és 99.999 %, olvassa el az összes fiókot a több területi adatbázis rendelkezésre állását. A korábban ismertetett Azure Cosmos DB rendelkezésre állását garantálja tartalmaznia kell egy abszolút felső határa késés minden adat- és a vezérlősík műveletekhez. A rendelkezésre állási garanciák steadfast és régiók vagy a földrajzi régiók közötti távolság szerint száma nem változik. Rendelkezésre állási garanciák egyaránt manuális, valamint automatikus feladatátvételi alkalmazni. Azure Cosmos DB átlátszó többhelyű API-k, győződjön meg arról, hogy az alkalmazás logikai végpontok is működik, és transzparens módon irányítani tudja a kérelmek feladatátvétel esetén az új régióban kínál. A PUT eltérően, az alkalmazás nem kell regionális feladatátvétel után újra kell telepíteni, és a rendelkezésre állási SLA megmaradjanak.

### <a id="AvailabilityAndConsistency"></a>Rendelkezésre állási tartozó kapcsolat konzisztencia, a késés és a teljesítmény
Rendelkezésre állási tartozó kapcsolat konzisztencia, a késés és a teljesítmény ismertetett [konzisztencia tartozó kapcsolatban van-e rendelkezésre állási](#ConsistencyAndAvailability), [várakozási ideje a kapcsolatot a rendelkezésre állást](#LatencyAndAvailability) és [átviteli sebesség a kapcsolatot a rendelkezésre állást](#ThroughputAndAvailability). 

## <a id="GuaranteesAgainstDataLoss"></a>Biztosítja, és a rendszer viselkedését "adatvesztés"
Az Azure Cosmos Adatbázisba mindegyik partíciójához (gyűjtemény) köszönhetően magas rendelkezésre állású replikákat, amely legalább 10-20 tartalék tartományok vannak elosztva a száma. Összes írt szinkron módon történik, és tartósan elkötelezettek által a replikák többsége másodlagosak ahhoz, azok vonatkozik, az ügyfélnek. Aszinkron replikáció közötti partíciók több régióba elosztva a koordinációs lesz alkalmazva. Az Azure Cosmos DB biztosítja, hogy a bérlő által kezdeményezett kézi feladatátvételre adatvesztés nélküli. Automatikus feladatátvétel során Azure Cosmos DB biztosítja, hogy az adatvesztési időszak a szolgáltatásiszint-szerződés részeként konfigurált a kötött elavulási intervallum egy felső határa.

## <a id="CustomerFacingSLAMetrics"></a>SLA-metrikáinak ügyfélkapcsolati
Azure Cosmos DB transzparens módon az átviteli sebesség, a késés, a konzisztencia és a rendelkezésre állási metrikák tesz elérhetővé. A metrikák programozott módon, és az Azure portálon (lásd a következő ábra) keresztül érhetők el. Különböző küszöbértékek használata Azure Application Insights-riasztások is állíthat be.
 
**Konzisztencia, a késés, az átviteli sebesség és a rendelkezésre állási metrikák transzparens módon érhetők el minden bérlő számára**

![Az Azure Cosmos DB felhasználói által látható SLA-metrikáinak](./media/distribute-data-globally/customer-slas.png)

## <a id="Next Steps"></a>Következő lépések
* Globális többszörözésére Azure Cosmos DB fiókja az Azure portál használatával, lásd: [hogyan hajthat végre az Azure portál használatával Azure Cosmos DB globális adatbázis-replikáció](tutorial-global-distribution-sql-api.md).
* Az Azure Cosmos DB több főkiszolgálós architektúrák megvalósításához, lásd: [több főkiszolgálós adatbázis architektúrák rendelkező Azure Cosmos DB](multi-region-writers.md).
* További információ az automatikus és manuális feladatátvétel Azure Cosmos DB fog működni, lásd: [regionális feladatátvétel az Azure Cosmos Adatbázisba](regional-failover.md).

## <a id="References"></a>Hivatkozások
1. Eric sörgyár. [Robusztus elosztott rendszerek felé](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)
2. Eric sörgyár. [SAPKA később – az alkalmazásának tizenkét éve hogyan módosultak a szabályok](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf)
3. Gilbert, Lynch. - [Sörgyár &#39; s feltevésen és egységes, elérhető megvalósíthatóságának, partíció hibatűrő webes szolgáltatások](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)
4. Daniel Abadi. [A Modern konzisztencia mellékhatásokkal elosztott adatbázis rendszerek kialakítása](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf)
5. János Kleppmann. [Állítsa le az adatbázisok CP vagy a hozzáférési pont hívása](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html)
6. Peter Bailis és mások. [A gyakorlati részleges határozatképességére probabilisztikus a kötött elavulási (PBS)](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
7. Naor és gyapjú. [Betöltési, a kapacitás és a rendelkezésre állási kvórum rendszerekben](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf)
8. Herlihy és a következő. [Lineralizability: Helyességét feltételt az egyidejű objektumok](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)
9. [Az Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
