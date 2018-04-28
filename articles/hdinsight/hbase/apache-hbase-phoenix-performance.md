---
title: Az Azure HDInsight Phoenix teljesítmény |} Microsoft Docs
description: Gyakorlati tanácsok Phoenix teljesítményének optimalizálása érdekében.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 58ecf22fa0f9349a767455fe3ab08fca058d02da
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="phoenix-performance-best-practices"></a>A Phoenix teljesítményével kapcsolatos ajánlott eljárások

A legfontosabb Phoenix teljesítmény célja az alapul szolgáló HBase optimalizálása érdekében. Phoenix létrehoz egy relációs adatmodell, amely az SQL-lekérdezések alakítja át a HBase műveletek, például a vizsgálatok HBase felett. A következő tábla sémáját, a kijelölés, valamint az elsődleges kulcs, és az összes indexek használata a mezők sorrendje Phoenix teljesítményét.

## <a name="table-schema-design"></a>Táblatervezés séma

Phoenix egy táblát hoz létre, a tábla tárolja a HBase táblában. A HBase táblában az oszlopok (oszlopcsaláddal), amelyek együtt érhetők el csoportot tartalmaz. A Phoenix tábla sorának egy sort a HBase táblában, ahol minden egyes sorára áll, verzióval ellátott cellák társított egy vagy több oszlop. A HBase egysoros logikailag, azonos rowkey értékkel rendelkező kulcs-érték párok gyűjteménye. Ez azt jelenti, hogy minden kulcs-érték párt rowkey attribútummal rendelkezik, és adott rowkey attribútum értéke megegyezik a sorok.

A séma kialakítás Phoenix táblázat tartalmazza az elsődleges kulcs tervezési, oszlop termékcsalád tervezési, egyedi oszlop tervezési és az adatok particionálásáról.

### <a name="primary-key-design"></a>Elsődleges tervezési

A Phoenix egy olyan táblázatában megadva elsődleges kulcs határozza meg, hogyan tárolja az alapul szolgáló HBase tábla rowkey belül. A HBase az egyetlen lehetőség sorok eléréséhez a rowkey jelenti. Emellett a HBase táblában tárolt adatokat a rowkey szerint van rendezve. Phoenix rowkey értéke szereplő értékeit sorban, a sorrendben vannak definiálva elsődleges kulcs oszlopainak mindegyike hoz létre.

Például a tábla a névjegyek rendelkezik, az Utónév, utolsó nevét, telefonszám és cím, az azonos oszlop termékcsalád összes. Egy elsődleges kulcs feladatütemezési egyre több alapján adható meg:

|rowkey|       Cím|   Telefonszám| Utónév| Vezetéknév|
|------|--------------------|--------------|-------------|--------------|
|  1000|San Gabriel Dr 1111.|1-425-000-0002|    Jakab|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

Azonban ha Vezetéknév szerint gyakran kérdezze le az elsődleges kulcs nem hajthatja végre, mert minden egyes lekérdezés egy teljes táblázatbeolvasás minden Vezetéknév értékének olvasásához szükséges. Egy elsődleges kulcs meg ehelyett a Vezetéknév, az utónév és a társadalombiztosítási szám oszlopok. Ez az utolsó oszlop, a félreérthetőség megszüntetéséhez két lakosai ugyanazzal a névvel, például egy édesapja és fia azonos címen.

|rowkey|       Cím|   Telefonszám| Utónév| Vezetéknév| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|San Gabriel Dr 1111.|1-425-000-0002|    Jakab|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Ez az új elsődleges kulcs a sort a Phoenix által létrehozott kulcsok néz ki:

|rowkey|       Cím|   Telefonszám| Utónév| Vezetéknév| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|San Gabriel Dr 1111.|1-425-000-0002|    Jakab|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

A fenti első sorban a rowkey adatai képviselt látható módon:

|rowkey|       kulcs|   érték| 
|------|--------------------|---|
|  Dole-John-111|Cím |San Gabriel Dr 1111.|  
|  Dole-John-111|Telefonszám |1-425-000-0002|  
|  Dole-John-111|Utónév |Jakab|  
|  Dole-John-111|Vezetéknév |Dole|  
|  Dole-John-111|socialSecurityNum |111| 

A rowkey tárolja az adatokat egy példányát. Fontolja meg a méretét és az elsődleges kulcs szerepeltetni kívánt oszlopok számát, mert ez az érték megtalálható minden cella az alapul szolgáló HBase táblában.

