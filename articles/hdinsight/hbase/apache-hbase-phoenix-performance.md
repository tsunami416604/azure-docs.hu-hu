---
title: Az Azure HDInsight Phoenix-teljesítmény
description: Ajánlott eljárások a Phoenix teljesítmény optimalizálása érdekében.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 8a67327497c96cba570d2ddb8c525bf5a60be790
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308217"
---
# <a name="apache-phoenix-performance-best-practices"></a>Az Apache Phoenix teljesítmény ajánlott eljárások

A leginkább fontos szempont a [Apache Phoenix](https://phoenix.apache.org/) teljesítmény optimalizálása az alapul szolgáló [Apache HBase](http://hbase.apache.org/). A Phoenix a HBase, HBase-műveletek, például a vizsgálatok SQL-lekérdezések alakítja interaktív irányítópultunkat relációs adatmodellekkel hoz létre. A következő tábla sémáját, a kijelölés és az elsődleges kulcsot, és az összes indexek használata a mezők sorrendjét a kialakítás befolyásolja a Phoenix teljesítményét.

## <a name="table-schema-design"></a>Séma Táblatervezés

A Phoenix egy táblát hoz létre, amikor, hogy a táblázat egy HBase-tábla van tárolva. A HBase-táblára (oszlopcsaláddal) oszlopokat, amelyek együtt érhetők el csoportjait tartalmazza. A Phoenix adatbázistábla egy sort a HBase táblában, ahol minden egyes sorban áll, verzióval ellátott cellák társított egy vagy több oszlop. Logikailag a HBase egyetlen sor rowkey ugyanazzal az értékkel rendelkező kulcs-érték párok, gyűjteménye. Azt jelenti minden kulcs-érték pár rowkey attribútummal rendelkezik, és a rowkey tulajdonságok esetén attribútum értékét a adott sorának esetében megegyezik.

A Phoenix tábla séma tervezésekor az elsődleges kulcs a Tervező, oszlop családi tervezési, egyéni oszlop tervezési és az adatok particionálásáról.

### <a name="primary-key-design"></a>Elsődleges kulcs kialakítása

A Phoenix egy tábla definiálva elsődleges kulcs határozza meg, hogyan a rowkey tulajdonságok esetén a mögöttes HBase-tábla adatok vannak tárolva. A HBase a csak egy adott sorának eléréséhez módja a rowkey tulajdonságok esetén. Emellett egy HBase-táblában tárolt adatok a rowkey tulajdonságok esetén van rendezve. A Phoenix a rowkey tulajdonságok esetén érték elkülönített változó összefűzésével előállítjuk értékeit minden a sorban a sorrendben vannak definiálva elsődleges kulcs oszlopa épít fel.

Például egy tábla a névjegyek, a Utónév, utolsó nevét, telefonszámát és címét, mind az azonos oszlopcsalád. Megadhat egy elsődleges kulcs egy növekvő sorozatszám alapján:

|rowkey tulajdonságok esetén|       Cím|   telefon| Keresztnév| Vezetéknév|
|------|--------------------|--------------|-------------|--------------|
|  1000|A San Gabriel Dr 1111.|1-425-000-0002|    János|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

Azonban ha gyakori lekérdezési pedig Vezetéknév szerint az elsődleges kulcs nem végezhet, mert egyes lekérdezések teljes tábla beolvasásával minden lastName értékének olvasásához szükséges. Ehelyett a lastName, firstName és társadalombiztosítási szám oszlop elsődleges kulcs adhatja meg. Ebben az utolsó oszlopban az elem egyértelműségének biztosításához két lakosai ugyanazzal a névvel, például a father és lányomtól ugyanaz címen.

|rowkey tulajdonságok esetén|       Cím|   telefon| Keresztnév| Vezetéknév| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|A San Gabriel Dr 1111.|1-425-000-0002|    János|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Ez az új elsődleges kulcs a sor a Phoenix által létrehozott kulcsok a következő lesz:

|rowkey tulajdonságok esetén|       Cím|   telefon| Keresztnév| Vezetéknév| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole – John-111|A San Gabriel Dr 1111.|1-425-000-0002|    János|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

A fenti első sorában az adatokat a rowkey tulajdonságok esetén jelölt látható módon:

|rowkey tulajdonságok esetén|       kulcs|   érték| 
|------|--------------------|---|
|  Dole – John-111|Cím |A San Gabriel Dr 1111.|  
|  Dole – John-111|telefon |1-425-000-0002|  
|  Dole – John-111|Keresztnév |János|  
|  Dole – John-111|Vezetéknév |Dole|  
|  Dole – John-111|socialSecurityNum |111| 

A rowkey tulajdonságok esetén most már az adatok másolatát tárolja. Fontolja meg a méretét és az Ön elsődleges kulcsa, a is tartalmazó oszlopok számát, mert ez az érték megtalálható a mögöttes HBase táblában lévő minden celláját.

Is, ha az elsődleges kulcs értékeket, amelyeket a rendszer monoton növekvő rendelkezik, létre kell hoznia a táblát, amelynek *gyűjtők sója* elkerüléséhez írási elérési pontokhoz történő létrehozásához – lásd: [adatokat](#partition-data).

### <a name="column-family-design"></a>Családi tervezési oszlop

Ha egyes oszlopokat, mint a többi gyakrabban elért, létre kell hoznia a gyakran használt oszlopok elkülönítése a ritkán használt oszlopok több oszlopcsaláddal.

Is ha egyes oszlopokat általában a férnek hozzá együtt, helyezze el oszlopokat az azonos oszlopcsalád.

### <a name="column-design"></a>Oszlop-Tervező

* Tartsa meg a VARCHAR oszlop alatt nagy oszlopok az i/o-költségek miatt körülbelül 1 MB. Lekérdezések feldolgozásakor a HBase teljes cellák megvalósul keresztül elküldi azokat az ügyfél előtt, és az ügyfél megkapja őket teljes egészében az alkalmazáskód őket leadása előtt.
* Oszlopbeli értékek kompakt formátumban, például a protopuf, Avro, msgpack vagy BSON Store. JSON nem ajánlott, mivel nagyobb.
* Vegye figyelembe, hogy tömöríti az adatokat, mielőtt tárolási történő kivágási, a késés és i/o-költségek.

### <a name="partition-data"></a>Partícióadatok

A Phoenix segítségével szabályozható a számot, ahol az adatok terjesztése, régiók, ami jelentősen megnövelheti olvasási/írási teljesítmény. A Phoenix tábla létrehozásakor sója, vagy előre fel az adatokat.

Só tábla létrehozása során, adja meg a védőérték gyűjtők száma:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Ez sózás felosztja a tábla elsődleges kulcs, az értékek kiválasztása automatikusan értékeit mentén. 

Szabályozhatja, ahol a táblázat elágazást fordulhat elő, akkor is előre feloszthatja az a tábla azáltal, hogy a tartományértékeknek, amely mentén a felosztás történik. Például hozzon létre egy táblát a felosztás mentén három régióban:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Index kialakítása

A Phoenix indexe egy HBase-tábla, amelyek vagy azok egy részét az indexelt táblából az adatok másolatát tárolja. Az index javítja a teljesítményt az adott típusú lekérdezések.

Ha több indexet és majd lekérdezni egy táblát, Phoenix automatikusan kiválasztja az ajánlott index a lekérdezéshez. Az elsődleges index, válassza ki az elsődleges kulcsok alapján automatikusan jön létre.

Várható lekérdezések esetén is az oszlopok megadásával hozhat létre másodlagos indexeket.

Az indexek tervezésekor:

* Csak hozzon létre a szükséges indexeket.
* Gyakran frissített táblák indexei számának korlátozásához. Egy táblához frissítések jelenti azt, hogy a főtábla mind az indextáblákat írási műveletek.

## <a name="create-secondary-indexes"></a>A másodlagos indexek létrehozása

A másodlagos indexek mi lenne lehet be egy pont keresési cserébe tárterület teljes tábla beolvasásával és írási sebesség olvasási teljesítménye növelhető. A másodlagos indexek adhatók hozzá vagy eltávolítja a tábla létrehozása után, és nem igényel változtatásokat a meglévő lekérdezéseket – lekérdezések csak gyorsabban futnak. Igényeitől függően célszerű lehet kezelt indexek, működési indexeket, vagy mindkettőt.

### <a name="use-covered-indexes"></a>Az érintett indexek használata

Az érintett indexek közé tartoznak a sor mellett az indexelt értékek adatai indexeket. Miután megtalálta a kívánt indexbejegyzése, van, nem szükséges elsődleges tábla eléréséhez.

A példában például létrehozhat egy másodlagos indexet csak socialSecurityNum oszlopában táblázatban forduljon. A másodlagos index lenne felgyorsítása socialSecurityNum értékek szerint szűrő lekérdezéseket, de más mezőértékek beolvasása lesz szükség, egy másik olvassa el a fő táblázaton.

|rowkey tulajdonságok esetén|       Cím|   telefon| Keresztnév| Vezetéknév| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole – John-111|A San Gabriel Dr 1111.|1-425-000-0002|    János|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Ha szeretné általában, a Vezetéknév és az socialSecurityNum megadott lastName kereséséhez, létrehozhat egy kezelt index, amely tartalmazza a Keresztnév és Vezetéknév szerint a tényleges adatok az indextáblában:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

A kezelt index lehetővé teszi, hogy a következő lekérdezés az összes adat beszerezni úgy, ha a másodlagos indexet tartalmazó tábla olvasásakor:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Funkcionális indexek használata

Funkcionális indexek indexet létrehozni egy tetszőleges kifejezést várt lehet használni a lekérdezésekben teszi lehetővé. Egy funkcionális index már működik a, és a egy lekérdezést a kifejezést használ, az index használható a data-tábla helyett az eredmények lekéréséhez.

Ha például sikerült létrehozni az indexet, amelyekkel a kombinált Utónév és a egy személy vezetékneve kis-és keresések:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Lekérdezésterv

A lekérdezés a fő szempontok a következők:

* A lekérdezési terv ismertetése, és ellenőrizze a várt viselkedés.
* Csatlakozzon a hatékony.

### <a name="understand-the-query-plan"></a>A lekérdezési terv ismertetése

A [az SQLLine](http://sqlline.sourceforge.net/), műveletek, amelyek végrehajtják a Phoenix a terv megtekintéséhez használja az SQL-lekérdezés követ magyarázat. Ellenőrizze, hogy a terv:

* Adott esetben az elsődleges kulcsot használja.
* Használja a másodlagos indexeket ahelyett, hogy a tábla megfelelő.
* TARTOMÁNY-ellenőrzési vagy KIHAGYÁSA vizsgálat, amikor csak lehetséges, helyett használja tábla beolvasása.

#### <a name="plan-examples"></a>Példák megtervezése

Például tegyük fel, hogy már rendelkezünk egy táblával, légi járatok nevű tároló repülőjáratokkal késleltetés kapcsolatos információkat tartalmaz.

Válassza ki a egy airlineid, légi járatok `19805`, ahol a airlineid egy mezőt, amely nem szerepel az elsődleges kulcs, vagy bármely indexben:

    select * from "FLIGHTS" where airlineid = '19805';

Futtassa a következő a magyarázat parancsot:

    explain select * from "FLIGHTS" where airlineid = '19805';

A lekérdezésterv így néz ki:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

Ebben a csomagban vegye figyelembe a teljes vizsgálat keresztül REPÜLŐJÁRATOK kifejezés helyett szerepel. Ezt a kifejezést jelzi, hogy a végrehajtási does egy tábla beolvasása a táblából, nem pedig a hatékonyabb tartomány-ellenőrzési vagy KIHAGYÁSA vizsgálat lehetőséggel összes sorának.

Most tegyük fel, hogy le szeretné kérdezni a járatok 2014. január 2. a szolgáltató a `AA` ahol annak flightnum értéke meghaladja az 1. Tegyük fel, hogy az oszlopok év, hónap, dayofmonth, szolgáltatója és flightnum példa táblának, és az összetett elsődleges kulcs minden részét képezik. A lekérdezés a következőképpen jelenne meg:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Most vizsgálja meg ezt a lekérdezést a csomagot:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Az eredményül kapott csomag a következő:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Az szögletes zárójelek között szereplő értékek értékek az elsődleges kulcs. Ebben az esetben a tartományértékeknek a 2014-es évre, havi 1 és dayofmonth 2 rögzítettek, de lehetővé teszi értékek flightnum indul el, 2 és a (`*`). A lekérdezésterv megerősíti, hogy az elsődleges kulcs használatban van a várt módon.

Ezután hozzon létre egy index nevű REPÜLŐJÁRATOK táblára `carrier2_idx` Ez csak a szolgáltató mezőre. Ez az index is flightdate, tailnum, eredet és flightnum kezelt oszlopként, amelynek az adatait tárolja az index.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Tegyük fel, hogy szeretne kapni a szolgáltatói flightdate és tailnum, ahogy a következő lekérdezést:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Ez az index használt kell megjelennie:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

Teljes listáját a cikkek, amelyek szerepelhetnek terv eredmények ismertetik, a csomagok ismertetik című a [Apache Phoenix teljesítményhangolási útmutatóból](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Csatlakozás hatékonyan

Általában érdemes illesztések elkerülése érdekében, kivéve, ha az egyik oldal kicsi, különösen a gyakori lekérdezések.

Ha szükséges, azt megteheti a nagyobb egyesítéseknél a `/*+ USE_SORT_MERGE_JOIN */` mutatót, de a nagy csatlakozzon – drága művelet hatalmas mennyiségű sort felett. Ha az összes oldali jobb táblák teljes mérete meghaladná a rendelkezésre álló memória, a `/*+ NO_STAR_JOIN */` mutatót.

## <a name="scenarios"></a>Forgatókönyvek

Az alábbi irányelveket a gyakori minták ismertetik.

### <a name="read-heavy-workloads"></a>Olvasási számítási feladatokhoz

Olvasási a használati esetek, győződjön meg arról, hogy indexeket használ. Továbbá időmegtakarítás olvasási-többletterhelést okoz, vegye figyelembe, az érintett indexek létrehozása.

### <a name="write-heavy-workloads"></a>Írási számítási feladatok

Írási műveltekből számítási feladatokhoz, ahol monoton növekvő a az elsődleges kulcsot hozzon létre védőérték gyűjtők írási elérési pontokhoz miatt a további vizsgálat szükséges általános olvasási teljesítmény rovására elkerülése érdekében. Ezenkívül UPSERT írhat rekordokat nagy számú használatakor kapcsolja ki Végrehajtású és a batch a rekordokat.

### <a name="bulk-deletes"></a>Tömeges törlés

Nagyméretű törlésekor kapcsolja be a Végrehajtású a TÖRLŐ lekérdezések kiadása előtt, hogy az ügyfélnek nincs szüksége az összes törölt sor sorkulcsok jegyezni. Végrehajtású megakadályozza, hogy az ügyfél pufferelés a sorokat, a törlés hatással, úgy, hogy a Phoenix is törölheti azokat közvetlenül a kiszolgálókon régió adnak vissza őket az ügyfél járó költségek nélkül.

### <a name="immutable-and-append-only"></a>Nem módosítható, és csak hozzáfűzése

Ha a forgatókönyv írási sebesség adatintegritásának előnyben részesítése szemben, vegye figyelembe, írási előre napló letiltása, ha a táblázatok létrehozásával:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

További információ erről és más beállítások: [Phoenix nyelvtani](http://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>További lépések

* [Az Apache Phoenix-finomhangolási útmutató](https://phoenix.apache.org/tuning_guide.html)
* [A másodlagos indexek](http://phoenix.apache.org/secondary_indexing.html)
