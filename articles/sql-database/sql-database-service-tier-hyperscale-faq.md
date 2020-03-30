---
title: Azure SQL Database – – gyakori kérdések
description: Az ügyfelek gyakori kérdésekre adott válaszok egy Azure SQL-adatbázisról a Nagy kapacitású szolgáltatási rétegben – közismert nevén egy nagy kapacitású adatbázisról.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: 9f518df02b1923513fd014be53646a9a1be8465e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268625"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Azure SQL Database – – gyakori kérdések

Ez a cikk választ ad a gyakran feltett kérdésekre az ügyfelek számára, figyelembe véve az azure SQL Database nagy kapacitású szolgáltatási szint, a továbbiakban csak a nagy kapacitású a fennmaradó gyakori kérdések. Ez a cikk ismerteti a nagy kapacitású támogatja a nagy léptékű és a szolgáltatások, amelyek kompatibilisek a nagy kapacitású.

- Ez a GYIK azoknak az olvasóknak szól, akik röviden ismerik a nagy kapacitású szolgáltatási szintet, és konkrét kérdéseikre és aggályaikra választ kapnak.
- Ez a gyIK nem célja, hogy egy útikönyv, vagy válaszoljon a kérdésekre, hogyan kell használni a nagy méretű adatbázisok. A nagy kapacitású bevezetés, azt javasoljuk, hogy tekintse meg az [Azure SQL Database nagy kapacitású](sql-database-service-tier-hyperscale.md) dokumentációt.

## <a name="general-questions"></a>Általános kérdések

### <a name="what-is-a-hyperscale-database"></a>Mi az a nagy méretű adatbázis?

A nagy kapacitású adatbázis egy Azure SQL-adatbázis a nagy kapacitású szolgáltatási réteg, amely a nagy kapacitású kibővített tárolási technológia támogatja. A nagy kapacitású adatbázisok akár 100 TB-os adatszintet is támogatnak, és nagy átviteli és teljesítménybeli teljesítményt, valamint gyors skálázást biztosítanak a számítási feladatok követelményeihez való alkalmazkodásérdekében. A skálázás az alkalmazás számára transzparens – kapcsolat, lekérdezésfeldolgozás, stb munka, mint bármely más Azure SQL-adatbázis.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Milyen erőforrástípusok és beszerzési modellek támogatják a nagy kapacitást

The Hyperscale service tier is only available for single databases using the vCore-based purchasing model in Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Miben különbözik a nagy kapacitású szolgáltatási szint az általános célú és az üzleti legkritikusabb szolgáltatási szintektől?

A virtuálismag-alapú szolgáltatási szintek az adatbázis rendelkezésre állása és a tárolási típus, a teljesítmény és a maximális méret alapján vannak megkülönböztetve az alábbi táblázatban leírtak szerint.

| | Erőforrás típusa | Általános célú |  Rugalmas skálázás | Üzleti kritikus |
|:---:|:---:|:---:|:---:|:---:|
| **A legjobb** |Összes|Költségvetés-orientált kiegyensúlyozott számítási és tárolási lehetőségeket kínál.|A legtöbb üzleti számítási feladatok. Automatikus skálázás tárolási méret akár 100 TB, gyors függőleges és vízszintes számítási méretezés, gyors adatbázis-visszaállítás.|OLTP alkalmazások magas tranzakciós aránnyal és alacsony I/o késéssel. A hibákkal és a gyors feladatátvételekkel szembeni legnagyobb ellenálló képességet kínálja több szinkron módon frissített kópia használatával.|
|  **Erőforrás típusa** ||Egyetlen adatbázis / rugalmas készlet / felügyelt példány | Önálló adatbázis | Egyetlen adatbázis / rugalmas készlet / felügyelt példány |
| **Számítási méret**|Egyetlen adatbázis / rugalmas készlet * | 1–80 virtuális mag | 1–80 virtuális mag* | 1–80 virtuális mag |
| |Felügyelt példány | 8, 16, 24, 32, 40, 64, 80 virtuális mag | N/A | 8, 16, 24, 32, 40, 64, 80 virtuális mag |
| **Tárolás típusa** | Összes |Prémium szintű távtároló (példányonként) | Nem kapcsolt tároló helyi SSD-gyorsítótárral (példányonként) | Szupergyors helyi SSD-tároló (példányonként) |
| **Tárhely mérete** | Egyetlen adatbázis / rugalmas készlet *| 5 GB – 4 TB | Akár 100 TB | 5 GB – 4 TB |
| | Felügyelt példány  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **IOPS** | Önálló adatbázis | 500 IOPS virtuális magonként, 7000 maximális IOPS-értékkel | A nagykapacitás ú többszintű architektúra több szinten gyorsítótárazásával. A hatékony IOPS a munkaterheléstől függ. | 5000 IOPS 200 000 maximális IOPS-értékkel|
| | Felügyelt példány | A fájlméretétől függ | N/A | 1375 IOPS/virtuális mag |
|**Rendelkezésre állás**|Összes|1 replika, nincs olvasási horizontális felskálázás, nincs helyi gyorsítótár | Több kópia, legfeljebb 4 olvasási horizontális felskálázás, részleges helyi gyorsítótár | 3 replika, 1 Olvasási horizontális felskálázás, zónaredundáns HA, teljes helyi tároló |
|**Biztonsági másolatok**|Összes|RA-GRS, 7-35 napos megőrzés (alapértelmezés szerint 7 nap)| RA-GRS, 7 napos megőrzés, állandó időpont-időpont helyreállítás (PITR) | RA-GRS, 7-35 napos megőrzés (alapértelmezés szerint 7 nap) |

