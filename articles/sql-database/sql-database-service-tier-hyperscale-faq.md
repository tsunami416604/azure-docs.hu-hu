---
title: Azure SQL Database nagy kapacitású – GYIK | Microsoft Docs
description: Válaszok a gyakori kérdésekre az ügyfelek a nagy kapacitású szolgáltatási rétegében – általában nagy kapacitású-adatbázis néven – egy Azure SQL Database-adatbázist kérnek.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/06/2019
ms.openlocfilehash: 8c35877c7de2fa89a8fe7a94c11787814183df9e
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162257"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Az Azure SQL nagy kapacitású-adatbázisokkal kapcsolatos gyakori kérdések

Ez a cikk a Azure SQL Database nagy kapacitású szolgáltatási szintjében (általában nagy kapacitású-adatbázis néven) található adatbázist fontolgató ügyfelekre vonatkozó gyakori kérdésekre adott válaszokat tartalmazza. Ez a cikk azokat a forgatókönyveket ismerteti, amelyeket a nagy kapacitású támogat, és a több funkciós szolgáltatásokat általában a SQL Database nagy kapacitású kompatibilisek.

- Ez a gyakori kérdések olyan olvasók számára készültek, akik röviden megértették a nagy kapacitású szolgáltatási szintet, és arra keresnek, hogy konkrét kérdéseiket és szempontjaikat is megválaszolják.
- Ez a GYIK nem azt jelenti, hogy egy SQL Database nagy kapacitású-adatbázis használatával kapcsolatos kérdéseit nem lehet útikönyvként megválaszolni. Ezért javasoljuk, hogy tekintse át a [Azure SQL Database nagy kapacitású](sql-database-service-tier-hyperscale.md) dokumentációját.

## <a name="general-questions"></a>Általános kérdések

### <a name="what-is-a-hyperscale-database"></a>Mi az a nagy kapacitású-adatbázis?

A nagy kapacitású-adatbázis egy Azure SQL Database-adatbázis a nagy kapacitású szolgáltatási rétegében, amelyet a nagy kapacitású kibővíthető tárolási technológiája támogat. A nagy kapacitású-adatbázisok akár 100 TB-os adatmennyiséget is támogatnak, és magas átviteli sebességet és teljesítményt biztosítanak, valamint gyors méretezést tesznek lehetővé a számítási feladatok követelményeinek megfelelően. A skálázás átlátható az alkalmazás – a kapcsolatok, a lekérdezés-feldolgozás stb. – esetében, hasonlóan más SQL-adatbázisokhoz.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Milyen típusú erőforrástípusok és beszerzési modellek támogatják a nagy kapacitású-t

A nagy kapacitású szolgáltatási réteg csak az önálló adatbázisok esetében érhető el a Azure SQL Database virtuális mag-alapú vásárlási modelljét használva.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Miben különbözik a nagy kapacitású szolgáltatási szintjei a általános célú és üzletileg kritikus szolgáltatási szintjeitől

A virtuális mag-alapú szolgáltatási rétegek elsődlegesen a rendelkezésre állás, a tárolási típus és a IOPs alapján különböztethetők meg.

- A általános célú szolgáltatási szintje a legtöbb üzleti számítási feladathoz megfelelő, és a számítási és tárolási lehetőségek kiegyensúlyozott készletét kínálja, ahol az IO-késés vagy a feladatátvételi idő nem a prioritás.
- A nagy kapacitású szolgáltatási szintje nagy mennyiségű adatbázis-számítási feladatra van optimalizálva.
- A üzletileg kritikus szolgáltatási szintje olyan üzleti számítási feladatokhoz megfelelő, ahol az IO-késés prioritás.

