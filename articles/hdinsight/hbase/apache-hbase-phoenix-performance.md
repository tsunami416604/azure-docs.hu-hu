---
title: Phoenix teljesítmény az Azure HDInsightban
description: Gyakorlati tanácsok az Apache Phoenix teljesítményének optimalizálásához az Azure HDInsight-fürtökhöz
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 7f8f20be81e815414c283f7ec48aa6503e3b60ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552644"
---
# <a name="apache-phoenix-performance-best-practices"></a>Az Apache Phoenix teljesítményével kapcsolatos ajánlott eljárások

Az [Apache Phoenix](https://phoenix.apache.org/) teljesítményének legfontosabb szempontja az alapul szolgáló [Apache HBase optimalizálása.](https://hbase.apache.org/) A Phoenix létrehoz egy relációs adatmodellt a HBase tetején, amely az SQL-lekérdezéseket HBase-műveletekké, például vizsgálatokká alakítja. A táblaséma kialakítása, az elsődleges kulcs mezőinek kiválasztása és sorrendje, valamint az indexek használata mind befolyásolja a Phoenix teljesítményét.

## <a name="table-schema-design"></a>Táblaséma tervezése

Amikor létrehoz egy táblát a Phoenixben, a tábla egy HBase-táblában tárolódik. A HBase tábla oszlopcsoportokat (oszlopcsaládokat) tartalmaz, amelyek együttesen érhetők el. A Főnix táblázat egyik sora a HBase táblázat egyik sora, ahol minden sor egy vagy több oszlophoz társított verziós cellákból áll. Logikailag egyetlen HBase sor kulcs-érték párok gyűjteménye, amelyek mindegyike azonos sorkulcs-értékkel rendelkezik. Ez azt illeti, minden kulcs-érték pár nak van egy sorkulcs attribútuma, és a rowkey attribútum értéke megegyezik egy adott sorban.

A Főnix tábla sémája tartalmazza az elsődleges kulcs tervet, az oszlopcsalád tervezését, az egyes oszloptervezést és az adatok particionálásának módját.

### <a name="primary-key-design"></a>Elsődleges kulcs kialakítása

A Phoenix ben egy táblában definiált elsődleges kulcs határozza meg, hogyan tárolja az adatokat az alapul szolgáló HBase-tábla sorkulcsában. A HBase-ben az egyetlen módja annak, hogy egy adott sor a sorkulcs. Ezenkívül a HBase táblában tárolt adatok a sorkulcs szerint vannak rendezve. A Phoenix úgy építi fel a sorkulcs értékét, hogy összefűzi a sor egyes oszlopainak értékeit, az elsődleges kulcsban definiált sorrendben.

A névjegyek táblája például a keresztnév, a vezetéknév, a telefonszám és a cím, amelyek mindegyike ugyanabban az oszlopcsaládban található. Az elsődleges kulcsot növekvő sorszám alapján határozhatja meg:

|sorkulcs|       address|   telefon| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji között|

Ha azonban gyakran kérdezad a lastName alapján, előfordulhat, hogy ez az elsődleges kulcs nem teljesít jól, mert minden lekérdezéshez teljes táblavizsgálatszükséges az összes lastName értékének beolvasásához. Ehelyett megadhat egy elsődleges kulcsot a lastName, firstName és social security number oszlopokban. Ez az utolsó oszlop, hogy eloszlassa a két lakos ugyanazon a címen az azonos nevű, mint például egy apa és fia.

|sorkulcs|       address|   telefon| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji között| 222 |

Ezzel az új elsődleges kulcs a sor kulcsok által generált Phoenix lenne:

|sorkulcs|       address|   telefon| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-János-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Kálvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji között| 222 |

A fenti első sorban a sorkulcs adatai a következőképpen jelennek meg:

|sorkulcs|       kulcs|   érték|
|------|--------------------|---|
|  Dole-János-111|address |1111 San Gabriel Dr.|  
|  Dole-János-111|telefon |1-425-000-0002|  
|  Dole-János-111|firstName |John|  
|  Dole-János-111|lastName |Dole|  
|  Dole-János-111|socialSecurityNum |111|

Ez a sorkulcs most tárolja az adatok másolatait. Vegye figyelembe az elsődleges kulcsban szereplő oszlopok méretét és számát, mivel ez az érték az alapul szolgáló HBase tábla minden cellájában szerepel.

Továbbá, ha az elsődleges kulcs olyan értékeket tartalmaz, amelyek monoton módon növekvőek, létre kell hoznia a táblázatot *sógyűjtőkkel,* hogy elkerülje az írási hotspotok létrehozását - [lásd: Partíciós adatok](#partition-data).

### <a name="column-family-design"></a>Oszlopcsalád kialakítása

Ha egyes oszlopok gyakrabban érhetők el, mint mások, több oszlopcsaládot kell létrehoznia, hogy elválasszák a gyakran használt oszlopokat a ritkán használt oszlopoktól.

Továbbá, ha bizonyos oszlopok általában együtt érhetők el, helyezze ezeket az oszlopokat ugyanabba az oszlopcsaládba.

### <a name="column-design"></a>Oszlop tervezés

* A VARCHAR oszlopokat körülbelül 1 MB alatt tarthatja a nagy oszlopok I/O költségei miatt. A lekérdezések feldolgozásakor a HBase teljes mértékben megvalósítja a cellákat, mielőtt átküldené őket az ügyfélnek, és az ügyfél megkapja azokat teljes egészében, mielőtt átadná őket az alkalmazáskódnak.
* Az oszlopértékeket kompakt formátumban, például protobuf, Avro, msgpack vagy BSON formátumban tárolhatja. JSON nem ajánlott, mivel ez nagyobb.
* Fontolja meg az adatok tömörítését a tárolás előtt a késés és az I/O költségek csökkentése érdekében.

### <a name="partition-data"></a>Partícióadatok

A Phoenix lehetővé teszi az adatok terjesztésének helye szerinti régiók számának szabályozását, ami jelentősen növelheti az olvasási/írási teljesítményt. A Phoenix-tábla létrehozásakor sózhat, vagy előre feloszthatja az adatokat.

A sózzuk a táblázat létrehozása során, adja meg a só vödrök számát:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Ez a sózás felosztja a táblázatot az elsődleges kulcsok értékei mentén, és automatikusan kiválasztja az értékeket. 

A táblázat felosztásának szabályozásához a táblázatot előre feloszthatja, ha megadja azokat a tartományértékeket, amelyek mentén a felosztás történik. Például egy táblázat létrehozása három terület mentén:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Index kialakítása

A Phoenix index egy HBase-tábla, amely az indexelt tábla néhány vagy összes adatának másolatát tárolja. Az index javítja az adott típusú lekérdezések teljesítményét.

Ha több index van definiálva, majd lekérdez egy táblát, a Phoenix automatikusan kiválasztja a lekérdezés legjobb indexét. Az elsődleges index automatikusan jön létre a kiválasztott elsődleges kulcsok alapján.

A várható lekérdezések esetén másodlagos indexeket is létrehozhat az oszlopok megadásával.

Az indexek tervezésekor:

* Csak a szükséges indexeket hozza létre.
* Korlátozza a gyakran frissített táblák indexeinek számát. A tábla frissítései a főtábla és az indextáblák írási leírásává alakíthatók le.

## <a name="create-secondary-indexes"></a>Másodlagos indexek létrehozása

A másodlagos indexek javíthatják az olvasási teljesítményt azáltal, hogy a teljes tábla-vizsgálatot pontkereshetővé alakítják, a tárhely és az írási sebesség árán. A másodlagos indexek hozzáadhatók vagy eltávolíthatók a tábla létrehozása után, és nem igényelnek módosításokat a meglévő lekérdezéseken – a lekérdezések csak gyorsabban futnak. Az igényeitől függően fontolja meg a fedett indexek, funkcionális indexek vagy mindkettő létrehozását.

### <a name="use-covered-indexes"></a>Fedett indexek használata

A fedett indexek olyan indexek, amelyek az indexelt értékekmellett a sorból származó adatokat is tartalmaznak. Miután megtalálta a kívánt indexbejegyzést, nincs szükség az elsődleges táblához való hozzáférésre.

Például a példa kapcsolattartó táblában létrehozhat egy másodlagos indexet csak a socialSecurityNum oszlopban. Ez a másodlagos index felgyorsítaná a socialSecurityNum értékek szerint szűrő lekérdezéseket, de más mezőértékek beolvasásához egy másik olvasásra van szükség a főtáblához.

|sorkulcs|       address|   telefon| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-János-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Kálvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji között| 222 |

Ha azonban általában a socialSecurityNum alapján szeretné megkeresni a keresztnevet és a vezetéknevet, létrehozhat egy fedett indexet, amely tartalmazza a keresztnevet és a vezetéknevet tényleges adatként az indextáblában:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Ez a lefedett index lehetővé teszi, hogy a következő lekérdezés csak a másodlagos indexet tartalmazó táblából olvasva szerezze be az összes adatot:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Funkcionális indexek használata

A funkcionális indexek lehetővé teszik, hogy indexet hozzon létre egy tetszőleges kifejezésen, amelyet várhatóan használni fog a lekérdezésekben. Ha már van egy funkcionális index a helyén, és a lekérdezés használja ezt a kifejezést, az index lehet használni, hogy az eredmények beolvasása helyett az adattábla.

Létrehozhat például egy indexet, amely lehetővé teszi a kis- és nagybetűk et nem megkülönböztető keresések készítését egy személy kombinált kereszt- és vezetéknevére vonatkozóan:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Lekérdezés terv

A lekérdezéstervezés fő szempontja a következő:

* Ismerje meg a lekérdezési tervet, és ellenőrizze annak várható viselkedését.
* Hatékonyan csatlakozzon.

### <a name="understand-the-query-plan"></a>A lekérdezési terv ismertetése

Az [SQLLine](http://sqlline.sourceforge.net/)programban használja a EXPLAIN és az SQL-lekérdezést a Phoenix által végrehajtandó műveletterv megtekintéséhez. Ellenőrizze, hogy a terv:

* Szükség esetén használja az elsődleges kulcsot.
* Az adattábla helyett megfelelő másodlagos indexeket használ.
* A TABLE SCAN helyett lehetőség szerint RANGE SCAN vagy SKIP SCAN.Uses RANGE SCAN or SKIP SCAN Whenever scan, than TABLE SCAN.

#### <a name="plan-examples"></a>Példák tervezése

Tegyük fel például, hogy van egy FLIGHTS nevű táblája, amely a járat késési adatait tárolja.

A ( légitársaság ) légitársasággal `19805`rendelkező összes olyan járat kiválasztása, ahol az airlineid olyan mező, amely nem szerepel az elsődleges kulcsban vagy az indexben:

    select * from "FLIGHTS" where airlineid = '19805';

Futtassa a magyarázat parancsot az alábbiak szerint:

    explain select * from "FLIGHTS" where airlineid = '19805';

A lekérdezési terv így néz ki:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

Ebben a tervben vegye figyelembe a FULL SCAN OVER FLIGHTS kifejezést. Ez a kifejezés azt jelzi, hogy a végrehajtás a táblázat összes sorában table scan-t végez, nem pedig a hatékonyabb RANGE SCAN vagy SKIP SCAN beállítást.

Most tegyük fel, hogy 2014. `AA` Tegyük fel, hogy az oszlopok év, hónap, dayofmonth, fuvarozó és flightnum létezik a példa táblában, és mind része az összetett elsődleges kulcs. A lekérdezés a következőképpen néz ki:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Vizsgáljuk meg a lekérdezés tervét a következővel:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Az eredményül kapott terv:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

A szögletes zárójelben lévő értékek az elsődleges kulcsok értéktartományai. Ebben az esetben a tartományértékeket a 2014-es, az`*`1. Ez a lekérdezési terv megerősíti, hogy az elsődleges kulcs a várt módon van használva.

Ezután hozzon létre egy `carrier2_idx` indexet a JÁRATOK nevű táblában, amely csak a szállítmányozó mezőben található. Ez az index tartalmazza a repülési dátumot, a tailnumot, az eredetet és a flightnumot is fedett oszlopokként, amelyek adatait szintén az index tárolja.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Tegyük fel, hogy a szállítót a repülési dátummal és a tailnummal együtt szeretné beszerezni, ahogy az a következő lekérdezésben is:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Látnia kell, hogy ezt az indexet használja:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

A terv eredményeinek magyarázata című témakörben megjelenő elemek teljes listáját az [Apache Phoenix Hangolási útmutató](https://phoenix.apache.org/tuning_guide.html)Terv című szakaszában talál.

### <a name="join-efficiently"></a>Csatlakozás hatékonyan

Általában el szeretné kerülni az illesztéseket, kivéve, ha az egyik oldal kicsi, különösen a gyakori lekérdezések nél.

Ha szükséges, akkor nem nagy `/*+ USE_SORT_MERGE_JOIN */` csatlakozik a tipp, de a nagy illesztés egy drága művelet nagy számú sorok. Ha az összes jobb oldali asztal teljes mérete meghaladja a `/*+ NO_STAR_JOIN */` rendelkezésre álló memóriát, használja a tippet.

## <a name="scenarios"></a>Forgatókönyvek

Az alábbi irányelvek néhány gyakori mintát írnak le.

### <a name="read-heavy-workloads"></a>Leolvasási nagy munkaterhelések

Az olvasási nehéz használati esetek, győződjön meg arról, hogy indexeket használ. Emellett az olvasási idő többletterhelése érdekében fontolja meg a fedett indexek létrehozását.

### <a name="write-heavy-workloads"></a>Írás-nehéz számítási feladatok

Az írási nehéz számítási feladatok, ahol az elsődleges kulcs monoton módon növekszik, hozzon létre sóvödrök, hogy segítsen elkerülni az írási hotspotok, rovására a teljes olvasási átviteli sebességgel, mert a további vizsgálatok szükséges. Továbbá, ha a UPSERT segítségével nagy számú rekordot ír, kapcsolja ki az autoCommit-et, és kötegelje fel a rekordokat.

### <a name="bulk-deletes"></a>Tömeges törlés

Nagy adatkészlet törlésekor kapcsolja be az automatikus véglegesítést a DELETE lekérdezés kiadása előtt, hogy az ügyfélnek ne kelljen megjegyeznie az összes törölt sor sorkulcsait. Az automatikus véglegesítés megakadályozza, hogy az ügyfél pufferelje a DELETE által érintett sorokat, így a Phoenix közvetlenül a régiókiszolgálókon törölheti őket anélkül, hogy az ügyfélnek visszalenne térülve.

### <a name="immutable-and-append-only"></a>Nem módosítható és csak hozzáfűzhető

Ha a forgatókönyv előnyben részesíti az írási sebességet az adatok sértetlenségével szemben, fontolja meg az írási napló letiltását a táblák létrehozásakor:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Erről és más lehetőségekről az Apache Phoenix Grammar ( [Apache Phoenix Grammar](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>További lépések

* [Apache Phoenix hangolási útmutató](https://phoenix.apache.org/tuning_guide.html)
* [Másodlagos indexek](https://phoenix.apache.org/secondary_indexing.html)
