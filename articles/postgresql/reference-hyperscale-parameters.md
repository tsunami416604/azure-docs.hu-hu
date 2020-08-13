---
title: Kiszolgálói paraméterek – nagy kapacitású (nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Paraméterek a nagy kapacitású (Citus) SQL API-ban
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 57258540f3cd7b4c47b662b0885453cedd188e5b
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136836"
---
# <a name="server-parameters"></a>Kiszolgálóparaméterek

A nagy kapacitású viselkedését (Citus), a standard PostgreSQL-ből és a nagy kapacitású (Citus) jellemző paramétereket is befolyásolja különböző kiszolgálói paraméterek. Ha többet szeretne megtudni a PostgreSQL konfigurációs paraméterekről, látogasson el a PostgreSQL-dokumentáció [futtatási idő konfiguráció](http://www.postgresql.org/docs/current/static/runtime-config.html) szakaszára.

A hivatkozás további része a nagy kapacitású (Citus) adott konfigurációs paramétereinek megvitatására irányul. Ezek a paraméterek beállíthatók a Azure Portal nagy kapacitású (Citus) kiszolgálói csoportjának **Beállítások** területén a **munkavégző csomópont paramétereinek** területén.

> [!NOTE]
>
> Előfordulhat, hogy a Citus motor régebbi verzióit futtató nagy kapacitású-kiszolgálócsoportok nem rendelkeznek az alább felsorolt paraméterekkel.

## <a name="general-configuration"></a>Általános konfiguráció

### <a name="citususe_secondary_nodes-enum"></a>citus. \_ másodlagos \_ csomópontok használata (Enum)

Beállítja a kiválasztott lekérdezésekhez tartozó csomópontok kiválasztásakor használandó házirendet. Ha a "mindig" értékre van állítva, akkor a Planner csak a "másodlagos" noderole jelölt csomópontokat kérdezi le [pg_dist_nodeban](reference-hyperscale-metadata.md#worker-node-table).

A felsorolás támogatott értékei a következők:

-   **soha:** (alapértelmezés) az összes olvasás az elsődleges csomópontokon történik.
-   **mindig:** Az olvasás a másodlagos csomópontokon történik, és az INSERT/Update utasítások le vannak tiltva.

### <a name="cituscluster_name-text"></a>citus. cluster \_ neve (szöveg)

Tájékoztatja a koordinátori csomópont-Plannert, amely a fürt koordinálja. A fürt \_ nevének beállítása után a Planner önállóan fogja lekérdezni a munkavégző csomópontokat a fürtben.

### <a name="citusenable_version_checks-boolean"></a>citus. Enable \_ verzió \_ -ellenőrzések (logikai)

A nagy kapacitású (Citus) verziójának frissítéséhez a kiszolgáló újraindítása szükséges (az új megosztott könyvtár felvételéhez), majd az ALTER EXTENSION UPDATE paranccsal.  A két lépés végrehajtásának elmulasztása hibát okozhat vagy összeomlik.
A nagy kapacitású (Citus) így ellenőrzi a kód verzióját és a kiterjesztés egyezését, és ha nem, akkor hibákra \' .

Ez az érték alapértelmezés szerint igaz, és a koordinátoron érvényes. Ritka esetekben előfordulhat, hogy az összetett frissítési folyamatokhoz a paraméter hamis értékre állítása szükséges, ami letiltja az ellenőrzési eljárást.

### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus. log \_ elosztott \_ holtpont \_ észlelése (logikai)

Azt határozza meg, hogy naplózza-e az elosztott holtpont észlelésével kapcsolatos feldolgozást a kiszolgálói naplóban. Az alapértelmezett érték a False (hamis).

### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus. Distributed \_ holtpont \_ észlelési \_ tényező (lebegőpontos)

Az elosztott holtpontok ellenőrzése előtti várakozási idő beállítása. Különösen a várakozási idő lesz az érték, és a PostgreSQL \' s holtpont- [ \_ időtúllépési](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) beállítása szorzata. Az alapértelmezett érték `2`. Egy érték `-1` letiltja az elosztott holtpont észlelését.

### <a name="citusnode_connection_timeout-integer"></a>citus. Node \_ kapcsolat \_ időkorlátja (egész szám)

A `citus.node_connection_timeout` GUC beállítja a kapcsolatok létesítésére való várakozás maximális időtartamát (ezredmásodpercben). A nagy kapacitású (Citus) hibát jelez, ha az időtúllépés legalább egy feldolgozói kapcsolat létrehozása előtt eltelik. Ez a GUC a koordinátor és a dolgozók közötti kapcsolatokat is befolyásolja.

-   Alapértelmezett: öt másodperc
-   Minimum: 10 ezredmásodperc
-   Maximum: egy óra

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

## <a name="query-statistics"></a>Lekérdezési statisztikák

### <a name="citusstat_statements_purge_interval-integer"></a>citus. stat \_ utasítások \_ kiürítési \_ időköze (egész szám)

Beállítja, hogy a karbantartási démon milyen gyakorisággal távolítsa el a nem egyező [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) rekordokat `pg_stat_statements` . Ez a konfigurációs érték a kiürítések közötti időintervallumot állítja be másodpercben, alapértelmezett érték: 10. A 0 érték letiltja a kiürítéseket.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Ez a paraméter a koordinátoron érvényes, és futásidőben is módosítható.

## <a name="data-loading"></a>Betöltés

### <a name="citusmulti_shard_commit_protocol-enum"></a>citus. multi-szegmens \_ \_ véglegesítő \_ protokoll (Enum)

Beállítja azt a commit protokollt, amelyet a rendszer akkor használ, amikor kivonatot terjesztenek az elosztott táblázatba. Az egyes szegmensek elhelyezésekor a másolás tranzakciós blokkban történik annak biztosítása érdekében, hogy a másolás során hiba esetén a rendszer ne végezzen adatot. Van azonban egy olyan meghibásodási eset, amelyben a másolás minden helyen sikeres, de a (hardveres) hiba az összes tranzakció véglegesíte előtt történik. Ezzel a paraméterrel megelőzhető az adatvesztés ebben az esetben, ha a következő véglegesítő protokollok közül választ:

-   **2PC:** (alapértelmezett) az a tranzakció, amelyben a rendszer a szegmens elhelyezéseken a másolást végzi, először a PostgreSQL \' s [kétfázisú véglegesítés](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) , majd a véglegesítés használatával készül el. A sikertelen véglegesítés manuálisan helyreállítható vagy megszakítható a VÉGLEGESÍTett előkészített vagy VISSZAÁLLÍTÁSi művelettel.
    A 2PC használatakor a [maximálisan \_ előkészített \_ tranzakciókat](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) az összes feldolgozóra vonatkozóan növelni kell, jellemzően a maximális \_ kapcsolatokkal azonos értékre.
-   **1db:** Azokat a tranzakciókat, amelyekben a rendszer a szegmensek szerinti elhelyezéseken végzi a MÁSOLÁSt, egyetlen fordulóban véglegesíti. Előfordulhat, hogy az adatvesztés meghiúsul, ha a másolás minden Elhelyezéskor sikeres volt (ritka).

### <a name="citusshard_replication_factor-integer"></a>citus. szilánk \_ replikációs \_ tényező (egész szám)

Beállítja a replikálási tényezőt a szegmensek esetében, azon csomópontok számát, amelyeken a szegmensek lesznek elhelyezve, és az alapértelmezett érték az 1. Ez a paraméter futásidőben állítható be, és a koordinátorra is érvényes. A paraméter ideális értéke a fürt méretétől és a csomópont meghibásodási sebességtől függ.  Ha például nagyméretű fürtöket futtat, és gyakrabban szeretné megfigyelni a csomópontok hibáit, érdemes lehet ezt a replikációs tényezőt megnövelni.

### <a name="citusshard_count-integer"></a>citus. szilánkok \_ száma (egész szám)

Beállítja a kivonatoló particionálású táblák és az alapértelmezett értékek 32-re vonatkozó szegmensének darabszámát.  Ezt az értéket a [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF használja a kivonatoló particionálású táblák létrehozásakor. Ez a paraméter futásidőben állítható be, és a koordinátorra is érvényes.

### <a name="citusshard_max_size-integer"></a>citus. szilánk \_ maximális \_ mérete (egész szám)

Megadja azt a maximális méretet, ameddig a szegmens növekedni fog, és az alapértelmezett érték 1 GB lesz. Ha az egyik szegmensnél a forrásfájl \' s mérete (amely átmeneti tároláshoz használatos) meghaladja ezt a konfigurációs értéket, az adatbázis biztosítja, hogy az új szegmens létre lesz hozva. Ez a paraméter futásidőben állítható be, és a koordinátorra is érvényes.

## <a name="planner-configuration"></a>Planner konfigurálása

### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus. limit \_ záradék \_ \_ -sorok beolvasásának \_ száma (egész szám)

Beállítja a lekérdezni kívánt sorok számát a limit záradék optimalizálásához.
Bizonyos esetekben a limit záradékokkal rendelkező lekérdezések esetében előfordulhat, hogy az egyes feladatokból származó összes sort be kell olvasnia az eredmények létrehozásához. Ezekben az esetekben, és ha egy közelítés értelmes eredményeket eredményezne, ez a konfigurációs érték beállítja az egyes szegmensek lekéréséhez szükséges sorok számát. A határérték-közelítések alapértelmezés szerint le vannak tiltva, és ez a paraméter a-1 értékre van beállítva. Ez az érték futásidőben állítható be, és a koordinátorra is érvényes.

### <a name="cituscount_distinct_error_rate-floating-point"></a>citus. Count \_ \_ – eltérő hibák száma \_ (lebegőpontos)

A nagy kapacitású (Citus) a PostgreSQL-HLL kiterjesztés használatával kiszámíthatja a darabszámot (eltérő). Ez a konfigurációs bejegyzés beállítja a várt hibák arányát a darabszám kiszámításakor (eltérő). 0,0, amely az alapértelmezett, letiltja a darabszám (eltérő) közelítését. és a 1,0 nem biztosít garanciát az eredmények pontosságára vonatkozóan. Javasoljuk, hogy ezt a paramétert 0,005 értékre állítsa a legjobb eredmények érdekében. Ez az érték futásidőben állítható be, és a koordinátorra is érvényes.

### <a name="citustask_assignment_policy-enum"></a>citus. feladat \_ -hozzárendelési \_ házirend (Enum)

> [!NOTE]
> Ez a GUC csak akkor alkalmazható, ha a [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) nagyobb, mint egy, vagy [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables)a lekérdezésekre.

A feladatok feladatainak a feladatokhoz való hozzárendeléséhez használandó házirend beállítása. A koordinátor a feladatokat a feladatokhoz a szegmens helyei alapján rendeli hozzá. Ez a konfigurációs érték határozza meg a hozzárendelések végrehajtásakor használandó házirendet.
Jelenleg három lehetséges feladat-hozzárendelési házirend használható.

-   **kapzsi:** A kapzsi házirend az alapértelmezett, és célja, hogy egyenletesen ossza el a feladatokat a feldolgozók között.
-   **ciklikus multiplexelés:** A ciklikus időszeleteléses szabályzat a feladatokat a feldolgozók számára a különböző replikák közötti, ciklikusan megjelenő módon rendeli hozzá. Ez a szabályzat lehetővé teszi a fürt jobb kihasználtságát, ha a tábla szegmensek száma nem elég alacsony a feldolgozók számához képest.
-   **első replika:** Az első replika házirend a szegmensekhez tartozó elhelyezési sorrend (replika) alapján rendel el feladatokat. Más szóval a szegmenshez tartozó töredék-lekérdezés a szegmens első replikáját tartalmazó feldolgozóhoz van rendelve.
    Ez a módszer lehetővé teszi, hogy erős garanciát biztosítson arra vonatkozóan, hogy mely szegmenseket fogja használni a csomópontokon (azaz erősebb memória-rezidensi garancia).

Ez a paraméter futásidőben állítható be, és a koordinátorra is érvényes.

## <a name="intermediate-data-transfer"></a>Köztes Adatátvitel

### <a name="citusbinary_worker_copy_format-boolean"></a>citus. Binary \_ Worker \_ másolási \_ formátuma (logikai)

A bináris másolási formátum használatával továbbíthatja a köztes adatok feldolgozók közötti átvitelét.
A nagyméretű táblákhoz való csatlakozás során előfordulhat, hogy a nagy kapacitású (Citus) dinamikusan újra kell particionálni és meg kell keverni az adatmennyiséget a különböző munkatársak között Alapértelmezés szerint az adatátvitelt szöveges formátumban kell megadni. A paraméter engedélyezése arra utasítja az adatbázist, hogy a PostgreSQL bináris szerializálási formátumát használja az adatok átviteléhez. Ez a paraméter a feldolgozókon érvényes, és a PostgreSQL. conf fájlban kell módosítani. A konfigurációs fájl szerkesztése után a felhasználók SIGHUP-jelet küldhetnek, vagy újraindíthatják a kiszolgálót a módosítás érvénybe léptetéséhez.

### <a name="citusbinary_master_copy_format-boolean"></a>citus. bináris \_ fő \_ másolási \_ formátum (logikai)

A bináris másolási formátum használatával továbbíthatja az adatok koordinátor és a munkavégzők között. Az elosztott lekérdezések futtatásakor a dolgozók az időközi eredményeket a végső összesítéshez továbbítják a koordinátornak. Alapértelmezés szerint az adatátvitelt szöveges formátumban kell megadni. A paraméter engedélyezése arra utasítja az adatbázist, hogy a PostgreSQL bináris szerializálási formátumát használja az adatok átviteléhez.
Ez a paraméter beállítható futásidőben, és a koordinátoron is érvényes.

### <a name="citusmax_intermediate_result_size-integer"></a>citus. max \_ közbenső \_ eredmény \_ mérete (egész szám)

A közös táblakifejezés közbenső eredményeinek maximális mérete (KB), amelyet nem lehet leküldeni a feldolgozó csomópontokra a végrehajtáshoz, valamint az összetett allekérdezésekhez. Az alapértelmezett érték 1 GB, a-1 érték pedig nem korlátot jelent.
A korlátot meghaladó lekérdezések meg lesznek szakítva, és hibaüzenetet hoznak létre.

## <a name="ddl"></a>DDL

### <a name="citusenable_ddl_propagation-boolean"></a>citus. Enable \_ DDL \_ -propagálás (logikai)

Meghatározza, hogy a rendszer automatikusan propagálja-e a DDL-módosításokat a koordinátortól az összes feldolgozóig. Az alapértelmezett érték az igaz. Mivel egyes sémák módosításainak kizárólagos hozzáférésre van szükségük a táblákhoz, és mivel az automatikus propagálás az összes feldolgozóra vonatkozik, a nagy kapacitású (Citus) fürt átmenetileg kevésbé rugalmasan reagál. Dönthet úgy, hogy letiltja ezt a beállítást, és manuálisan propagálja a módosításokat.

## <a name="executor-configuration"></a>Végrehajtó konfigurációja

### <a name="general"></a>Általános kérdések

#### <a name="citusall_modifications_commutative"></a>citus. minden \_ módosítás \_ kommutatív

A nagy kapacitású (Citus) kikényszeríti a commutativity-szabályokat, és a megfelelő zárolásokat szerzi be a módosítási műveletekhez a viselkedés javítása érdekében. Tegyük fel például, hogy egy INSERT utasítás egy másik INSERT utasítással, de UPDATE vagy DELETE utasítással nem. Hasonlóképpen azt is feltételezi, hogy egy frissítési vagy TÖRLÉSi utasítás nem egy másik UPDATE vagy DELETE utasítással van felcserélve. Ez az elővigyázatosság azt jelenti, hogy a frissítésekhez és törlésekhez nagy kapacitású (Citus) szükséges az erősebb zárolások beolvasásához.

Ha olyan frissítési utasításokkal rendelkezik, amelyek a lapkákkal vagy más frissítésekkel kommutatív, akkor a paraméter igaz értékre állításával kihasználhatja ezeket a commutativity feltételezéseket. Ha a paraméter értéke TRUE (igaz), az összes parancs kommutatív minősül, és megosztott zárolást igényel, amely javíthatja a teljes átviteli sebességet. Ez a paraméter beállítható futásidőben, és a koordinátoron is érvényes.

#### <a name="citusremote_task_check_interval-integer"></a>citus. távoli \_ feladatok \_ ellenőrzési \_ időköze (egész szám)

Meghatározza, hogy a nagy kapacitású (Citus) milyen gyakorisággal ellenőrizze a feladat-követési végrehajtó által kezelt feladatok állapotát. Alapértelmezés szerint 10 ms. A koordinátor feladatokat rendel a feldolgozókhoz, majd rendszeresen ellenőrzi azokat az egyes feladatok \' előrehaladásával kapcsolatban. Ez a konfigurációs érték állítja be két következő ellenőrzés közötti időtartamot. Ez a paraméter a koordinátoron érvényes, és beállítható futásidőben.

#### <a name="citustask_executor_type-enum"></a>citus. Task \_ végrehajtó \_ típusa (Enum)

A nagy kapacitású (Citus) három végrehajtói típussal rendelkezik az elosztott VÁLASZTÓ lekérdezések futtatásához.  A kívánt végrehajtót a konfigurációs paraméter beállításával lehet kiválasztani. A paraméter elfogadott értékei a következők:

-   **adaptív:** Az alapértelmezett érték. A több szegmens között átfedésben lévő összesítéseket és közös elhelyezésű illesztéseket tartalmazó lekérdezések gyors megválaszolásához optimális megoldás.
-   **feladat-követés:** A Feladatkezelő végrehajtója jól használható a hosszú ideig futó, összetett lekérdezésekhez, amelyek az adatok keverését igénylik a munkavégző csomópontokon és a hatékony erőforrás-kezelésben.
-   **valós idejű:** (elavult) az adaptív végrehajtóhoz hasonló célt szolgál, de kevésbé rugalmas, és a munkavégző csomópontok számára nagyobb hálózati nyomást eredményezhet.

Ez a paraméter futásidőben állítható be, és a koordinátorra is érvényes.

#### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus. \_ többfeladatos \_ lekérdezés \_ naplózási \_ szintje (Enum) {#multi_task_logging}

Minden olyan lekérdezés naplózási szintjét állítja be, amely egynél több feladatot hoz létre (azaz több szegmenst üt). A naplózás a több-bérlős alkalmazások áttelepítése során hasznos, mivel választhatja a hibákat, vagy figyelmeztetheti ezeket a lekérdezéseket, így megkeresheti őket, és felvehet egy bérlői \_ azonosító szűrőt. Ez a paraméter beállítható futásidőben, és a koordinátoron is érvényes. A paraméter alapértelmezett értéke \' Off \' .

A felsorolás támogatott értékei a következők:

-   **kikapcsolva:** A több feladatot létrehozó lekérdezések naplózásának kikapcsolása (azaz több szegmensre is kiterjed)
-   **hibakeresés:** A naplók a hibakeresés súlyossági szintjének kimutatására szolgálnak.
-   **napló:** Naplózza az utasítást a napló súlyossági szintjén. A naplófájl tartalmazni fogja a futtatott SQL-lekérdezést.
-   **Figyelmeztetés:** A naplófájlok utasítás súlyossági szintje.
-   **Figyelmeztetés:** A naplók utasítás a figyelmeztetés súlyossági szintjén.
-   **hiba:** A naplók utasítása a hiba súlyossági szintjén.

Hasznos lehet a `error` fejlesztési tesztelés során használni, és alacsonyabb a naplózási szint, mint a `log` tényleges éles üzembe helyezés során.
Ha a lehetőséget választja, `log` akkor a több feladatot tartalmazó lekérdezések megjelennek az adatbázis naplófájljaiban az utasítás után megjelenő lekérdezéssel \" .\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

#### <a name="citusenable_repartition_joins-boolean"></a>citus. az \_ újraparticionálási \_ illesztések engedélyezése (logikai)

Általában az újraparticionálási illesztések az adaptív végrehajtóval való elvégzésére tett kísérlet során hibaüzenet jelenik meg.  A True érték megadása azonban lehetővé teszi, hogy a `citus.enable_repartition_joins` nagy kapacitású (Citus) átmenetileg átváltson a Task-Tracker-végrehajtóra az illesztés végrehajtásához.  Az alapértelmezett érték a hamis.

### <a name="task-tracker-executor-configuration"></a>Feladat-követési végrehajtó konfigurációja

#### <a name="citustask_tracker_delay-integer"></a>citus. a feladat \_ nyomon követésének \_ késleltetése (egész szám)

Ezzel a paraméterrel állítható be a Feladatkezelő alvó időpontja a feladatok kezelése és az alapértelmezett érték között a 200 MS. A feladat-követési folyamat rendszeresen felébred, az összes hozzá rendelt feladatra, valamint a feladatok ütemezett és végrehajtásához.  Ezután a feladat követése egy adott időszakra alvó állapotba lép, mielőtt újra elkezdené ezeket a feladatokat.
Ez a konfigurációs érték határozza meg az adott alvó időszak hosszát. Ez a paraméter a feldolgozókon érvényes, és a PostgreSQL. conf fájlban kell módosítani. A konfigurációs fájl szerkesztése után a felhasználók elküldhetnek egy SIGHUP-jelet, vagy újraindíthatják a kiszolgálót a módosítás érvénybe léptetéséhez.

Ez a paraméter csökkenthető a feladat-követési végrehajtó által okozott késleltetés levágásával a felügyeleti körök közötti időbeli eltérés csökkentése érdekében.
A késleltetés csökkentése olyan esetekben hasznos, amikor a szegmensben lekérdezett lekérdezések rövidek, és így rendszeresen frissítik az állapotukat.

#### <a name="citusmax_assign_task_batch_size-integer"></a>citus. max \_ hozzárendelési \_ feladat \_ kötegének \_ mérete (egész szám)

A koordinátor végrehajtója szinkron módon, a feldolgozókon lévő démonhoz rendel feladatokat a kötegekben. Ezzel a paraméterrel állítható be az egyetlen kötegben hozzárendelhető feladatok maximális száma. Ha nagyobb méretű köteget választ, a gyorsabb feladat-hozzárendelést is lehetővé teszi. Ha azonban a feldolgozók száma nagy, akkor hosszabb időt vehet igénybe az összes feldolgozó számára a feladatok beszerzése.  Ez a paraméter beállítható futásidőben, és a koordinátoron is érvényes.

#### <a name="citusmax_running_tasks_per_node-integer"></a>citus. max \_ futó \_ feladatok \_ / \_ csomópont (egész szám)

A feladat-követési folyamat az adott esetben hozzárendelt feladatokat ütemezze és hajtja végre. Ez a konfigurációs érték azt állítja be, hogy a tevékenységek legfeljebb hány feladatot hajtanak végre egyszerre egy csomóponton egy adott időpontban, és az alapértelmezett érték 8.

A korlát biztosítja, hogy ne \' legyen sok olyan feladat, amely egyszerre több lemezt is tartalmaz, és segít elkerülni a lemez I/O-tartalmát. Ha a lekérdezések a memóriából vagy az SSD-lemezekről vannak kiszolgálva, a \_ csomópontok maximális futási \_ feladatait a művelet \_ \_ nem érinti.

#### <a name="cituspartition_buffer_size-integer"></a>citus. partition \_ puffer \_ mérete (egész szám)

Beállítja a partíciós műveletekhez és az alapértelmezett értékekhez használt puffer méretét 8 MB-ra.
A nagy kapacitású (Citus) lehetővé teszi, hogy a tábla-adatmennyiség több fájlba legyen particionálva, ha két nagyméretű tábla van csatlakoztatva. A partíciós puffer betöltését követően az újraparticionált adatfájlok a lemezen lévő fájlokba kerülnek.  Ez a konfigurációs bejegyzés futásidőben állítható be, és hatékony a feldolgozók számára.

### <a name="explain-output"></a>Kimenet magyarázata

#### <a name="citusexplain_all_tasks-boolean"></a>citus. \_ az összes \_ feladat magyarázata (logikai)

Alapértelmezés szerint a nagy kapacitású (Citus) egyetlen, tetszőleges feladat kimenetét jeleníti meg, amikor egy elosztott lekérdezésen futtat [magyarázatot](http://www.postgresql.org/docs/current/static/sql-explain.html) . A legtöbb esetben a magyarázat kimenete a feladatok között hasonló lesz. Alkalmanként a tevékenységek némelyike másképp lesz megtervezve, vagy sokkal magasabb végrehajtási idővel rendelkezik. Ezekben az esetekben hasznos lehet ezt a paramétert engedélyezni, amely után a magyarázat kimenete tartalmazza az összes feladatot. Az összes feladat magyarázata miatt a magyarázat hosszabb időt is igénybe vehet.

## <a name="next-steps"></a>Következő lépések

* A konfiguráció egy másik formája a kiszolgálói paraméterek mellett egy nagy kapacitású-(Citus-) kiszolgálócsoport erőforrás- [konfigurációs beállításai](concepts-hyperscale-configuration-options.md) .
* Az alapul szolgáló PostgreSQL-adatbázis [konfigurációs paramétereket](http://www.postgresql.org/docs/current/static/runtime-config.html)is tartalmaz.