\*A rugalmas készletek nem támogatottak a nagy kapacitású szolgáltatási csomagban

### <a name="who-should-use-the-hyperscale-service-tier"></a>Ki használja a nagy kapacitású szolgáltatási szintet?

A nagy kapacitású szolgáltatási szint olyan ügyfelek számára készült, akik nagy helyszíni SQL Server-adatbázisokkal rendelkeznek, és a felhőbe való áthelyezéssel szeretnék korszerűsíteni alkalmazásaikat, vagy azoknak az ügyfeleknek, akik már használják az Azure SQL Database-t, és jelentősen ki szeretnék bővíteni a az adatbázisok növekedési potenciálja. A nagy kapacitású felhasználók számára is készült, akik nagy teljesítményt és nagy méretezhetőséget is keresnek. A Hyperscale, kapsz:

- Adatbázis mérete 100 TB-ig
- Az adatbázis gyors biztonsági mentései az adatbázis méretétől függetlenül (a biztonsági mentések a tárolási pillanatképeken alapulnak)
- Az adatbázis gyors visszaállítása az adatbázis méretétől függetlenül (a visszaállítások a tárolási pillanatképekből származnak)
- Nagyobb naplóátviteli-átmenő, függetlenül az adatbázis méretétől és a virtuális magok számától
- Olvassa el a Scale-out egy vagy több csak olvasható replikák, olvasási kiszervezéshez használt és a forró készenléti.
- A számítási feladatok gyors, állandó idő alatt való felskálázása, hogy nagyobb teljesítményű legyen a nagy munkaterhelés hez, majd folyamatosan csökkentse a számítást. Ez hasonló például a P6 és a P11 közötti fel- és leskálázáshoz, de sokkal gyorsabb, mivel ez nem az adatművelet mérete.

### <a name="what-regions-currently-support-hyperscale"></a>Jelenleg melyek támogatják a nagy kapacitást