| | Erőforrás típusa | Általános rendeltetés |  Rugalmas skálázás | Üzleti szempontból kulcsfontosságú |
|:---:|:---:|:---:|:---:|:---:|
| **Legjobb a következőhöz:** |Összes|  A legtöbb üzleti számítási feladat. A költségvetés-orientált kiegyensúlyozott számítási és tárolási lehetőségeket kínál. | Nagy adatkapacitási követelményekkel rendelkező adatalkalmazások, valamint a tárterület automatikus méretezésének és méretezésének képessége. | OLTP alkalmazások nagy tranzakciós sebességgel és a legalacsonyabb késleltetésű IO-val. A maximális rugalmasságot nyújt a hibákhoz több, elkülönített replika használatával.|
|  **Erőforrás típusa** ||Önálló adatbázis/rugalmas készlet/felügyelt példány | Önálló adatbázis | Önálló adatbázis/rugalmas készlet/felügyelt példány |
| **Számítási méret**|Önálló adatbázis/rugalmas készlet * | 1 – 80 virtuális mag | 1 – 80 virtuális mag * | 1 – 80 virtuális mag |
| |Felügyelt példány | 8, 16, 24, 32, 40, 64, 80 virtuális mag | – | 8, 16, 24, 32, 40, 64, 80 virtuális mag |
| **Tárolás típusa** | Összes |Prémium szintű távoli tárterület (/példány) | A leválasztott tárterület helyi SSD-gyorsítótárral (/példány) | Villámgyors helyi SSD-tároló (példány) |
| **Tárterület mérete** | Önálló adatbázis/rugalmas készlet | 5 GB – 4 TB | Akár 100 TB | 5 GB – 4 TB |
| | Felügyelt példány  | 32 GB – 8 TB | – | 32 GB – 4 TB |
| **IO-átviteli sebesség** | Önálló adatbázis * * | 500 IOPS/virtuális mag 7000 maximális IOPS | A nagy kapacitású egy többrétegű architektúra, több szinten történő gyorsítótárazással. A hatékony IOPs a munkaterheléstől függ. | 5000 IOPS 200 000 maximális IOPS|
| | Felügyelt példány | A fájl méretétől függ | – | Felügyelt példány: A fájl méretétől függ|
|**Rendelkezésre állás**|Összes|1 replika, nincs olvasási méretezés, nincs helyi gyorsítótár | Több replika, legfeljebb 4 olvasási méretezés, részleges helyi gyorsítótár | 3 replika, 1 olvasási méretezés, zóna – redundáns HA, teljes helyi gyorsítótár |
|**Mentések**|Összes|RA-GRS, 7-35 nap (alapértelmezés szerint 7 nap)| RA-GRS, 7 nap, állandó időpontra történő helyreállítás (PITR) | RA-GRS, 7-35 nap (alapértelmezés szerint 7 nap) |

\*A nagy kapacitású szolgáltatási szintje nem támogatja a rugalmas készleteket

### <a name="who-should-use-the-hyperscale-service-tier"></a>Kinek érdemes a nagy kapacitású szolgáltatási szintet használnia

Az nagy kapacitású szolgáltatási réteg elsődlegesen olyan ügyfelek számára készült, akik nagy helyszíni SQL Server adatbázisokkal rendelkeznek, és szeretnék modernizálni alkalmazásaikat a felhőre való áttéréssel, illetve a már használatban lévő Azure SQL Database és a jelentős kibővítéshez az adatbázis növekedésének lehetséges lehetőségei. A nagy kapacitású olyan ügyfelek számára is ajánlott, akik nagy teljesítményt és magas skálázhatóságot keresnek. A nagy kapacitású a következőket kapja:

- Akár 100 TB-os adatbázis-méret támogatása
- Gyors adatbázisok biztonsági mentése az adatbázis méretétől függetlenül (a biztonsági másolatok fájl-Pillanatképek alapján)
- Gyors adatbázis-visszaállítás az adatbázis méretétől függetlenül (a visszaállítja a fájlok pillanatképeit)
- A magasabb szintű naplózási teljesítmény a gyors tranzakció-végrehajtási időt eredményezi az adatbázis méretétől függetlenül
- Az olvasási munkaterhelés kiosztása és a gyors készenléti állapot érdekében olvassa el a méretezést egy vagy több írásvédett csomópontra.
- A számítási feladatok gyors vertikális felskálázása állandó idő alatt, hogy hatékonyabb legyen a nehéz munkaterhelésnek megfelelően alkalmazkodni, majd az állandó idő alatt lekicsinyíthető legyen. Ez hasonló a P6 és a P11 közötti vertikális felskálázáshoz, például sokkal gyorsabb, mivel ez nem az adatműveletek mérete.