Is, ha az elsődleges kulcs értékeket, amelyeket a rendszer monoton módon növekvő, készítsen a tábla *gyűjtők sója* írási elérési pontokhoz való létrehozása elkerülése érdekében – tekintse meg a [adatok particionálása](#partition-data).

### <a name="column-family-design"></a>Oszlop termékcsalád kialakítása

Ha valamelyik oszlop gyakrabban mások érhetők el, a ritkábban használt oszlopok elválasztásához ritkán használt oszlop több oszlopcsaláddal kell létrehoznia.

Is ha az egyes oszlopok az egyes együttesen érhető el, helyezze el ezek az oszlopok egy oszlop család.

### <a name="column-design"></a>Oszlop kialakítása

* VARCHAR oszlopok alatt körülbelül 1 MB az i/o-költségek miatt nagy oszlopok megtartása. Lekérdezések feldolgozásakor HBase teljes cellák megvalósul, mielőtt az ügyfél keresztül elküldi őket, és az ügyfél megkapja azokat a teljes átadás a őket az alkalmazáskódban előtt.
* Kompakt formátumban, például a protobuf, Avro, msgpack vagy BSON oszlopok értékeinek tárolására. JSON nem ajánlott, mivel a mérete.
* Fontolja meg a tárolási késés és i/o-költségek kivágni előtt az adatok tömörítése.

### <a name="partition-data"></a>Partícióadatok

Phoenix teszi szabályozzák, hogy az adatok terjesztési helyének, régiók, ami jelentősen növelheti a olvasási/írási teljesítmény. A Phoenix tábla létrehozásakor sója, vagy előre ossza fel az adatokat.

Egy tábla só létrehozása során, adja meg a védőérték gyűjtők száma:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

A sózás felosztja a tábla elsődleges kulcsok, az értékek automatikusan kiválasztása értékek mentén. 

Szabályozására, amelyen a tábla elágazást történik, előre fel a tábla azáltal, hogy a tartományértékeknek, amely mentén a felosztás következik be. Például tábla létrehozása vágási mentén három régiók:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Indexek tervezése

A Phoenix indexe egy HBase tábla, vagy azok egy részét a indexelt táblából az adatok másolatát tárolja. Az index javítja a teljesítményt lekérdezések bizonyos típusú.

Ha több indexet és majd a tábla lekérdezése, Phoenix automatikusan kiválasztja a lekérdezés az ajánlott index. Az elsődleges index választja az elsődleges kulcsok alapján automatikusan jön létre.

Várható lekérdezések esetén is létrehozhat másodlagos indexek az oszlopok megadásával.

Az indexek létrehozása során:

* A szükséges indexek csak létrehozása.
* Gyakran frissített táblák indexei számának korlátozása. Egy táblázaton jelenti azt, hogy a fő táblázat és az index táblák írási műveletek.

## <a name="create-secondary-indexes"></a>Másodlagos indexek létrehozása

Másodlagos indexek olvassa el a jobb teljesítmény érdekében mi lenne egy pont keresési tárhelyet használ a teljes táblázatbeolvasás kell, és írási sebesség. Másodlagos indexek lehet hozzáadni, vagy eltávolítja a tábla létrehozása után, és nem szükséges módosítania meglévő lekérdezéseit a – lekérdezések csak gyorsabbá. Igényeitől függően célszerű kezelt indexek, működési indexeket, vagy mindkettőt.

### <a name="use-covered-indexes"></a>Az érintett indexek használata

Az érintett indexek rendszer mellett a értékek indexelt sor adatai indexeket. Miután megtalálta a kívánt indexbejegyzése, nincs szükség az elsődleges tábla eléréséhez.

A példában például létrehozhat egy másodlagos index csak a socialSecurityNum oszlop a tábla forduljon. A másodlagos index volna felgyorsítása socialSecurityNum szűrés lekérdezések, de más mezők értékének beolvasása szükséges egy másik olvassa el a fő táblázaton.

|rowkey|       Cím|   Telefonszám| Utónév| Vezetéknév| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|San Gabriel Dr 1111.|1-425-000-0002|    Jakab|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Azonban ha általában szeretné megkeresni a Keresztnév és Vezetéknév a socialSecurityNum megadott, létrehozhat egy kezelt indexet, amely tartalmazza az utónév és Vezetéknév tényleges adatként az index táblázatban:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

A kezelt index lehetővé teszi, hogy a következő lekérdezés az összes adatainak megszerzése úgy, ha a másodlagos indexet tartalmazó tábla olvasásakor:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Funkcionális indexek használata

Működési indexek engedélyezi, hogy a lekérdezésekben használható várhatóan tetszőleges kifejezés indexet létrehozni. Miután működési index már működik a, és egy lekérdezést, hogy kifejezést használ, az index a adattábla helyett az eredmények lekérése is használható.

Például létrehozhatja lehetővé teszik a kombinált Utónév és a személy vezetéknevét nem betűérzékeny keresések index:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Lekérdezéstervezés

A fő lekérdezés tervezési szempontok a következők:

* Ismerje meg a lekérdezésterv, és ellenőrizze a normális működés.
* Csatlakoztassa a hatékony.

### <a name="understand-the-query-plan"></a>A lekérdezésterv ismertetése

A [SQLLine](http://sqlline.sourceforge.net/), használja az SQL-lekérdezés követ magyarázat a tervet, amelyek végrehajtják a Phoenix műveletek. Ellenőrizze, hogy a terv:

* Adott esetben az elsődleges kulcsot használja.
* Használja a megfelelő másodlagos indexek ahelyett, hogy a tábla.
* TARTOMÁNY vizsgálata vagy KIHAGYÁSA vizsgálat, amikor csak lehetséges, helyett használja tábla vizsgálata.

#### <a name="plan-examples"></a>Példák megtervezése

Tegyük fel ki, repülési késési információkat tároló járatok nevű tábla.

Jelölje be a egy airlineid az összes repülőút `19805`, ahol a airlineid, amely nem az elsődleges kulcs vagy az index mező:

    select * from "FLIGHTS" where airlineid = '19805';

Futtassa a magyarázat parancs a következőképpen:

    explain select * from "FLIGHTS" where airlineid = '19805';

A lekérdezésterv így néz ki:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

A terv jegyezze fel a teljes vizsgálat keresztül járatok kifejezés helyett szerepel. Ezt a kifejezést jelzi, hogy a végrehajtási does ellenőrzése a tábla összes sorát a táblázatban, nem pedig a hatékonyabb tartomány ellenőrzési és a SKIP vizsgálati beállítással keresztül.

Most tegyük fel például, 2014. január 2. a szolgáltatói a lekérdezendő repülőútra `AA` 1-nél nagyobb volt, ahol a flightnum. Tegyük fel, hogy az oszlopok év, hónap, dayofmonth, vivőjel és flightnum szerepel a példában a táblázatban, és az összetett elsődleges kulcs összes részét képezik. A lekérdezés a következőképpen nézne ki:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Vizsgáljuk meg ehhez a lekérdezéshez a terv:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Az eredményül kapott terv van:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

A szögletes zárójelek értékei értékek az elsődleges kulcs. Ebben az esetben a tartományértékeknek 2014 év, hónap 1 és dayofmonth 2 fix, de lehetővé teszi értékek flightnum indítása 2 és a (`*`). A lekérdezésterv megerősíti, hogy az elsődleges kulcs használatban van, a várt.

Ezután hozzon létre egy index nevű járatok táblán `carrier2_idx` a csak a vivőjel mező. Ezt az indexet tartalmaz, amelynek adatait tárolja az index az érintett oszlop flightdate, tailnum, eredet és flightnum is.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Tegyük fel például, le szeretné kérdezni a szolgáltatói flightdate és tailnum, ahogy az alábbi lekérdezést:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Használja ezt az indexet kell megjelennie:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

Megjelenhet elemek teljes listáját terv eredmények ismertetik, a magyarázó tervek című a [Apache Phoenix hangolása útmutató](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Csatlakozás hatékonyan

Általában szeretné elkerülni az illesztések, kivéve, ha egy kiszolgálóoldali kicsi, különösen a gyakori lekérdezések.

Ha szükséges, akkor nagy csatlakozások a `/*+ USE_SORT_MERGE_JOIN */` mutatót, de nagy illesztés egy drága művelet keresztül hatalmas mennyiségű sort. Ha a jobb oldali mellett táblák teljes mérete meghaladná a rendelkezésre álló memória, használja a `/*+ NO_STAR_JOIN */` mutató.

## <a name="scenarios"></a>Forgatókönyvek

Az alábbi útmutatást néhány gyakori mintázatokat írja le.

### <a name="read-heavy-workloads"></a>Olvasási-gyakori feladatok

Az olvasási műveleteket használati esetekben, ellenőrizze, hogy indexeket használ. Emellett időmegtakarítás olvasás-terhelés, érdemes lehet létrehozni az érintett indexek.

### <a name="write-heavy-workloads"></a>Írási műveleteket munkaterhelések

Írási műveleteket munkaterhelések esetén, ahol az elsődleges kulcs monoton módon növekvő hozzon létre védőérték gyűjtők írási elérési pontokhoz való, csökkenti a teljes olvasási teljesítményt miatt a szükséges további vizsgálatok elkerülése érdekében. Is ha UPSERT írni a rekordok nagy száma, kapcsolja ki a Végrehajtású és a rekordok kötegelt.

### <a name="bulk-deletes"></a>Tömeges törlése

A nagy adatkészletet törlésekor bekapcsolása Végrehajtású DELETE lekérdezés kiadása előtt, hogy az ügyfélnek nincs szüksége jegyezze meg a sor kulcsok minden törölt soraihoz. Végrehajtású megakadályozza az ügyfél a sorok, a törlés hatással úgy, hogy a Phoenix törölhetők közvetlenül a régió kiszolgálók nélkül a adja vissza az ügyfélnek a pufferelés.

### <a name="immutable-and-append-only"></a>Nem módosítható és fűzi

A forgatókönyv írási sebesség adatintegritás előnyben részesítése szemben, ha érdemes a írási keresési naplót a táblák létrehozásakor:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Erről és más beállítások részletekért lásd: [Phoenix nyelvtan](http://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>További lépések

* [Phoenix hangolása útmutató](https://phoenix.apache.org/tuning_guide.html)
* [Másodlagos indexek](http://phoenix.apache.org/secondary_indexing.html)