A nagy kapacitású szolgáltatási szint jelenleg az [Azure SQL Database nagykapacitású áttekintése](sql-database-service-tier-hyperscale.md#regions)területen felsorolt régiókban érhető el.

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Létrehozhatok több nagy méretű adatbázist logikai kiszolgálónként

Igen. További információt és a logikai kiszolgálónkénti nagykapacitású adatbázisok számának korlátozását az SQL Database erőforráskorlátai egy [logikai kiszolgálón lévő egy- és készletezett adatbázisokra vonatkozóan](sql-database-resource-limits-logical-server.md)talál.

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>A nagy kapacitású adatbázisok teljesítményjellemzői

A nagy kapacitású architektúra nagy teljesítményt és átviteli teljesítményt biztosít, miközben támogatja a nagy adatbázisméreteket. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Mi a nagy méretűadatbázisok méretezhetősége?

A nagy kapacitás gyors méretezhetőséget biztosít a számítási feladatok igénye alapján.

- **Fel-fel/le méretezés**

  A Nagy kapacitással az elsődleges számítási méretet az erőforrások, például a CPU és a memória szempontjából, majd folyamatosan csökkentheti. Mivel a tároló meg van osztva, a felskálázás és a leskálázás nem az adatművelet mérete.  
- **Méretezés be- és kiméretezése**

  A Nagy kapacitással egy vagy több további számítási replikák, amelyek az olvasási kérelmek kiszolgálására is lehetővé teszi. Ez azt jelenti, hogy használhatja ezeket a további számítási replikák csak olvasható replikák az olvasási számítási feladatok az elsődleges számítási tehermentesíti. A mellett, hogy csak olvasható, ezek a replikák is szolgálnak a készenléti állapotban az elsődleges feladatátvétel esetén.

  Ezek a további számítási replikák kiépítése lehet tenni állandó idő alatt, és egy online művelet. Ezekhez a további írásvédett számítási kópiákhoz úgy csatlakozhat, hogy a `ApplicationIntent` kapcsolati karakterlánc argumentumát a-ra `ReadOnly`állítja. Az `ReadOnly` alkalmazásleképezéssel létesített kapcsolatok automatikusan átkerülnek a további írásvédett számítási replikák egyikéhez.

## <a name="deep-dive-questions"></a>Deep Dive kérdések

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Keverhetem a hiperskálás és az egyes adatbázisokat egyetlen logikai kiszolgálón

Igen.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>A nagy kapacitás ú megköveteli az alkalmazásprogramozási modell módosítását

Nem, az alkalmazásprogramozási modell változatlan marad. A kapcsolati karakterláncot a szokásos módon, valamint a nagy kapacitású adatbázissal való kommunikáció egyéb rendszeres módjait használja.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Milyen tranzakcióelkülönítési szint az alapértelmezett a nagy kapacitású adatbázisokban

Az elsődleges replika, az alapértelmezett tranzakció elkülönítési szint RCSI (Olvasás véglegesített pillanatkép elkülönítése). A Leolvasási horizontális felskálázás másodlagos replikák, az alapértelmezett elkülönítési szint Pillanatkép.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Eltudom hozni a helyszíni vagy iaaS SQL Server licencemet a Nagy kapacitású

Igen, az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) a Nagy kapacitású. Minden SQL Server Standard mag 1 nagy méretű virtuális magra képezhető le. Minden SQL Server Enterprise mag 4 nagy méretű virtuális magra képes leképezni. A másodlagos replikákhoz nincs szükség SQL-licencre. Az Azure Hybrid Benefit ár automatikusan alkalmazza a read scale-out (másodlagos) replikák.

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Milyen típusú számítási feladatok at Hiperscale tervezték

A nagy kapacitású kapacitással támogatja az összes SQL Server-számítási feladatot, de elsősorban oltp-ra van optimalizálva. Hibrid (HTAP) és analitikus (data mart) számítási feladatokat is hozhat.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>Hogyan választhatok az Azure SQL Data Warehouse és az Azure SQL Database Hyperscale között?

Ha jelenleg az SQL Server t adatraktárként használt interaktív elemzési lekérdezéseket futtat, a Nagy kapacitás egy nagyszerű lehetőség, mivel kis és közepes méretű adatraktárakat (például néhány TB-t akár 100 TB-ig) is üzemeltethet alacsonyabb költséggel, és áttelepítheti az SQL Server-adatokat. a raktári számítási feladatok at hyperscale minimális T-SQL kód változások.

Ha az adatelemzést összetett lekérdezésekkel és 100 MB/s-nál magasabb tartós betöltési sebességgel futtatja, vagy párhuzamos adattárház (PDW), Teradata vagy más massively parallel processing (MPP) adatraktárakat használ, az SQL Data Warehouse lehet a legjobb választás.
  
## <a name="hyperscale-compute-questions"></a>Nagy léptékű számítási kérdések

### <a name="can-i-pause-my-compute-at-any-time"></a>Bármikor szüneteltethetem a számítást

Nem ebben az időben, azonban a számítási és a replikák száma lefelé skálázható a költségek csökkentése érdekében nem csúcsidőben.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Kiépíthetek egy számítási replikát extra RAM memóriával a nagy memóriaigényű munkaterhelésemhez

