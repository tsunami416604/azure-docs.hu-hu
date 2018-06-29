---
title: Globális adatok Azure Cosmos DB terjesztése |} Microsoft Docs
description: Ismerje meg a globális adatbázisokat az Azure Cosmos Adatbázisból, egy globálisan elosztott, mutli-modell dokumentumadatbázis-szolgáltatás segítségével bolygónk méretű georeplikáció, a feladatátvételt és az adatok helyreállítás.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: b161fad822804ed0b2a6c7ad5315eca45984b19d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081489"
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Globális adatok Azure Cosmos DB terjesztése
Azure a széles körű – folyamatosan bővülő, és egy globális erőforrásigényét tért 50 + földrajzi régiók között. A globális jelenlét, az Azure biztosít a fejlesztők differenciált képességeit egyik létre, telepíthetnek és könnyen globálisan elosztott alkalmazások kezelésére képes. 

Az [Azure Cosmos DB](../cosmos-db/introduction.md) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása az alapvető fontosságú alkalmazásokhoz. Az Azure Cosmos DB biztosít kulcsrakész globális terjesztési, [átviteli sebesség és tárterület a rugalmas méretezést](../cosmos-db/partition-data.md) világszerte, egyjegyű ezredmásodperces késések fordulnak elő, a 99th PERCENTILIS [öt jól meghatározott konzisztencia modellek](consistency-levels.md), és magas rendelkezésre állás érdekében minden biztonsági mentés által garantált [iparágvezető átfogó SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Az Azure Cosmos DB [automatikusan elvégzi az adatok](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) anélkül, hogy a séma vagy az index felügyeleti foglalkozik. Több modellre szolgáltatás, és támogatja a dokumentum, a kulcs-érték, a graph és az oszlop-család adatmodellekben. Egy natív módon született felhőbe, mint Azure Cosmos DB van gondosan fejthetők vissza a több-bérlős és a globális terjesztési egészen az alapoktól fel.


![Az Azure Cosmos DB gyűjtemény particionálta és elosztva három régiók](./media/distribute-data-globally/global-apps.png)

**Egyetlen Azure Cosmos DB tárolót particionálta és elosztva a több Azure-régiók**

Ahogy rendelkezik Azure Cosmos DB felépítésekor, hozzáadását globális terjesztési utólag nem lehet. Nem lehet "menetes-on" egy "egyszeri" adatbázis helyrendszer elveire. A globálisan elosztott adatbázis által biztosított képességeket span felett marad, hogy a hagyományos földrajzi katasztrófa földrajzi--helyreállítási által kínált "egyhelyes" adatbázisok. Egyetlen hely adatbázisok ajánlat földrajzi-vész-Helyreállítási képességet globálisan elosztott adatbázisok szigorú részhalmazai. 

Azure Cosmos DB kulcsrakész globális terjesztési, a fejlesztőknek nem kell hozhat létre a saját replikációs állványok alkalmazó vagy a Lambda mintát (például [AWS DynamoDB replikációs](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)), vagy az adatbázis naplója keresztül "double írások" végrehajtása különféle régiókban. Végezzük *nem* ezen módszerek, javasoljuk, mivel helyességét ilyen szempontok biztosítása, és adja meg a hang SLA-k nem lehetséges. 

Ebben a cikkben nyújtunk Azure Cosmos DB globális terjesztési funkcióiról. Azt is ismertetik Azure Cosmos DB egyedi megközelítése átfogó SLA-kat biztosít. 

## <a id="EnableGlobalDistribution"></a>Kulcsrakész globális terjesztési engedélyezése
Azure Cosmos-adatbázis egyszerű megírásához globálisan elosztott alkalmazások engedélyezéséhez a következő lehetőségeket biztosítja. Ezek a képességek érhetők el az Azure Cosmos DB erőforrás-szolgáltató alapú [REST API-k](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/) továbbá az Azure-portálon.

Az alábbi videóban megtekinthet a kulcsrakész globális szoftverterjesztési funkció az Azure Cosmos Adatbázisba működés közben.

> [!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

### <a id="RegionalPresence"></a>A széles körű regionális jelenléte 
Azure folyamatosan nő a földrajzi jelenléte hozásával [új régiók](https://azure.microsoft.com/regions/) online. Azure Cosmos-adatbázis lesz minősítve a *eligazodást szolgáltatás* az Azure-ban, és alapértelmezés szerint minden új Azure régióban érhető. Ez lehetővé teszi, hogy rendelje hozzá az Azure Cosmos DB adatbázisfiók egy földrajzi régiót, amint Azure megnyílik az új üzleti régióját.

### <a id="UnlimitedRegionsPerAccount"></a>Az Azure Cosmos DB adatbázisfiók régiók korlátlan számú társítása
Azure Cosmos DB lehetővé teszi tetszőleges számú Azure-régiók társítása Azure Cosmos DB adatbázis fiókját. Geokerítések korlátozások (például a kínai, a németországi) kívül vannak, amely Azure Cosmos DB adatbázis fiókhoz tartozó régiók száma nincs korlátozva. Az alábbi ábrán egy eloszthatja 25 Azure-régiók konfigurált adatbázis-fiók.  

![25 Azure-régiók átfedés Azure Cosmos DB adatbázisfiók](./media/distribute-data-globally/spanning-regions.png)

**A bérlő Azure Cosmos-adatbázis adatbázis-fiók feszítőfa 25 Azure-régiók**


### <a id="PolicyBasedGeoFencing"></a>Csoportházirend-alapú geokerítések
Csoportházirend-alapú geokerítések Azure Cosmos DB terveztek. Geokerítések adatok irányítási és a megfelelőségi korlátozások biztosításához fontos összetevője, és előfordulhat, hogy egy adott területre társít a fiókját. Geokerítések példái közé tartoznak (azonban nem csak) hatókör a globális terjesztési a régiók szuverén felhő (például a kínai és a németországi) vagy a kormányzati adózás határ (például Ausztrália) belül. A házirendek az Azure-előfizetéséhez, a metaadatok segítségével szabályozza.

### <a id="DynamicallyAddRegions"></a>Dinamikus hozzáadása és eltávolítása a régiók
Azure Cosmos DB lehetővé teszi (társítható) hozzáadása vagy eltávolítása (leválasztani) régiókban található adatbázis fiókját a eddigi (lásd: [előző ábra](#UnlimitedRegionsPerAccount)). Az adatok így vannak elrendezve partíciók párhuzamos replikáció címtár Azure Cosmos DB biztosítja, hogy egy új régió bekerül, esetén elérhető műveletek 30 percen belül bárhol a világon (feltéve, hogy az adatok több 100 TB-nyi vagy kisebb). 

### <a id="FailoverPriorities"></a>Feladatátvételi prioritások
Abban az esetben nem tervezett kimaradás regionális feladatátvétel pontos sorrendjét szabályozásához Azure Cosmos DB lehetővé teszi a hozzárendelni egy *prioritás* az adatbázishoz tartozó különböző régiókban fiókhoz (lásd az alábbi ábrát). Az Azure Cosmos DB biztosítja, hogy az automatikus feladatátvételre kerül sor a megadott prioritási sorrendben. Regionális feladatátvétel kapcsolatos további információkért lásd: [az üzletmenet folytonossága érdekében az Azure Cosmos Adatbázisba automatikus regionális feladatátvétel](regional-failover.md).


![Feladatátvételi prioritások konfigurálása az Azure Cosmos DB](./media/distribute-data-globally/failover-priorities.png)

**A bérlő Azure Cosmos-adatbázis is konfigurálhat a feladatátvételi prioritási sorrendben (jobb oldali ablaktábla) egy adatbázis-fiókjához társított régiók**

### <a id="ConsistencyLevels"></a>Több, jól meghatározott konzisztencia modellek globálisan elosztott adatbázisok
Támogatja az Azure Cosmos DB [több jól meghatározott, intuitív és gyakorlati konzisztencia modellek](consistency-levels.md) SLA-k által támogatott. Kiválaszthatja, hogy egy adott konzisztencia modell (a rendelkezésre álló lehetőségek listájában) attól függően, hogy a munkaterhelés forgatókönyveket. 

### <a id="TunableConsistency"></a>Globálisan replikált adatbázisok hangolható konzisztencia
Azure Cosmos-adatbázis programozott és az alapértelmezett konzisztencia választás futásidőben egy kérelem alapon enyhíteni teszi lehetővé. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Dinamikusan konfigurálható olvasási és írási régiók
Azure Cosmos DB "Olvasás", "write" vagy "olvasási/írási" régiók lehetővé teszi a régiók (az adatbázishoz tartozó) konfigurálását. 

### <a id="ElasticallyScaleThroughput"></a>Azure-régiók közötti rugalmasan méretezési átviteli sebesség
Rugalmasan méretezhető egy Cosmos-DB Azure-tárolót üzembe helyezési átviteli által programozott módon. Az átviteli sebesség a Azure Cosmos DB tároló terjesztése régiókra vonatkozik.

### <a id="GeoLocalReadsAndWrites"></a>Földrajzi helyi olvasása és írása
Egy globálisan elosztott adatbázis a fő előnyt az jelenti, hogy adatok bárhol a világon kis késésű hozzáférést kínál. Azure Cosmos-adatbázis kis késleltetésű olvasások kínál, és írja: a világméretű 99th PERCENTILIS visszaadása. Biztosítja, hogy az összes olvasási szolgáltatott legközelebbi (helyi) régióban. Az olvasási kérelem kiszolgálására, a helyi a régióban, amelyben az olvasás kiadott kvórum szolgál. Ugyanez vonatkozik írási műveleteket. Az írási elfogadott, csak azt követően replikák többsége tartósan sértették meg az írási helyileg, de éppen által kezdeményezett távoli replikákon lehet visszaigazolni az írási műveletek nélkül. Másképp helyezése, a replikáció protokoll Azure Cosmos DB alatt működik, feltételezve, hogy az olvasási és írási határozatképességére a rendszer mindig a helyi a régióban, ahol a kérelmet adtak ki.

### <a id="ManualFailover"></a>Manuális feladatátvétel
Azure Cosmos-adatbázis segítségével elindíthatja a feladatátvételt egy adatbázis-fiók ellenőrzése a *végpont* (meghaladja az adatbázis) a teljes alkalmazás rendelkezésre állásának tulajdonságait. Mivel a biztonsági és a hiba észlelése és a vezető választás liveness tulajdonságait is garantáltan, biztosítja, hogy az Azure Cosmos DB *nulla adatvesztés* kézi feladatátvételre bérlői által kezdeményezett művelet.

### <a id="AutomaticFailover"></a>Automatikus feladatátvétel
Azure Cosmos-adatbázis automatikus feladatátvétel során egy vagy több regionális kimaradások esetén támogatja. Egy regionális feladatátvétel során Azure Cosmos DB olvasási késése, hasznos üzemidő elérhetőségét, konzisztencia, és a szolgáltatásiszint-szerződések átviteli tart fenn. Azure Cosmos-adatbázis egy felső határa biztosít egy automatikus feladatátvételi művelet elvégzéséhez időtartama. Ez a az ablak az esetleges adatvesztés regionális kimaradás során.

### <a id="GranularFailover"></a>Másik feladatátvevő Granularitás van tervezve
Az automatikus és manuális feladatátvételi lehetőségeket jelenleg az adatbázis-fiók részletességű érhetők el. Vegye figyelembe, belső Azure Cosmos DB úgy lett kialakítva *automatikus* feladatátvételi egyeztetését részletességű adatbázis, a tároló vagy akár partíciójának (a tároló, a tulajdonos kulcsok számos). 

### <a id="MultiHomingAPIs"></a>Az Azure Cosmos DB több homing
Azure Cosmos-adatbázis lehetővé teszi egy adatbázist vagy interakciót *logikai* (régió-független) vagy *fizikai* (régióspecifikus) végpontok. Logikai végpontok használata biztosítja, hogy az alkalmazás átlátható módon lehet többhelyű feladatátvétel. Az utóbbi egy fizikai végpont olvasások átirányítja az alkalmazásba részletesebb felügyeletét biztosítja, és írja az adott régióban.

Az írásvédett beállítások konfigurálásával kapcsolatos információkért a [SQL API](../cosmos-db/tutorial-global-distribution-sql-api.md), [tábla API](../cosmos-db/tutorial-global-distribution-table.md), és [MongoDB API](../cosmos-db/tutorial-global-distribution-mongodb.md) a cikkeiben.

### <a id="TransparentSchemaMigration"></a>Átlátható és konzisztens legyen az adatbázis-séma- és index áttelepítése 
Azure Cosmos-adatbázis található teljesen [séma-független](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Az adatbázismotor egyedi kialakításának automatikusan lehetővé teszi, hogy Azure Cosmos adatbázis, és szinkron módon index adatfeldolgozást, akkor az adatok anélkül, hogy semmilyen sémát, illetve másodlagos indexek a felhasználótól. Ez lehetővé teszi, hogy az globálisan elosztott alkalmazás gyorsan többször aggódni az adatbázisban séma- és index áttelepítési vagy összehangolása a sémamódosítások több fázisban alkalmazás végrehajtása nélkül. Az Azure Cosmos DB biztosítja, hogy az, hogy Ön kifejezetten tett házirendek indexelő módosításait nem eredményez teljesítmény vagy a rendelkezésre állási teljesítménycsökkenést.  

### <a id="ComprehensiveSLAs"></a>Átfogó SLA-k (túl magas rendelkezésre állás)
Globálisan elosztott adatbázis szolgáltatásként Azure Cosmos DB kínál jól meghatározott és átfogó SLA **rendelkezésre állási**, **késés**, **átviteli**, és **konzisztencia** globális léptékű az adatbázishoz tartozó régiók függetlenül fut az adatbázishoz.  

## <a id="LatencyGuarantees"></a>Késés garanciák
Egy globálisan elosztott adatbázis-szolgáltatás, például Azure Cosmos DB fő előnye, hogy az adatokhoz bárhol a világon kis késésű hozzáférést. Azure Cosmos-adatbázis, a különböző adatbázis-műveleteknél 99th PERCENTILIS garantált kis késleltetést biztosítja. A replikációs protokoll Azure Cosmos DB által biztosítja, hogy a Helyadatbázis-műveletek (olvasási és írási) mindig megtörténik-e az ügyfél helyi régióban. A várakozási SLA-t az Azure Cosmos DB olvasások, a (szinkron) indexelt írások és lekérdezések, a 99th PERCENTILIS garanciákat nyújt a különböző kérelem-válasz méretű. A késés garantálja az írási műveletek közé tartozik a tartós többsége kvórum véglegesíti a helyi régión belül.

### <a id="LatencyAndConsistency"></a>Késleltetés a kapcsolat konzisztencia 
Az erős konzisztencia globálisan elosztott beállítása tesz globálisan elosztott szolgáltatás, szinkron módon replikálja az írási műveletek vagy igényel kereszt-régió olvasások szinkron módon végrehajtásához. A világos és a nagy kiterjedésű hálózat megbízhatóságát sebessége határozzák meg, hogy, hogy az erős konzisztencia az magasabb késések és az adatbázis-művelet romlik a rendelkezésre állás eredményezheti. Ezért annak érdekében, hogy a 99th PERCENTILIS és rendelkezésre állás 99,99 % fiókokhoz egyetlen régión és laza konzisztencia fiókokhoz több területi és több területi adatbázis fiókokhoz, a szolgáltatás elérhetőségét 99.999 % alacsony késleltetésű garantált aszinkron replikáció kell alkalmazni. Ez szolgálna megköveteli, hogy a szolgáltatás is kell-e kínál [jól meghatározott, laza konzisztencia modellek](consistency-levels.md) – mint erős (az alacsony késleltetés és a rendelkezésre állási garanciát nyújtanak) és ideális erősebb, mint a "végleges" konzisztencia gyengébb (az egy intuitív programozási modell).

Azure Cosmos DB biztosítja, hogy lépjen kapcsolatba a replikák meghatározott konzisztencia szintű garancia továbbítására különféle régiókban nem szükséges egy olvasási művelet. Hasonlóképpen, amely biztosítja, hogy egy írási művelet nem get tiltsa le, amíg a adatait replikálja a rendszer minden egyes keresztül ez azt jelenti, hogy írási műveleteket aszinkron módon replikálódnak régiók). Több területi adatbázis fiókok egyaránt erős, valamint több laza konzisztenciaszintek érhetők el. 

### <a id="LatencyAndAvailability"></a>Rendelkezésre állási várakozási ideje a kapcsolattal 
Késleltetés és a rendelkezésre állási az azonos érme két oldala. Egy művelet stabil állapotot és a rendelkezésre állási hibák és hálózati partíciók késés van szó. Az alkalmazás szempontból lassan futó adatbázis-művelet nem különböztethetők meg egy adatbázist, amely nem érhető el. 

Megkülönböztetni a nagy késleltetésű elérhetetlensége, az Azure Cosmos DB egy abszolút felső határa biztosít késés különböző adatbázis-művelet. Ha az adatbázis-művelet tovább tart, mint a felsőé befejeződik, az Azure Cosmos DB időtúllépési hiba adja vissza. Az Azure Cosmos DB rendelkezésre állási SLA biztosítja, hogy a időtúllépések elleni SLA-elérhetőséget bájtjai számítanak. 

### <a id="LatencyAndThroughput"></a>Átviteli várakozási ideje a kapcsolattal
Azure Cosmos-adatbázis nem tesz, válassza ki a késés és átviteli között. Az SLA a 99th PERCENTILIS mindkét várakozási eleget tegyen, és ellátta teljesítményt nyújt. 

## <a id="ConsistencyGuarantees"></a>Konzisztencia biztosítja
Amíg a [az erős konzisztencia modell](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) az arany szabvány, az adatok programozhatóság (stabil állapotban) magasabb késés elsajátíthatják a használatát áron származik, és csökkenteni a rendelkezésre állási (állásuk hibák). 

Azure Cosmos DB jól meghatározott programozási modellt biztosít, a replikált adatok konzisztencia kapcsolatos OK. Ahhoz, hogy egyszerűen hozhatók létre globálisan elosztott alkalmazások többhelyű alkalmas, a konzisztencia-modellek Azure Cosmos Database által elérhetővé tett lettek kialakítva régió-független, és független az a régió, ahol az olvasási és írási folyamatban van a és kiszolgálása között. 

Azure Cosmos-adatbázis konzisztencia SLA garantálja, hogy az olvasási kérések 100 %-os felel meg a konzisztencia garancia Ön (vagy az adatbázis-fiókot, vagy a kérelem szint) által megadott konzisztencia modell. Egy olvasási kérést tekinthető teljesül-e a konzisztencia SLA-t, ha teljesülnek a konzisztenciaszint társított konzisztencia biztosítja. A következő táblázat a konzisztencia biztosítja, hogy adott konzisztencia modellek Azure Cosmos DB által kínált rögzíti.

<table>
    <tr>
        <td><strong>Konzisztencia-modell</strong></td>
        <td><strong>Konzisztencia-jellemzők</strong></td>
        <td><strong>SLA</strong></td>
    </tr>
        <tr>
        <td>Erős</td>
        <td>Linearizable</td>
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
        <td>Egységes előtagja</td>
        <td>Egységes előtagja</td>
        <td>100%</td>
    </tr>
</table>

**Egy adott konzisztencia modellt az Azure Cosmos Adatbázisba társított konzisztencia biztosítja**


### <a id="ConsistencyAndAvailability"></a>Rendelkezésre állási konzisztencia a kapcsolattal
A [lehetséges eredmény](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) , a [CAP tétel](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) bizonyul a rendszer esetén elérhetők maradnak, és a hibák állásuk linearizable konzisztencia nyújtsanak valóban nem lehetséges. Az adatbázis-szolgáltatás kell választania a CP vagy a hozzáférési pont, a CP-rendszerek ahol leírtnál kisebb a rendelkezésre állási linearizable konzisztencia helyett a során a hozzáférési pont rendszerek leírtnál kisebb [linearizable konzisztencia](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) rendelkezésre állási helyett. Azure Cosmos DB soha nem sérti a kért konzisztencia modell, így a hivatalos CP rendszer. Azonban a gyakorlatban konzisztencia nincs mindent vagy semmit felkínált; nincsenek több jól meghatározott konzisztencia modellek közötti linearizable és a végleges konzisztencia a konzisztencia pontszámot mentén. Az Azure Cosmos Adatbázisba több laza konzisztencia modellt a valós életben előforduló helyzetek vonatkoznak, és intuitív használandó azonosítja. Azure Cosmos-adatbázis a konzisztencia-rendelkezésre állási mellékhatásokkal navigál felajánlásával egy [több enyhíteni még jól meghatározott konzisztencia modellek](consistency-levels.md) és a rendelkezésre állás 99,99 % összes egyetlen régió adatbázis fiókok és 99.999 % olvasási és írási a rendelkezésre állás az összes több területi adatbázis-fiók. 

### <a id="ConsistencyAndAvailability"></a>Késés konzisztencia a kapcsolattal
Egy átfogóbb változata kap tétel nevezik [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), amely is késleltetés és a konzisztencia mellékhatásokkal stabil állapotban fiókjainak. Meghatározza, hogy egy stabil állapotban egy adatbázisrendszer ki kell választania konzisztencia és a késleltetés között. Több laza konzisztencia modellek (aszinkron replikálás és helyi olvasási és írási határozatképességére által támogatott) Azure Cosmos DB biztosítja, hogy minden olvasási és írási helyi, az Olvasás, és régiók rendre írása. Ez lehetővé teszi az Azure Cosmos DB tesz a kis késleltetésű biztosítja, hogy a megadott konzisztencia modellek régión belül.  

### <a id="ConsistencyAndThroughput"></a>Átviteli sebesség konzisztencia a kapcsolattal
Mivel egy adott konzisztencia modell végrehajtásának függ a választott egy [kvórum típus](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), átviteli is függ a konzisztencia-modell választás. Például az Azure Cosmos DB, erősen konzisztens olvasása a RU kell fizetni az nagyjából *dupla* , hogy idővel konzisztenssé olvasások. Ebben az esetben szüksége lesz kettős ugyanazon eléréséhez RUs kiépítéséhez.


![Konzisztencia- és átviteli közötti kapcsolat](./media/distribute-data-globally/consistency-and-throughput.png)

**Kapcsolat egy adott konzisztencia modellhez Azure Cosmos DB olvasási kapacitás**

## <a id="ThroughputGuarantees"></a>Biztosítja, hogy az átviteli sebesség 
Azure Cosmos-adatbázis lehetővé teszi a skála átviteli sebesség (, valamint, tárolás), rugalmasan tetszőleges számú attól függően, hogy az igények vagy igény szerinti régiók között. 

![Elosztott Cosmos. Azure-adatbázis és a particionált gyűjtemények](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

**Egyetlen Azure Cosmos DB tárolót (között három erőforrás partíciók egy régiót belül) vízszintesen particionált, és ezután globálisan elosztva három Azure-régiók**

Egy Azure Cosmos DB tároló két dimenzió régión belül (i) és (ii) különböző régiókban lekérdezi terjesztése. Ezt a következőképpen teheti meg: 

* **Helyi terjesztési**: egyetlen régión belül egy Azure Cosmos DB tárolóhoz van vízszintesen horizontálisan a *erőforrás partíciók*. Minden erőforrás partíciós kulcsok készlete kezeli, és erősen egységes, és magas rendelkezésre állású fizikailag képviselt négy-replikák által más néven a *replikakészlethez* és a virtuálisgép-replikációt állapotban ezeket a replikák között. Azure Cosmos-adatbázis egy olyan teljes erőforrás-szabályozott rendszer, ahol erőforrás-partíció felelős kiveheti a részét a költségvetési lefoglalt rendszererőforrást átviteli sebesség biztosításához. A felhasználók számára átlátható, egy Azure Cosmos DB tároló skálázás. Azure Cosmos-adatbázis kezeli az erőforrás-partíciók és felosztja és fűzi össze azokat tárolóként igény szerint, és átviteli követelményei megváltoznak. 
* **Globális terjesztési**: Ha több területi adatbázis, minden olyan erőforrás partíció majd azokban a régiókban között van elosztva. Erőforrás-partíciók ugyanazokkal a kulcsokkal rendelkező különböző régiókban űrlap között *set partícióazonosító* (lásd: [előző ábra](#ThroughputGuarantees)).  Erőforrás partíció a partíción belül található koordinált az adatbázishoz tartozó különféle régiókban állapot virtuálisgép-replikációt használ. Attól függően, hogy a konzisztenciaszint konfigurálva, az erőforrás-partíciók a partíción belül dinamikusan használatával konfigurálhatók különböző topológiával (például csillag, lánckapcsolású-lánc, fa stb.). 

Egy válaszidejű partíció felügyeleti, terheléselosztás és szigorú erőforrás irányítás Azure Cosmos DB teszi rugalmasan méretezési átviteli egy Azure Cosmos DB tároló, vagy az adatbázis társított több Azure-régiók között. Egy futtatási művelet az Azure Cosmos Adatbázisba kiosztott átviteli sebesség módosítása. Más adatbázis-műveletek hasonló, Azure Cosmos DB biztosítja, hogy a kiosztott átviteli sebesség módosítására irányuló kérelem késését a abszolút felső határa. Például az alábbi ábrán egy ügyfél tároló a igény szerint rugalmasan kiosztott átviteli (beállításnak 1M - 10M kérelmek/másodperc közötti két régióban is).

![Az Azure Cosmos DB rugalmasan kiosztott átviteli sebesség](./media/distribute-data-globally/elastic-throughput.png)

**Az ügyfél-tárolóban rugalmasan kiosztott átviteli sebesség (1M - 10M kérelmek/másodperc eltérő)**

### <a id="ThroughputAndConsistency"></a>Átviteli sebesség a kapcsolat konzisztencia 
Ugyanez a [konzisztencia tartozó kapcsolat átviteli](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Átviteli sebesség a kapcsolatot a rendelkezésre állást
Azure Cosmos-adatbázis továbbra is fennáll, ha meg szeretné őrizni a magas rendelkezésre állású módosításai kiosztott átviteli sebesség. Azure Cosmos DB transzparens módon kezeli az erőforrás-partíciók (és a felosztott, egyesítési és klónozási műveletek), és biztosítja, hogy a műveletek nem csökkentheti a teljesítményt és rendelkezésre állás, amíg az alkalmazás rugalmasan növeli vagy csökkenti a teljesítményt. 

## <a id="AvailabilityGuarantees"></a>Rendelkezésre állási garanciák
Azure Cosmos-adatbázis egy 99,99 % SLA-elérhetőséget az összes egyetlen régión adatbázis és az összes több területi fiókok laza konzisztencia és 99.999 % elérhetőségét a fiókokhoz több területi adatbázis kínál. A korábban ismertetett Azure Cosmos DB rendelkezésre állását garantálja tartalmaznia kell egy abszolút felső határa késésének minden adat- és a vezérlősík műveletekhez. A rendelkezésre állási garanciák régiók vagy a földrajzi régiók közötti távolság száma nem változik. Rendelkezésre állási garanciák tekintetében egyaránt manuális, valamint automatikus feladatátvétel alkalmazhatók. Azure Cosmos DB átlátszó többhelyű API-k, győződjön meg arról, hogy az alkalmazás logikai végpontok is működik, és transzparens módon irányítani tudja a kérelmek feladatátvétel új régióban kínál. Az alkalmazás nem kell újra kell telepíteni, abban az esetben, ha egy regionális feladatátvétel és a rendelkezésre állási SLA-k mindig megmaradnak.

### <a id="AvailabilityAndConsistency"></a>Rendelkezésre állási tartozó kapcsolat konzisztencia, a késés és a teljesítmény
A szakaszok elérhetőségét a kapcsolat konzisztencia, a késés és a teljesítmény ismertetett [konzisztencia tartozó kapcsolatban van-e rendelkezésre állási](#ConsistencyAndAvailability), [várakozási ideje a kapcsolatot a rendelkezésre állást](#LatencyAndAvailability) és [Átviteli sebesség a kapcsolatot a rendelkezésre állást](#ThroughputAndAvailability). 

## <a id="CustomerFacingSLAMetrics"></a>SLA-metrikáinak ügyfélkapcsolati
Azure Cosmos DB transzparens módon az átviteli sebesség, a késés, a konzisztencia és a rendelkezésre állási metrikák tesz elérhetővé. Programozott módon, és az Azure-portálon keresztül érhetők el a metrikák (lásd az alábbi ábrát). Különböző küszöbértékek használata Azure Application Insights-riasztások is állíthat be.
 

![Az Azure Cosmos DB felhasználói által látható SLA-metrikáinak](./media/distribute-data-globally/customer-slas.png)

**Konzisztencia, a késés, az átviteli sebesség és a rendelkezésre állási metrikák transzparens módon érhetők el minden bérlő számára**

## <a id="Next Steps"></a>Következő lépések
* Globális többszörözésére Azure Cosmos DB fiókja az Azure portál használatával, lásd: [hogyan hajthat végre az Azure portál használatával Azure Cosmos DB globális adatbázis-replikáció](tutorial-global-distribution-sql-api.md).
* Az Azure Cosmos DB több főkiszolgálós architektúrák megvalósításához, lásd: [több főkiszolgálós adatbázis architektúrák rendelkező Azure Cosmos DB](multi-region-writers.md).
* További információ az automatikus és manuális feladatátvétel Azure Cosmos DB fog működni, lásd: [regionális feladatátvétel az Azure Cosmos Adatbázisba](regional-failover.md).

## <a id="References"></a>Hivatkozások
1. Eric sörgyár. [Robusztus elosztott rendszerek felé](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)
2. Eric sörgyár. [SAPKA később – az alkalmazásának tizenkét éve hogyan módosultak a szabályok](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf)
3. Gilbert, Lynch. - [Sörgyár&#39;s feltevésen és egységes, elérhető megvalósíthatóságának, partíció hibatűrő webes szolgáltatások](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)
4. Daniel Abadi. [A Modern konzisztencia mellékhatásokkal elosztott adatbázis rendszerek kialakítása](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf)
5. János Kleppmann. [Állítsa le az adatbázisok CP vagy a hozzáférési pont hívása](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html)
6. Peter Bailis és mások. [A gyakorlati részleges határozatképességére probabilisztikus a kötött elavulási (PBS)](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
7. Naor és gyapjú. [Betöltési, a kapacitás és a rendelkezésre állási kvórum rendszerekben](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf)
8. Herlihy és a következő. [Lineralizability: Helyességét feltételt az egyidejű objektumok](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)
9. [Az Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