### <a name="what-regions-currently-support-hyperscale"></a>A jelenleg támogatott régiók nagy kapacitású

A Azure SQL Database nagy kapacitású szintje jelenleg a [Azure SQL Database nagy kapacitású áttekintése](sql-database-service-tier-hyperscale.md#regions)területen felsorolt régiókban érhető el.

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Létrehozhatok több nagy kapacitású-adatbázist egy logikai kiszolgálón

Igen. A logikai kiszolgálók nagy kapacitású-adatbázisainak számával kapcsolatos további információkért és korlátokért tekintse meg a [logikai kiszolgálókon található önálló és készletezett adatbázisok SQL Database erőforrás-korlátozásait](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>A nagy kapacitású-adatbázisok teljesítményének jellemzői

A SQL Database nagy kapacitású architektúra nagy teljesítményt és teljesítményt biztosít a nagyméretű adatbázisok méretének támogatása mellett. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>A nagy kapacitású-adatbázisok skálázhatósága

SQL Database a nagy kapacitású a számítási feladatok igénye alapján gyors skálázhatóságot biztosít.

- **Felfelé/lefelé skálázás**

  A nagy kapacitású segítségével az elsődleges számítási méretet az erőforrások, például a processzor, a memória, és az állandó idő szerinti skálázás alapján méretezheti fel. Mivel a tárterület meg van osztva, a skálázás és a skálázás nem az adatművelet mérete.  
- **Méretezés be/ki**

  A nagy kapacitású lehetővé teszi egy vagy több további számítási csomópont kiépítését, amelyeket az olvasási kérések kiszolgálására használhat. Ez azt jelenti, hogy ezekkel a további számítási csomópontokkal írásvédett csomópontként használhatja az olvasási munkaterhelés kiszervezését az elsődleges számításból. A csak olvasható adatok mellett ezek a csomópontok az elsődleges feladatátvétel esetén is a gyors készenléti állapotot is szolgálják.

  Ezen további számítási csomópontok kiépítés állandó időben és online művelettel végezhető el. Ehhez a további írásvédett számítási csomópontokhoz is csatlakozhat, ha a `ApplicationIntent` kapcsolati `readonly`karakterlánc argumentumát a értékre állítja. A rendszer automatikusan átirányítja a (val `readonly` ) jelölésű kapcsolatokat a további írásvédett számítási csomópontok egyikére.

## <a name="deep-dive-questions"></a>Részletes kérdések

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Használhatok nagy kapacitású és önálló adatbázisokat egyetlen logikai kiszolgálón.

Igen.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>A nagy kapacitású meg kell változtatni az alkalmazás programozási modelljét

Nem, az alkalmazás programozási modellje a következőképpen marad. A kapcsolódási karakterláncot a szokásos módon, a többi normál mód használatával is használhatja az Azure SQL Database-adatbázissal való kommunikációhoz.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Milyen tranzakció-elkülönítési szintek lesznek alapértelmezettek SQL Database nagy kapacitású-adatbázison

Az elsődleges csomóponton a tranzakció elkülönítési szintje RCSI (olvasási véglegesített pillanatkép-elkülönítés). Az olvasási skála másodlagos csomópontjain az elkülönítési szint pillanatkép.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>Használhatom a helyszíni vagy IaaS SQL Server-licencet SQL Database nagy kapacitású

Igen, [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) elérhető a nagy kapacitású számára. Minden SQL Server Standard mag 1 nagy kapacitású-virtuális mag képezhető le. Minden SQL Server Enterprise mag 4 nagy kapacitású-virtuális mag képezhető le. Másodlagos replikák esetén nem szükséges SQL-licenc. A rendszer automatikusan alkalmazza a Azure Hybrid Benefit árát az olvasási skálára (másodlagos) vonatkozó replikára.

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>Milyen számítási feladatokat SQL Database nagy kapacitású terveztek

SQL Database a nagy kapacitású támogatja az összes SQL Server munkaterhelést, de elsődlegesen a OLTP-re van optimalizálva. A hibrid (HTAP) és az analitikus (data mart) számítási feladatokat is használhatja.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Hogyan választhatok Azure SQL Data Warehouse és SQL Database nagy kapacitású

Ha jelenleg interaktív elemzési lekérdezéseket futtat SQL Server adattárházként, SQL Database a nagy kapacitású nagyszerű lehetőség, mert viszonylag kis adattárházat (például néhány TB-ot akár 10 TB-ot) is üzemeltet, és az adatraktárat is áttelepítheti a arehouse számítási feladatait T-SQL-nagy kapacitású nélkül SQL Database.

Ha az adatelemzést nagyméretű, összetett lekérdezésekkel és párhuzamos adatraktárral (PDW), Teradata vagy más, nagymértékben párhuzamos processzor (MPP) adattárházak használatával futtatja, SQL Data Warehouse lehet a legjobb választás.
  
## <a name="sql-database-hyperscale-compute-questions"></a>SQL Database nagy kapacitású számítási kérdései

### <a name="can-i-pause-my-compute-at-any-time"></a>Bármikor szüneteltethető a számításom

Jelenleg azonban a számítások és a replikák számának csökkentése érdekében a költségeket a nem csúcsidőben is csökkentheti.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>Létrehozhatok egy extra RAM-mal rendelkező számítási feladatot a nagy memória-igényű munkaterheléshez

Nem. A RAM további mennyiségének megkezdéséhez magasabb számítási méretre kell frissítenie. További információ: [nagy kapacitású Storage és számítási méretek](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-for-provisioned-compute).

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>Több különböző méretű számítási csomópont is kiépíthető

Nem.

### <a name="how-many-read-scale-replicas-are-supported"></a>Hány olvasási léptékű replika támogatott

Alapértelmezés szerint a nagy kapacitású-adatbázisok egyetlen olvasási léptékű replikával (összesen két replikával) jönnek létre. Az írásvédett replikák számát a [Azure Portal](https://portal.azure.com), a [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), a [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) vagy a [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)használatával méretezheti át 0 és 4 között.

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>A magas rendelkezésre állás érdekében további számítási csomópontokat kell kiépíteni

A nagy kapacitású-adatbázisokban a rugalmasságot a tárolási szinten biztosítjuk. Csak egy replikára van szükség a rugalmasság biztosításához. Ha a számítási replika nem érhető el, a rendszer automatikusan létrehoz egy új replikát adatvesztés nélkül.

Ha azonban csak egy replika van, eltarthat egy ideig, hogy a feladatátvételt követően létrejöjjön a helyi gyorsítótár az új replikában. A gyorsítótár-újraépítési fázisban az adatbázis közvetlenül az oldalról kérdezi le az adatforrásokat, így csökkentve a IOPS és a lekérdezési teljesítményt.

A magas rendelkezésre állást igénylő, kritikus fontosságú alkalmazások esetében legalább 2 számítási csomópontot kell kiépíteni, beleértve az elsődleges számítási csomópontot (alapértelmezett). Így a feladatátvétel esetén rendelkezésre áll egy gyors készenléti állapot.

## <a name="data-size-and-storage-questions"></a>Az adatméretre és a tárolásra vonatkozó kérdések

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>Mi a SQL Database nagy kapacitású által támogatott maximális adatbázis-méret

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Mi a nagy kapacitású rendelkező tranzakciónapló mérete

A nagy kapacitású és a tranzakciós napló gyakorlatilag végtelen. Nem kell aggódnia a naplózási területnek a nagy adatátviteli sebességű rendszeren való futtatásával. A log generálási arány azonban a folyamatos agresszív számítási feladatokhoz is szabályozható. A folyamatos, a napló generálásának maximális aránya körülbelül 100 MB/s.

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>A saját adatbázis-méretezési szolgáltatásom egyre növekszik

Az `tempdb` adatbázis a helyi SSD-tárolón található, és a kiépített számítási méret alapján van konfigurálva. A `tempdb` szolgáltatás optimalizált és elvégezhető, hogy maximális teljesítménybeli előnyöket biztosítson. A `tempdb` méret nem konfigurálható, és a tároló alrendszere kezeli.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>Az adatbázis méretének automatikus növekedése vagy az adatfájlok méretének kezelése

Az adatbázis mérete automatikusan növekszik, amikor több adatot szúr be/tölt be.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>Mi az az adatbázis legkisebb mérete, amelyet SQL Database nagy kapacitású támogat vagy a

10 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>Milyen növekményekben növekszik az adatbázis mérete

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>A tároló SQL Database helyi vagy távoli nagy kapacitású

A nagy kapacitású-ben az adatfájlok tárolása az Azure standard Storage szolgáltatásban történik. Az adatok nagy mértékben gyorsítótárazva vannak a helyi SSD-tárolón, a számítási csomópontokhoz közeledő gépeken. Emellett a számítási csomópontok gyorsítótárral rendelkeznek a helyi SSD-meghajtón és a memóriában (a pufferben stb.) a távoli csomópontokból érkező adatok lekérésének gyakoriságának csökkentése érdekében.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Kezelhetem vagy Megadhatom a fájlokat vagy fájlcsoportok a nagy kapacitású használatával

Nem
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Létrehozhatok-e szigorú korlátot az adatbázis adatnövekedéséhez

Nem

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Hogyan történik az adatfájlok megállapítása SQL Database nagy kapacitású

Az adatfájlokat az oldal-kiszolgálók vezérlik. Az adatméret növekedésével az adatfájlok és a hozzájuk kapcsolódó kiszolgálóoldali csomópontok is felvehetők.

### <a name="is-database-shrink-supported"></a>Támogatott-e az adatbázis-zsugorodás

Nem

### <a name="is-database-compression-supported"></a>Az adatbázis-tömörítés támogatott

Igen

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Ha óriási táblázattal rendelkezem, a táblázat adatai több adatfájlba kerülnek

Igen. Egy adott táblához tartozó adatlapokon több adatfájl is végződhet, amelyek mindegyike azonos fájlcsoportja tartozik. Az SQL Server egy [arányos kitöltési stratégiát](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) használ az adatfájlokon keresztüli adatelosztásra.

## <a name="data-migration-questions"></a>Az adatáttelepítés kérdései

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Áthelyezhetem a meglévő Azure SQL-adatbázisokat a nagy kapacitású szolgáltatási szintjére

Igen. A meglévő Azure SQL-adatbázisokat áthelyezheti nagy kapacitású. Ez egy egyirányú áttelepítés. Az adatbázisok nem helyezhetők át a nagy kapacitású egy másik szolgáltatási szintjére. Javasoljuk, hogy készítsen másolatot az éles adatbázisokról, és váltson át a nagy kapacitású-re a fogalmak igazolására (Pócs).
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>Áthelyezhetem a nagy kapacitású-adatbázisokat más kiadásokra

Nem. Jelenleg nem helyezhető át egy nagy kapacitású-adatbázis egy másik szolgáltatási szintjére.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Elveszítem a nagy kapacitású szolgáltatási szintjére való Migrálás után elérhető funkciókat és képességeket

Igen. Néhány Azure SQL Database funkció még nem támogatott a nagy kapacitású-ben, beleértve a hosszú távú adatmegőrzési biztonsági mentést is. Miután áttelepítette az adatbázisokat a nagy kapacitású, ezek a funkciók nem működnek.  Elvárjuk, hogy ezek a korlátozások ideiglenesek legyenek.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>Áthelyezhetem a helyszíni SQL Server adatbázist vagy a SQL Server virtuálisgép-adatbázist a nagy kapacitású

Igen. Az összes meglévő áttelepítési technológiát használhatja a nagy kapacitású való áttelepítéshez, beleértve a BACPAC, a tranzakciós replikációt, a logikai betöltést. Lásd még a [Azure Database Migration Service](../dms/dms-overview.md).

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Milyen állásidő van a helyszíni vagy virtuálisgép-környezetből a nagy kapacitású-re való Migrálás során, és hogyan lehet azt csökkenteni

Az állásidő ugyanaz, mint a leállás, amikor az adatbázisait egyetlen adatbázisba telepíti át Azure SQL Databaseban. A tranzakciós replikálással](replication-to-sql-database.md#data-migration-scenario
) csökkentheti az adatbázisok leállási áttelepítését akár néhány TB-os méretig. [ A nagy méretű adatbázisok (10 + TB) esetében érdemes lehet az ADF-, Spark-vagy más adatáthelyezési technológiák használatával áttelepíteni az adatátvitelt.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Mennyi ideig tart, hogy X mennyiségű adattal SQL Database nagy kapacitású

A nagy kapacitású képes az új/módosított adatmennyiség 100 MB/s sebességű felhasználására.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Beolvasható az adatok a blob Storage-ból, és gyors betöltés (például a Base és a SQL Data Warehouse)

Beolvashatja az Azure Storage-ból származó adatait, és betöltheti az adatterhelést egy nagy kapacitású-adatbázisba (ugyanúgy, mint egy normál önálló adatbázis esetében). A (z) Azure SQL Database jelenleg nem támogatja a következőt: Base. Az [SQL-hez készült Spark-összekötővel](sql-database-spark-connector.md) [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) vagy Spark-feladatot futtathat a [Azure Databricksban](https://docs.microsoft.com/azure/azure-databricks/) . A Spark-összekötő az SQL-hez támogatja a tömeges beszúrást.

Az egyszerű helyreállítás vagy a tömeges naplózási modell nem támogatott a nagy kapacitású. A magas rendelkezésre állás biztosításához teljes helyreállítási modell szükséges. A nagy kapacitású azonban az új naplózási architektúra miatt egy adott Azure SQL Database-adatbázishoz képest jobb adatfeldolgozási sebességet biztosít.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>SQL Database nagy kapacitású lehetővé teszi több csomópont kiépítés nagy mennyiségű adatot.

Nem. SQL Database nagy kapacitású egy SMP architektúra, és nem aszimmetrikus többprocesszoros vagy több főkiszolgálós architektúra. Csak a csak olvasási feladatok skálázásához több replikát is létrehozhat.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>A legrégebbi SQL Server verziója SQL Database nagy kapacitású-támogatás áttelepítését

SQL Server 2005. További információ: [áttelepítés önálló adatbázisra vagy készletezett adatbázisra](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Kompatibilitási problémák esetén tekintse meg az [adatbázis-áttelepítési kompatibilitási problémák megoldását](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues)ismertető témakört.

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>A SQL Database nagy kapacitású támogatja az áttelepítést más adatforrásokból, például az Aurora, a MySQL, az Oracle, a DB2 és más adatbázis-platformokból

Igen. A SQL Servertól eltérő adatforrásokból érkező logikai áttelepítés szükséges. A logikai áttelepítéshez használhatja a [Azure Database Migration Service](../dms/dms-overview.md) .

## <a name="business-continuity-and-disaster-recovery-questions"></a>Üzletmenet-folytonossági és vész-helyreállítási kérdések

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Milyen SLA-kat biztosítanak egy nagy kapacitású-adatbázishoz

Az alapértelmezett elsődleges és 1 olvasható másodlagos értékkel az SLA 99,95%-os rendelkezésre állást biztosít.  Több replikával az SLA akár 99,99%-ot is meghaladhat.  

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Az adatbázis biztonsági másolatait a Azure SQL Database szolgáltatás felügyeli

Igen

### <a name="how-often-are-the-database-backups-taken"></a>Az adatbázis biztonsági másolatainak gyakorisága

A SQL Database nagy kapacitású adatbázisok esetében nincsenek hagyományos teljes, különbözeti és naplózott biztonsági másolatok. Ehelyett az adatfájlok és a létrehozott naplófájlok rendszeres pillanatképei egyszerűen megmaradnak a konfigurált vagy elérhető megőrzési időszakra vonatkozóan.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>SQL Database nagy kapacitású-támogatás időpontjának visszaállítása

Igen

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>Mi a helyreállítási időkorlát (RPO)/Recovery időcélkitűzése (RTO) a Backup/Restore SQL Database nagy kapacitású

A RPO 0 perc. Az RTO cél kevesebb, mint 10 perc, az adatbázis méretétől függetlenül. 

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>A nagyméretű adatbázisok biztonsági másolatai hatással vannak a számítási teljesítményre az elsődlegesen

Nem. A biztonsági mentéseket a tárolási alrendszer kezeli, és kihasználja a fájlok pillanatképeit. Nem érintik a felhasználói munkaterhelést az elsődlegesen.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>Végezhetek geo-visszaállítást SQL Database nagy kapacitású-adatbázissal

Igen.  A Geo-visszaállítás teljes mértékben támogatott.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>Beállítható a Geo-replikáció SQL Database nagy kapacitású-adatbázissal

Jelenleg nem.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>A másodlagos számítási csomópontok földrajzilag replikálódnak a SQL Database nagy kapacitású

Jelenleg nem.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>Készíthetek SQL Database nagy kapacitású-adatbázis biztonsági mentését, és Visszaállíthatom a helyszíni kiszolgálóra vagy SQL Server virtuális gépre

Nem. A nagy kapacitású-adatbázisok tárolási formátuma eltér a hagyományos SQL Servertól, és nem szabályozza a biztonsági mentéseket, és nem fér hozzájuk. Az adatSQL Database nagy kapacitású-adatbázisból való kijelentkezéshez használja az exportálási szolgáltatást, vagy használja a Scripting Plus BCP-t.

## <a name="cross-feature-questions"></a>Több funkcióval kapcsolatos kérdések

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Elveszítem a nagy kapacitású szolgáltatási szintjére való Migrálás után elérhető funkciókat és képességeket

Igen. Néhány Azure SQL Database funkció nem támogatott a nagy kapacitású-ben, beleértve a hosszú távú adatmegőrzési biztonsági mentést. Miután áttelepítette az adatbázisokat a nagy kapacitású, ezek a funkciók nem működnek.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>A SQL Database nagy kapacitású együtt fog működni

Nem. A Azure SQL Database nem támogatja a (z) alalapot.

### <a name="does-the-compute-have-support-for-r-and-python"></a>Támogatja a számítás az R és a Python használatát

Nem. Az R és a Python nem támogatott Azure SQL Databaseban.

### <a name="are-the-compute-nodes-containerized"></a>A tárolóban lévő számítási csomópontok

Nem. Az adatbázis egy számítási virtuális gépen található, nem pedig tároló.

## <a name="performance-questions"></a>Teljesítménnyel kapcsolatos kérdések

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Mekkora átviteli sebességet lehet leküldeni a legnagyobb SQL Database nagy kapacitású számítási feladatokhoz

Az adatváltozások (tranzakciós napló adatgenerálása) konzisztens 100 MB/s-nál nagyobb száma látható

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>Hány IOPS kapok a legnagyobb SQL Database nagy kapacitású számítási feladatokhoz

A IOPS és az IO-késés a munkaterhelés-mintáktól függően változhat.  Ha az elérni kívánt adatok helyiek a számítási gyorsítótárban, akkor a helyi SSD-ként megegyező IO-minták lesznek.   

### <a name="does-my-throughput-get-affected-by-backups"></a>A biztonsági másolatok hatással vannak a teljesítményre

Nem. A számítást a rendszer leválasztja a tárolási rétegből, hogy elkerülje a számítás hatását.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>Hatással van-e az átviteli sebesség a további számítási csomópontok kiépítésekor

Mivel a tárterület meg van osztva, és nincs közvetlen fizikai replikáció az elsődleges és a másodlagos számítási csomópontok között, technikailag az elsődleges csomópont átviteli sebessége nem lesz hatással az olvasási léptékű csomópontok hozzáadásával. Azonban a folyamatos agresszív számítási feladatok szabályozása lehetővé teheti, hogy a naplók a másodlagos csomópontokon és a kiszolgálókon is érvénybe lépjenek, és elkerülhető legyen a másodlagos csomópontok rossz olvasási teljesítménye.

## <a name="scalability-questions"></a>Skálázhatósági kérdések

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Mennyi időt vesz igénybe a számítási csomópontok vertikális fel-és leskálázása

A számítások felfelé vagy lefelé történő skálázása 5-10 percet vesz igénybe az adatok méretétől függetlenül.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Az adatbázis offline állapotban van, amíg folyamatban van a méretezés felfelé/lefelé művelete.

Nem. A felfelé és lefelé történő skálázás online állapotú lesz.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Ha a skálázási műveletek folyamatban vannak, a csatlakozás eldobása várható.

A fel-és leskálázás eredményeként a meglévő kapcsolatok el lettek dobva, amikor feladatátvétel történik a számítási csomóponton a célként megadott mérettel. Az olvasási replikák hozzáadása nem eredményezi a kapcsolatok elejtését.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>A számítási csomópontok automatikus vagy a végfelhasználó által aktivált műveletének méretezése

Végfelhasználó. Nem automatikus.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>A számítási felskálázással is növekszik `tempb`

Igen. A temp db automatikusan vertikális felskálázást végez, ahogy a számítás növekszik.  

### <a name="can-i-provision-multiple-primary-compute-nodes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Több elsődleges számítási csomópont is kiépíthető, például egy több főkiszolgálós rendszer, ahol több elsődleges számítási fej lehet magasabb szintű párhuzamosságot vezetni

Nem. Csak az elsődleges számítási csomópont fogad írási/olvasási kérelmeket. A másodlagos számítási csomópontok csak olvasási kérelmeket fogadnak el.

## <a name="read-scale-questions"></a>Méretezési kérdések olvasása

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Hány másodlagos számítási csomópontot lehet kiépíteni

Alapértelmezés szerint 2 replikát hozunk létre a nagy kapacitású-adatbázisokhoz. Ha módosítani szeretné a replikák számát, [Azure Portal](https://portal.azure.com)használatával teheti meg.

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Hogyan kapcsolódás ezekhez a másodlagos számítási csomópontokhoz

Ehhez a további írásvédett számítási csomópontokhoz is csatlakozhat, ha a `ApplicationIntent` kapcsolati `readonly`karakterlánc argumentumát a értékre állítja. A rendszer automatikusan átirányítja a (val `readonly` ) jelölésű kapcsolatokat a további írásvédett számítási csomópontok egyikére.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-node-using-ssms--other-client-tools"></a>Hogyan ellenőrizni, hogy sikerült-e csatlakozni a másodlagos számítási csomóponthoz a SSMS/más ügyféleszközök használatával?

A következő T-SQL-lekérdezést hajthatja végre a SSMS/other Client Tools `SELECT DATABASEPROPERTYEX ( '<database_name>' , 'updateability' )`használatával:.
Az eredmény az `READ_ONLY` , ha a Kapcsolódás a csak olvasási jogosultsággal rendelkező másodlagos csomópontra mutat `READ_WRITE` , vagy ha a Kapcsolódás az elsődleges csomópontra mutat.

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>Létrehozhatok egy dedikált végpontot az olvasási léptékű replikához

Nem. Csak olvasási léptékű replikához csatlakozhat `ApplicationIntent=ReadOnly`. ehhez a következőt kell megadnia:.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Az olvasási feladat intelligens terheléselosztása a rendszeren történik

Nem. A csak olvasási feladatot átirányítja egy véletlenszerű olvasási léptékű replikára.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>A másodlagos számítási csomópontok az elsődleges számítástól függetlenül méretezhetők és leállíthatók

Nem. A másodlagos számítási csomópontokat is használja a rendszer, így a feladatátvétel esetén az elsődlegesnek azonos konfigurációnak kell lennie.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Az elsődleges számítási és a további másodlagos számítási csomópontok esetében eltérő ideiglenes adatbázis-méretezést kapok

Nem. A beállítása a számítási méret kiépítés alapján történik,amásodlagosszámításicsomópontokméretemegegyezikazelsődlegesszámításimérettel.`tempdb`

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>Hozzáadhatok indexeket és nézeteket a másodlagos számítási csomópontokon

Nem. A nagy kapacitású-adatbázisok megosztott tárolóval rendelkeznek, ami azt jelenti, hogy minden számítási csomópont ugyanazokat a táblákat, indexeket és nézeteket látja. Ha további indexeket szeretne használni a másodlagos olvasásra, előbb fel kell vennie azokat az elsődlegesre.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Mennyi késleltetéssel kell számolni az elsődleges és a másodlagos számítási csomópont között

Attól függően, hogy a rendszer mikor véglegesíti a tranzakciót az elsődlegesen, a napló generálási arányának megfelelően lehet pillanatnyi vagy alacsony ezredmásodpercben.

## <a name="next-steps"></a>További lépések

További információ a nagy kapacitású szolgáltatási szintjéről: [nagy kapacitású szolgáltatási szintje](sql-database-service-tier-hyperscale.md).