Nem. Ahhoz, hogy több RAM-ot kapjon, nagyobb számítási méretre kell frissítenie. További információ: [Nagykapacitású tárolási és számítási méretek.](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Több különböző méretű számítási replikát is kiépíthetek

Nem.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Hány olvasási kibővített replikák támogatottak

A nagy kapacitású adatbázisok egy olvasási kibővített kópiával (két kópiával, beleértve az elsődlegest) jönnek létre. Az [Azure Portal](https://portal.azure.com) vagy a [REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)használatával 0 és 4 között méretezheti az írásvédett replikák számát.

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>A magas rendelkezésre állás érdekében további számítási replikákat kell kiépítenem

A nagy kapacitású adatbázisokban az adatok rugalmassága a tárolási szinten biztosított. A rugalmasság biztosításához csak egy replika szükséges. Ha a számítási kópia nem működik, egy új replika jön létre automatikusan adatvesztés nélkül.

Ha azonban csak egy replika van, eltarthat egy ideig, amíg a feladatátvétel után létrehozza a helyi gyorsítótárat az új kópiában. A gyorsítótár-újraépítési fázisban az adatbázis közvetlenül a lapkiszolgálókról olvassa le az adatokat, ami nagyobb tárolási késést és a lekérdezés idotartamának csökkenését eredményezi.

Az alapvető fontosságú alkalmazások, amelyek megkövetelik a magas rendelkezésre állás minimális feladatátvételi hatás, legalább 2 számítási replikák, beleértve az elsődleges számítási replika. Ez az alapértelmezett beállítás. Így van egy készenléti replikát, amely feladatátvételi célként szolgál.

## <a name="data-size-and-storage-questions"></a>Adatmérettel és tárolással kapcsolatos kérdések

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Mi a nagy kapacitással támogatott maximális adatbázisméret?

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Mekkora a tranzakciós napló a nagy méretezésű

A tranzakciós napló a nagy kapacitással gyakorlatilag végtelen. Nem kell aggódnia, hogy elfogy a naplóterület egy olyan rendszeren, amely nagy naplóátviteli sebességgel rendelkezik. A napló generálási sebessége azonban folyamatos, agresszív számítási feladatok írása esetén szabályozható. A tartós naplógenerálási csúcs 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>Az `tempdb` adatbázis növekedésével növekszik a skálázás

Az `tempdb` adatbázis a helyi SSD-tárolón található, és a kiosztott számítási méretnek megfelelően van méretezve. Az `tempdb` Ön úgy van optimalizálva, hogy maximális teljesítményelőnyöket biztosítson. `tempdb`a méret nem konfigurálható, és az Ön számára kezelhető.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>Az adatbázis mérete automatikusan nő, vagy kezelnem kell az adatfájlok méretét

Az adatbázis mérete automatikusan növekszik, ahogy több adatot szúr be/bead.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Mi az a legkisebb adatbázisméret, amelyet a Nagykapacitás támogat, vagy amely

40 GB. A nagy kapacitású adatbázis 10 GB-os kezdőmérettel jön létre. Ezután 10 percenként 10 GB-tal növekszik, amíg el nem éri a 40 GB-os méretet. A 10 GB-os tokmány mindegyike egy másik oldalkiszolgálón van lefoglalva, hogy több IOPS-t és magasabb I/O-párhuzamosságot biztosítson. Az optimalizálás miatt, még akkor is, ha a kezdeti adatbázisméretet 40 GB-nál kisebbnek választja, az adatbázis automatikusan legalább 40 GB-ra nő.

### <a name="in-what-increments-does-my-database-size-grow"></a>Milyen lépésekben nő az adatbázis mérete

Minden adatfájl 10 GB-tal nő. Egyszerre több adatfájl is növekedhet.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>A tároló a nagy kapacitású helyi vagy távoli

A Nagy kapacitású adatfájlok az Azure standard szintű tárolójában tárolódnak. Az adatok teljes mértékben gyorsítótárazva vannak a helyi SSD-tárolón, a számítási replikákhoz közel lévő lapkiszolgálókon. Emellett a számítási replikák adatgyorsítótárakkal rendelkeznek a helyi SSD-n és a memóriában, hogy csökkentsék az adatok távoli lapkiszolgálókról történő beolvasásának gyakoriságát.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Kezelhetek vagy definiálhatok fájlokat vagy fájlcsoportokat a Nagyméretezés segítségével

Nem. Az adatfájlok hozzáadása automatikusan megtörténik. A további fájlcsoportok létrehozásának gyakori okai nem érvényesek a nagy kapacitású tárolási architektúrában.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Kiépíthetek egy szigorú korlátot az adatbázis adatainak növekedésére vonatkozóan

Nem.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Hogyan készülnek az adatfájlok a Nagykapacitással?

Az adatfájlokat oldalkiszolgálók vezérlik, adatfájlonként egy oldalkiszolgálóval. Az adatméret növekedésével az adatfájlok és a kapcsolódó oldalkiszolgálók is hozzáadódnak.

### <a name="is-database-shrink-supported"></a>Támogatott-e az adatbázis zsugorítása

Nem.

### <a name="is-data-compression-supported"></a>Támogatott-e az adattömörítés?

Igen, beleértve a sor-, oldal- és oszlopcentrikus tömörítést.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Ha hatalmas táblám van, a táblázatadataim több adatfájlközött is eloszlanak

Igen. Az adott táblához társított adatlapok több adatfájlba is bekerülhetnek, amelyek mind ugyanannak a fájlcsoportnak a részét képezik. Az SQL Server [arányos kitöltési stratégiát](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) alkalmaz az adatok adatfájlokon keresztüli elosztására.

## <a name="data-migration-questions"></a>Adatáttelepítéssel kapcsolatos kérdések

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Áthelyezhetem a meglévő Azure SQL-adatbázisaimat a Nagykapacitású szolgáltatási szintre

Igen. A meglévő Azure SQL-adatbázisok at nagy kapacitásra helyezheti át. Ez egyirányú migráció. Az adatbázisok nem helyezhető át a nagy kapacitású egy másik szolgáltatási szintre. A koncepció igazolása (POCs), azt javasoljuk, hogy készítsen másolatot az adatbázisról, és telepítse át a másolatot a nagy kapacitású.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Áthelyezhetem a nagy kapacitású adatbázisokat más szolgáltatási szintekre

Nem. Jelenleg nem helyezhet át egy nagy kapacitású adatbázist egy másik szolgáltatási szintre.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Elveszítek-e bármilyen funkciót vagy képességet a Nagykapacitású szolgáltatási szintre való áttelepítés után?

Igen. Az Azure SQL Database egyes funkciói még nem támogatottak a Nagy kapacitású szolgáltatásokban, beleértve, de nem kizárólagosan a hosszú távú biztonsági mentés megőrzését. Miután áttelepítette az adatbázisokat a nagy kapacitásra, ezek a szolgáltatások nem működnek.  Elvárjuk, hogy ezek a korlátozások ideiglenesek legyenek.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Áthelyezhetem a helyszíni SQL Server-adatbázisomat vagy az SQL Server-adatbázisomat egy felhőalapú virtuális gépre a nagy kapacitású

Igen. Az összes meglévő áttelepítési technológia segítségével áttelepítheti a nagy kapacitású, beleértve a tranzakciós replikáció, és minden más adatáthelyezési technológiák (tömeges másolása, Azure Data Factory, Azure Databricks, SSIS). Lásd még: az [Azure Database Migration Service](../dms/dms-overview.md), amely számos áttelepítési forgatókönyvet támogat.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Mi az állásidő a helyszíni vagy virtuálisgép-környezetből a nagy kapacitású környezetbe való áttelepítés során, és hogyan minimalizálhatom azt?

A nagy kapacitásra való áttelepítés állásideje megegyezik az adatbázis-áttelepítések orido-k más Azure SQL Database szolgáltatási szintekre való áttelepítésekor. A [tranzakciós replikáció](replication-to-sql-database.md#data-migration-scenario
) segítségével minimalizálhatja az akár néhány TB méretű adatbázisok állásidő-áttelepítését. Nagyon nagy adatbázisok (10+ TB) esetén érdemes lehet adatokat áttelepíteni Az ADF, Spark vagy más adatmozgatási technológiák használatával.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Mennyi időbe kerülne, hogy x mennyiségű adatot vigyük be a hiperskálás

A nagy kapacitás 100 MB/s új/módosított adat használatára képes, de az Azure SQL-adatbázisokba való áthelyezéshez szükséges időt befolyásolja a rendelkezésre álló hálózati átviteli sebesség, a forrásolvasási sebesség és a céladatbázis szolgáltatási szintre vonatkozó célkitűzése is.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>Tudok olvasni adatokat blob storage és nem gyors betöltés (mint a Polybase az SQL Data Warehouse)

Egy ügyfélalkalmazás adatokat olvashat az Azure Storage-ból, és betöltheti az adatokat egy nagy méretű adatbázisba (csakúgy, mint bármely más Azure SQL-adatbázis). A Polybase jelenleg nem támogatott az Azure SQL Database-ben. A gyors betöltés alternatívájaként használhatja az [Azure Data Factoryt,](https://docs.microsoft.com/azure/data-factory/)vagy használhatja a Spark-feladatot az [Azure Databricks-ben](https://docs.microsoft.com/azure/azure-databricks/) az [SQL Spark-összekötővel.](sql-database-spark-connector.md) A Spark-összekötő az SQL támogatja a tömeges lapka.

Az Azure Blob áruházból is tömegesen olvashat adatokat a BULK INSERT vagy az OPENROWSET használatával: [Példák az Azure Blob Storage-beli adatokhoz való tömeges hozzáférésre.](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location)

Egyszerű helyreállítási vagy tömeges naplózási modell nem támogatott a nagy kapacitású. Teljes helyreállítási modell szükséges a magas rendelkezésre állás és a pont-in-time helyreállítási. A nagy kapacitású naplóarchitektúra azonban jobb adatbetöltési arányt biztosít más Azure SQL Database szolgáltatási szintekhez képest.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>A nagy kapacitású szolgáltatások lehetővé teszik több csomópont kiépítését nagy mennyiségű adat párhuzamos betöltéséhez

Nem. A nagykapacitás egy szimmetrikus többfeldolgozású (SMP) architektúra, és nem masszívan párhuzamos feldolgozás (MPP) vagy többfőes architektúra. Csak több replikát hozhat létre az írásvédett számítási feladatok horizontális felskálázásához.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Mi a legrégebbi SQL Server-verzió, amely támogatja a nagy kapacitásra való áttelepítést?

SQL Server 2005. További információt az [Áttelepítés egyetlen adatbázisra vagy készletezett adatbázisra](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database)című témakörben talál. A kompatibilitási problémákról az [Adatbázis-áttelepítési kompatibilitási problémák megoldása](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues)című témakörben található.

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>Támogatja-e a Hiperscale-fájlok más adatforrásokból, például az Amazon Aurora, a MySQL, a PostgreSQL, az Oracle, a DB2 és más adatbázisplatformokról történő áttelepítést?

Igen. [Az Azure Database Migration Service](../dms/dms-overview.md) számos áttelepítési forgatókönyvet támogat.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Üzletmenet-folytonossági és vész-helyreállítási kérdések

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Milyen SLA-k állnak rendelkezésre a nagy kapacitású adatbázisokhoz?

Lásd: [SLA for Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). További másodlagos számítási replikák növelik a rendelkezésre állást, akár 99,99%, egy adatbázis két vagy több másodlagos számítási replikák.

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Az azure SQL Database szolgáltatás kezeli az adatbázis biztonsági másolatait

Igen.

### <a name="how-often-are-the-database-backups-taken"></a>Milyen gyakran készülnek az adatbázis biztonsági másolatai

Nincsenek hagyományos teljes, különbözeti és naplóbiztonsági mentések a nagy kapacitású adatbázisokhoz. Ehelyett az adatfájlok rendszeres tárolási pillanatképei vannak. A létrehozott napló egyszerűen megmarad a konfigurált megőrzési időszakban, amely lehetővé teszi a visszaállítást a megőrzési időszakon belül bármely időpontra.

### <a name="does-hyperscale-support-point-in-time-restore"></a>A hiperskálázási támogatási pont visszaállítása

Igen.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Mi a helyreállítási pont célkitűzés (RPO)/Helyreállítási idő célkitűzés (RTO) az adatbázis-visszaállításhoz a nagy kapacitású

Az RPO 0 min. Az RTO-cél kevesebb, mint 10 perc, az adatbázis méretétől függetlenül. 

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>Az adatbázis biztonsági mentése hatással van az elsődleges vagy másodlagos replikák számítási teljesítményére

Nem. A biztonsági másolatokat a tárolási alrendszer kezeli, és kihasználja a tárolási pillanatképeket. Ezek nem befolyásolják a felhasználói számítási feladatokat.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Végrehajthatok geo-visszaállítást nagy méretű adatbázissal

Igen. Geo-visszaállítás teljes mértékben támogatott. A pont-az-időpont visszaállítással ellentétben a geo-visszaállítás adatméret-műveletet igényel. Az adatfájlok másolása párhuzamosan történik, így a művelet időtartama elsősorban az adatbázis legnagyobb fájljának méretétől függ, nem pedig az adatbázis teljes méretétől. Geo-visszaállítási idő jelentősen rövidebb lesz, ha az adatbázis vissza áll az Azure régióban, amely [párosítva](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) van a régióban a forrás-adatbázis.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Beállíthatok georeplikációt a nagykapacitású adatbázissal

Jelenleg nem.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Készíthetek-e nagy kapacitású adatbázis-biztonsági mentést, és visszaállíthatom azt a helyszíni kiszolgálóra vagy az SQL Server kiszolgálójára egy virtuális gépen

Nem. A nagy kapacitású adatbázisok tárolási formátuma eltér az SQL Server bármely kiadott verziójától, és nem szabályozza a biztonsági mentéseket, és nem férhet hozzá. Ahhoz, hogy az adatok at egy nagy méretű adatbázis, kinyerheti az adatokat bármilyen adatmozgatási technológiák, azaz az Azure Data Factory, Az Azure Databricks, SSIS, stb.

## <a name="cross-feature-questions"></a>Több funkcióra feltett kérdések

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Elveszítek-e bármilyen funkciót vagy képességet a Nagykapacitású szolgáltatási szintre való áttelepítés után?

Igen. Az Azure SQL Database egyes funkciói nem támogatottak a nagy kapacitású, beleértve, de nem kizárólagosan a hosszú távú biztonsági mentés megőrzése. Miután áttelepítette az adatbázisokat a nagy kapacitásra, ezek a szolgáltatások nem működnek.

### <a name="will-polybase-work-with-hyperscale"></a>Will Polybase dolgozni Hyperscale

Nem. A Polybase nem támogatott az Azure SQL Database-ben.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>Támogatja-e a Hiperscale az R és a Python

Jelenleg nem.

### <a name="are-compute-nodes-containerized"></a>A számítási csomópontok tárolóba vannak foglalva

Nem. A nagy kapacitású folyamatok a [Service Fabric-csomópontokon](https://azure.microsoft.com/services/service-fabric/) (VM-ek) futnak, nem tárolókban.

## <a name="performance-questions"></a>Teljesítménykérdések

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Mennyi írási átviteli áteresztési műveletet tudok lenyomni egy nagy méretű adatbázisban

A tranzakciós napló átviteli sebességének felső határa 100 MB/s a nagy méretű számítási mérethez. Ennek a sebességnek a elérésére képes több tényezőtől függ, beleértve, de nem kizárólagosan a számítási feladatok típusát, az ügyfél konfigurációját, és az elsődleges számítási replika megfelelő számítási kapacitással rendelkezik a napló ilyen ütemben történő létrehozásához.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Hány IOPS kapok a legnagyobb számítási

Az IOPS és az IO-késés a számítási feladatok mintáitól függően változik. Ha az elért adatok a számítási replika gyorsítótárazott, látni fogja, hasonló I/Performance, mint a helyi SSD.

### <a name="does-my-throughput-get-affected-by-backups"></a>Az átviteli parancsot érintik a biztonsági mentések

Nem. A számítás le van választva a tárolórétegről. Ez kiküszöböli a biztonsági mentés teljesítményre gyakorolt hatását.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Az átviteli kapacitás támái lesznek, ha további számítási replikákat létesítek

Mivel a tároló meg van osztva, és nincs közvetlen fizikai replikáció az elsődleges és másodlagos számítási replikák között, az elsődleges replika átviteli tengelye nem lesz közvetlenül hatással másodlagos replikák hozzáadása. Azonban előfordulhat, hogy folyamatosan szabályozhatja a számítási feladatok írása az elsődleges, hogy a napló alkalmazni a másodlagos replikák és a lap kiszolgálók felzárkózni, a másodlagos replikák gyenge olvasási teljesítmény elkerülése érdekében.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Hogyan diagnosztizálhatók és háríthatók el teljesítményproblémák egy nagy méretű adatbázisban?

A legtöbb teljesítménybeli probléma esetén, különösen a tárolási teljesítményben nem gyökerező problémák esetén gyakori SQL Server diagnosztikai és hibaelhárítási lépések lépnek érvénybe. A nagy kapacitású tárolási diagnosztika az [SQL nagy teljesítményű hibaelhárítási diagnosztika című témakörben található.](sql-database-hyperscale-performance-diagnostics.md)

## <a name="scalability-questions"></a>Méretezhetőségi kérdések

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Mennyi ideig tartana fel-le skálázni egy számítási replikát?

Skálázás számítási fel-le kell venni 5–10 perc, függetlenül az adatok méretétől.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Az adatbázis offline állapotban van, miközben a felskálázási/lefelé művelet folyamatban van

Nem. A fel- és leskálázás online lesz.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>A méretezési műveletek folyamatban lévő állapotának elérésekor a kapcsolat megszakadására számítok

Felfelé vagy lefelé történő felskálázás esetén a meglévő kapcsolatok megszakadnak, amikor a skálázási művelet végén feladatátvétel történik. Másodlagos replikák hozzáadása nem eredményez kapcsolatcseppeket.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>A számítási replikák fel- és leskálázása automatikus vagy végfelhasználó által aktivált művelet

Végfelhasználói. Nem automata.  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>Az `tempdb` adatbázis mérete is nő, ahogy a számítás felvan skálázva

Igen. Az `tempdb` adatbázis automatikusan felskálázódik, ahogy a számítási növekszik.  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Több elsődleges számítási replikát is kiépíthetek, például egy többfős rendszert, ahol több elsődleges számítási fej magasabb szintű egyidejűséget vezethet

Nem. Csak az elsődleges számítási replika fogadja olvasási/írási kérelmeket. Másodlagos számítási replikák csak olvasni írásvédett kérelmeket fogad el.

## <a name="read-scale-out-questions"></a>Kibővített kérdések olvasása

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Hány másodlagos számítási replikát tudok kiépíteni?

Alapértelmezés szerint egy másodlagos replika a nagy kapacitású adatbázisok hozunk létre. Ha módosítani szeretné a replikák számát, ezt megteheti az [Azure Portal](https://portal.azure.com) vagy a [REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)használatával.

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Hogyan csatlakozhatok ezekhez a másodlagos számítási replikákhoz?

Ezekhez a további írásvédett számítási kópiákhoz úgy csatlakozhat, hogy a `ApplicationIntent` kapcsolati karakterlánc argumentumát a-ra `ReadOnly`állítja. A megjelölt `ReadOnly` kapcsolatok automatikusan átirányítva lesznek a további írásvédett számítási replikák egyikére.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Hogyan ellenőrizhetem, hogy sikeresen csatlakoztam-e másodlagos számítási replikához SSMS vagy más ügyféleszközök használatával?

A következő T-SQL lekérdezést `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`hajthatja végre: .
Az eredmény `READ_ONLY` akkor következik be, ha írásvédett `READ_WRITE` másodlagos kópiához csatlakozik, és csatlakozik az elsődleges kópiához. Ne feledje, hogy az adatbázis környezetét a nagy kapacitású adatbázis nevére kell beállítani, nem pedig az `master` adatbázisra.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Létrehozhatok egy dedikált végpontot egy olvasási kibővített replika számára

Nem. Az olvasási kibővített replikákhoz csak a `ApplicationIntent=ReadOnly`megadott mennyiség megadásával csatlakozhat.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>A rendszer intelligens terheléselosztást végez az olvasási munkaterhelésben

Nem. Az írásvédett szándékkal rendelkező új kapcsolatot a rendszer egy tetszőleges olvasási kibővített kópiához irányítja át.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Az elsődleges replikától függetlenül fel-le skálázhatom a másodlagos számítási replikákat

Nem. A másodlagos számítási replika is magas rendelkezésre állású feladatátvételi célokként is használatos, így ugyanolyan konfigurációval kell rendelkezniük, mint az elsődleges a feladatátvétel után a várható teljesítmény biztosításához.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Kapok különböző `tempdb` méretezésaz én elsődleges számítási és a további másodlagos számítási replikák

Nem. Az `tempdb` adatbázis a számítási méret kiépítése alapján van konfigurálva, a másodlagos számítási replikák mérete megegyezik az elsődleges számítási méret.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Hozzáadhatok indexeket és nézeteket a másodlagos számítási replikákhoz

Nem. A nagy kapacitású adatbázisok megosztott tárolóval rendelkeznek, ami azt jelenti, hogy az összes számítási kópia ugyanazokat a táblákat, indexeket és nézeteket látja. Ha azt szeretné, hogy további indexek vannak optimalizálva a másodlagos olvasásokhoz, hozzá kell adnia őket az elsődlegeshez.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Mennyi késleltetés lesz az elsődleges és a másodlagos számítási replikák között?

Adatkésés attól az időponttól kezdve, amikor egy tranzakció véglegesítése az elsődleges, hogy az idő látható egy másodlagos függ az aktuális napló generálási arány. A tipikus adatkésés alacsony ezredmásodpercben van.

## <a name="next-steps"></a>Következő lépések

A nagy kapacitású szolgáltatási szintről további információt a [Nagykapacitású szolgáltatási szint című témakörben talál.](sql-database-service-tier-hyperscale.md)
