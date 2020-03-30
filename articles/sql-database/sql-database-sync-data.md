---
title: Adatszinkronizálás
description: Ez az áttekintés bemutatja az Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: 1ee2efbb8aebfc2f1a94c89edef6166898946d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74422528"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Adatok szinkronizálása több felhőbeli és helyszíni adatbázisban az SQL Data Sync segítségével

Az SQL Data Sync egy Azure SQL Database-re épülő szolgáltatás, amely lehetővé teszi a kiválasztott adatok kétirányú szinkronizálását több SQL-adatbázis és SQL Server-példány között.

> [!IMPORTANT]
> Az Azure SQL Data Sync jelenleg nem támogatja az Azure SQL Database felügyelt példánya.

## <a name="when-to-use-data-sync"></a>Mikor kell használni az Adatszinkronizálást?

Az adatszinkronizálás olyan esetekben hasznos, ahol az adatokat több Azure SQL-adatbázisban vagy SQL Server-adatbázisban kell frissíteni. Az adatszinkronizálás fő felhasználási esetei a következők:

- **Hibrid adatszinkronizálás:** Az Adatszinkronizálás segítségével a helyszíni adatbázisok és az Azure SQL-adatbázisok között szinkronizálhatja az adatokat a hibrid alkalmazások engedélyezéséhez. Ez a funkció vonzó lehet az ügyfelek számára, akik fontolgatják, hogy a felhőbe költöznek, és szeretnék, hogy az alkalmazás egy részét az Azure-ban.
- **Elosztott alkalmazások:** Sok esetben hasznos a különböző adatbázisok közötti különböző számítási feladatok elkülönítése. Például ha egy nagy éles adatbázis, de azt is meg kell futtatni a jelentéskészítési vagy elemzési számítási feladatok ezen adatok, hasznos, hogy egy második adatbázis a további számítási feladatokhoz. Ez a megközelítés minimálisra csökkenti az éles számítási feladatokra gyakorolt teljesítményhatást. Az Adatszinkronizálás segítségével a két adatbázis szinkronizálható marad.
- **Globálisan elosztott alkalmazások:** Számos vállalkozás több régiót, sőt több országot/régiót is felölel. A hálózati késés minimalizálása érdekében a legjobb, ha az adatok egy önhöz közeli régióban vannak. Az Adatszinkronizálás segítségével könnyedén szinkronizálhatja az adatbázisokat a világ különböző régióiban.

Az adatszinkronizálás nem az előnyben részesített megoldás a következő esetekben:

| Forgatókönyv | Néhány ajánlott megoldás |
|----------|----------------------------|
| Vészhelyreállítás | [Azure georedundáns biztonsági mentések](sql-database-automated-backups.md) |
| Méretezés olvasása | [Írásvédett replikák használata írásvédett lekérdezési számítási feladatok betöltéséhez (előzetes verzió)](sql-database-read-scale-out.md) |
| ETL (OLTP-tól OLAP-ig) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) vagy [SQL Server integrációs szolgáltatások](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Áttelepítés a helyszíni SQL Server kiszolgálóról az Azure SQL Database szolgáltatásba | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Az SQL-adatszinkronizálás áttekintése

Az adatszinkronizálás a szinkronizálási csoport koncepciójára épül. A szinkronizálási csoport a szinkronizálni kívánt adatbázisok csoportja.

A Data Sync egy hubot és egy küllős topológiát használ az adatok szinkronizálásához. A szinkronizálási csoport egyik adatbázisát Hub-adatbázisként definiálhatja. A többi adatbázis tagadatbázis. A szinkronizálás csak a központ és az egyes tagok között történik.

- A **Hub-adatbázisnak** Azure SQL-adatbázisnak kell lennie.
- A **tagadatbázisok** lehetnek SQL-adatbázisok, helyszíni SQL Server-adatbázisok vagy SQL Server-példányok az Azure virtuális gépeken.
- A **szinkronizálási adatbázis** tartalmazza a metaadatokat és az adatszinkronizálásnaplózást. A szinkronizálási adatbázisnak egy Azure SQL-adatbázisnak kell lennie, amely ugyanabban a régióban található, mint a Hub Database. A szinkronizálási adatbázis az ügyfél által létrehozott és az ügyfél tulajdonában van.

> [!NOTE]
> Ha a helyszíni adatbázist tagadatbázisként használja, [telepítenie és konfigurálnia kell egy helyi szinkronizálási ügynököt.](sql-database-get-started-sql-data-sync.md#add-on-prem)

![Adatok szinkronizálása adatbázisok között](media/sql-database-sync-data/sync-data-overview.png)

A szinkronizálási csoport a következő tulajdonságokkal rendelkezik:

- A **szinkronizálási séma** leírja, hogy mely adatok szinkronizálása folyamatban van.
- A **Szinkronizálás iránya** lehet kétirányú, vagy csak egy irányban folyhat. Ez azt illeti, a Szinkronizálási irány lehet *Hub to Member*, vagy Tag a *Hub*, vagy mindkettő.
- A **szinkronizálási időköz** azt ismerteti, hogy milyen gyakran történik szinkronizálás.
- Az **ütközésfeloldási házirend** csoportszintű házirend, amely lehet *hub győzelem* vagy tag *nyer*.

## <a name="how-does-data-sync-work"></a>Hogyan működik az adatszinkronizálás?

- **Az adatok változásainak nyomon követése:** Az Adatszinkronizálás a változásokat beszúrási, frissítési és törlési eseményindítók használatával követi nyomon. A módosításokat a rendszer a felhasználói adatbázis egy oldaltáblájában rögzíti. Ne feledje, hogy a BULK INSERT alapértelmezés szerint nem indítja el az eseményindítókat. Ha FIRE_TRIGGERS nincs megadva, nem hajtvégre beszúrási eseményindítókat. Adja hozzá a FIRE_TRIGGERS beállítást, hogy az Adatszinkronizálás nyomon követhesse ezeket a beszúrásokat. 
- **Adatok szinkronizálása:** A Data Sync hub- és küllős modellben készült. A Hub külön-külön szinkronizálja az egyes tagokat. A Hub ról származó módosítások letöltődnek a tagba, majd a tag módosításai feltöltésre kerülnek a Hubba.
- **Ütközések feloldása:** A Data Sync két lehetőséget kínál az ütközések feloldására, *a Hub nyer,* vagy *a tag nyer.*
  - Ha a Hub wins lehetőséget *választja,* a hub változásai mindig felülírják a tag változásait.
  - Ha a *Tag nyer*lehetőséget választja, a tag módosításai felülírják a hub változásait. Ha egynél több tag van, a végső érték attól függ, hogy melyik tag szinkronizálelőször.

## <a name="compare-data-sync-with-transactional-replication"></a>Az adatszinkronizálás összehasonlítása a tranzakciós replikációval

| | Adatszinkronizálás | Tranzakciós replikáció |
|---|---|---|
| Előnyök | - Aktív-aktív támogatás<br/>- Kétirányú között helyszíni és az Azure SQL Database | - Kisebb késleltetés<br/>- Tranzakciós konzisztencia<br/>- A meglévő topológia újrafelhasználása a migráció után |
| Hátrányok | - 5 min vagy több késleltetés<br/>- Nincs tranzakciós konzisztencia<br/>- Nagyobb teljesítményhatás | - Nem lehet közzétenni az Azure SQL Database egyetlen adatbázisvagy készletbe helyezett adatbázis<br/>- Magas karbantartási költség |

## <a name="get-started-with-sql-data-sync"></a>Az SQL Data Sync – első lépések

### <a name="set-up-data-sync-in-the-azure-portal"></a>Adatszinkronizálás beállítása az Azure Portalon

- [Az Azure SQL Data Sync beállítása](sql-database-get-started-sql-data-sync.md)
- Adatszinkronizálási ügynök – [Adatszinkronizálási ügynök az Azure SQL Data Sync szolgáltatáshoz](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Adatszinkronizálás beállítása a PowerShell használatával

- [A PowerShell használata több Azure SQL-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
- [A PowerShell használata egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Az adatszinkronizálással kapcsolatos gyakorlati tanácsok áttekintése

- [Ajánlott eljárások az Azure SQL Data Synchez](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Valami rosszul sült el?

- [Az Azure SQL Data Synckel hibaelhárítása](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Konzisztencia és teljesítmény

### <a name="eventual-consistency"></a>Végleges konzisztencia

Mivel az adatszinkronizálás eseményindító-alapú, a tranzakciós konzisztencia nem garantált. A Microsoft garantálja, hogy az összes módosítás végül megtörténik, és hogy az adatszinkronizálás nem okoz adatvesztést.

### <a name="performance-impact"></a>Teljesítményre gyakorolt hatás

Az Adatszinkronizálás beszúrási, frissítési és törlési eseményindítókat használ a változások nyomon követésére. Oldaltáblákat hoz létre a felhasználói adatbázisban a változások nyomon követéséhez. Ezek a változáskövetési tevékenységek hatással vannak az adatbázis munkaterhelésére. Mérje fel a szolgáltatási szintet, és szükség esetén frissítsen.

A szinkronizálási csoport létrehozása, frissítése és törlése során a kiépítés és a megszüntetés szintén hatással lehet az adatbázis teljesítményére.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a>Követelmények és korlátozások

### <a name="general-requirements"></a>Általános követelmények

- Minden táblának rendelkeznie kell egy elsődleges kulccsal. Ne módosítsa az elsődleges kulcs értékét egyetlen sorban sem. Ha módosítania kell egy elsődleges kulcs értékét, törölje a sort, és hozza létre újra az új elsődleges kulcsértékkel.

> [!IMPORTANT]
> Egy meglévő elsődleges kulcs értékének módosítása a következő hibás viselkedést eredményezi:
> - A hub és a tag közötti adatok elveszhetnek, még akkor is, ha a szinkronizálás nem jelent problémát.
> - A szinkronizálás sikertelen lehet, mert a követési tábla az elsődleges kulcs módosítása miatt nem létező sorból származik a forrásból.

- Engedélyezni kell a pillanatkép-elkülönítést. További információ: [Pillanatkép-elkülönítés az SQL Serveren](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Általános korlátozások

- Egy tábla nem rendelkezhet olyan identitásoszloppal, amely nem az elsődleges kulcs.
- Az elsődleges kulcs nem rendelkezhet a következő adattípusokkal: sql_variant, bináris, varbinary, image, xml.
- Legyen elővigyázatos, ha a következő adattípusokat használja elsődleges kulcsként, mert a támogatott pontosság csak a második: idő, datetime, datetime2, datetimeoffset.
- Az objektumok (adatbázisok, táblák és oszlopok) neve nem tartalmazhatja a nyomtatható karaktereket, pont (.), bal oldali szögletes zárójelet ([) vagy jobb oldali szögletes zárójelet (]).
- Az Azure Active Directory-hitelesítés nem támogatott.
- Az azonos nevű, de eltérő sémával rendelkező táblák (például dbo.customers és sales.customers) nem támogatottak.
- A felhasználó által definiált adattípusokkal rendelkező oszlopok nem támogatottak

#### <a name="unsupported-data-types"></a>Nem támogatott adattípusok

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (XML támogatott)
- Kurzor, Sorverzió, Időbélyeg, Hierarchyid

#### <a name="unsupported-column-types"></a>Nem támogatott oszloptípusok

Az adatszinkronizálás nem szinkronizálható írásvédett vagy a rendszer által létrehozott oszlopokat. Példa:

- Számított oszlopok.
- Rendszer által létrehozott oszlopok a temporális táblákhoz.

#### <a name="limitations-on-service-and-database-dimensions"></a>A szolgáltatás- és adatbázisdimenziók korlátozásai

| **Méretek**                                                  | **Korlát**              | **Workaround**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Azon szinkronizálási csoportok maximális száma, amelyekhez egy adatbázis tartozhat.       | 5                      |                             |
| Végpontok maximális száma egyetlen szinkronizálási csoportban              | 30                     |                             |
| A helyszíni végpontok maximális száma egyetlen szinkronizálási csoportban. | 5                      | Több szinkronizálási csoport létrehozása |
| Adatbázis-, tábla-, séma- és oszlopnevek                       | Névnként 50 karakter |                             |
| Táblázatok szinkronizálási csoportban                                          | 500                    | Több szinkronizálási csoport létrehozása |
| Oszlopok egy táblázatban egy szinkronizálási csoportban                              | 1000                   |                             |
| Adatsor mérete egy táblában                                        | 24 Mb                  |                             |
| Minimális szinkronizálási időköz                                           | 5 perc              |                             |

> [!NOTE]
> Egy szinkronizálási csoportban legfeljebb 30 végpont lehet, ha csak egy szinkronizálási csoport van. Ha egynél több szinkronizálási csoport van, a végpontok száma az összes szinkronizálási csoportban nem haladhatja meg a 30-at. Ha egy adatbázis több szinkronizálási csoporthoz tartozik, akkor több végpontnak számít, nem egynek.

## <a name="faq-about-sql-data-sync"></a>Gyakori kérdések az SQL Data Sync-ről

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Mennyibe kerül az SQL Data Sync szolgáltatás?

Az SQL Data Sync szolgáltatás értnem ingyenes. Azonban továbbra is gyűjt adatátviteli díjakat az SQL Database-példányban történő és az SQL Database-példányból történő adatátvitelért. További információ: [SQL Database pricing](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Milyen régiók támogatják az adatszinkronizálást?

Az SQL Data Sync minden régióban elérhető.

### <a name="is-a-sql-database-account-required"></a>Szükséges-e SQL Database-fiók

Igen. A Hub-adatbázis üzemeltetéséhez SQL Database-fiókkal kell rendelkeznie.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Használhatom az Adatszinkronizálást csak az SQL Server helyszíni adatbázisai közötti szinkronizálásra

Nem közvetlenül. Az SQL Server helyszíni adatbázisai között közvetetten is szinkronizálhat, ha hub-adatbázist hoz létre az Azure-ban, majd hozzáadja a helyszíni adatbázisokat a szinkronizálási csoporthoz.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Használhatom az Adatszinkronizálást a különböző előfizetésekhez tartozó SQL-adatbázisok közötti szinkronizáláshoz

Igen. Szinkronizálhatja a különböző előfizetések által birtokolt erőforráscsoportokhoz tartozó SQL-adatbázisok at.

- Ha az előfizetések ugyanahhoz a bérlőhöz tartoznak, és minden előfizetéshez engedéllyel rendelkezik, konfigurálhatja a szinkronizálási csoportot az Azure Portalon.
- Ellenkező esetben a PowerShell használatával kell hozzáadnia a különböző előfizetésekhez tartozó szinkronizálási tagokat.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Használhatom az Adatszinkronizálást a különböző felhőkhöz tartozó SQL-adatbázisok (például az Azure Public Cloud és az Azure China 21Vianet) közötti szinkronizáláshoz

Igen. Szinkronizálhatja a különböző felhőkhöz tartozó SQL-adatbázisok között, a PowerShell használatával kell hozzáadnia a különböző előfizetésekhez tartozó szinkronizálási tagokat.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Használhatom a Data Sync-et a termelési adatbázisból származó adatok üres adatbázisba történő bemagzására, majd szinkronizálhatom őket

Igen. Hozza létre a sémát manuálisan az új adatbázisban az eredetiből történő parancsfájlálással. Miután létrehozta a sémát, adja hozzá a táblákat egy szinkronizálási csoporthoz az adatok másolásához és szinkronizálásához.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Az SQL Data Sync használatával kell biztonsági másolatot tartani és visszaállítani az adatbázisokat

Nem ajánlott az SQL Data Sync használata az adatok biztonsági másolatának létrehozásához. Nem lehet biztonsági másolatot és visszaállítást egy adott időpontban, mert az SQL Data Syncsyncizations nem verziószámozott. Továbbá az SQL Data Sync nem biztonsági másolatot más SQL-objektumok, például a tárolt eljárások, és nem teszi meg a megfelelő visszaállítási művelet gyorsan.

Az egyik ajánlott biztonsági mentési technikáról az [Azure SQL-adatbázis másolása](sql-database-copy.md)című témakörben található.

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Szinkronizálhatja az adatok szinkronizálását a titkosított táblák és oszlopok

- Ha egy adatbázis mindig titkosított, csak a *nem* titkosított táblákat és oszlopokat szinkronizálhatja. A titkosított oszlopok nem szinkronizálhatók, mert az adatszinkronizálás nem tudja visszafejteni az adatokat.
- Ha egy oszlop oszlopszintű titkosítást (CLE) használ, szinkronizálhatja az oszlopot, feltéve, hogy a sormérete kisebb, mint a 24 Mb-os maximális méret. A Data Sync a kulcs (CLE) által titkosított oszlopot normál bináris adatként kezeli. A többi szinkronizálási tag adatainak visszafejtéséhez ugyanazzal a tanúsítvánnyal kell rendelkeznie.

### <a name="is-collation-supported-in-sql-data-sync"></a>A rendezés támogatott az SQL Data Sync-ben

Igen. Az SQL Data Sync a következő esetekben támogatja a rendezést:

- Ha a kijelölt szinkronizálási sématáblák még nincsenek a központi vagy tagadatbázisokban, akkor a szinkronizálási csoport telepítésekor a szolgáltatás automatikusan létrehozza a megfelelő táblákat és oszlopokat az üres céladatbázisokban kiválasztott rendezési beállításokkal.
- Ha a szinkronizálandó táblák már léteznek a központi és a tagadatbázisokban is, az SQL Data Sync megköveteli, hogy az elsődleges kulcsoszlopai azonos egyeztetést alkalmazzanak a hub- és tagadatbázisok között a szinkronizálási csoport sikeres központi és tagi adatbázisainak üzembe helyezéséhez. Az elsődleges kulcsoszlopokon kívül az oszlopokra nincsenek rendezési korlátozások.

### <a name="is-federation-supported-in-sql-data-sync"></a>Az összevonás támogatott az SQL Data Sync-ben

Az összevonási gyökéradatbázis korlátozás nélkül használható az SQL Data Sync Service szolgáltatásban. Az összevont adatbázis végpontja nem adhat hozzá az SQL Data Sync aktuális verziójához.

## <a name="next-steps"></a>További lépések

### <a name="update-the-schema-of-a-synced-database"></a>Szinkronizált adatbázis sémájának frissítése

Frissíti egy adatbázis sémáját egy szinkronizálási csoportban? A sémamódosítások at a rendszer nem replikálja automatikusan. További megoldásokat az alábbi cikkekben talál:

- [Sémamódosítások replikációjának automatizálása az Azure SQL Data Sync szolgáltatásban](sql-database-update-sync-schema.md)
- [PowerShell használata meglévő szinkronizálási csoport szinkronizálási sémájának frissítéséhez](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Figyelés és hibaelhárítás

Az SQL Data Sync a várt módon halad? A tevékenységek figyeléséhez és a problémák elhárításához olvassa el az alábbi cikkeket:

- [Az Azure SQL-adatszinkronizálás figyelése az Azure Monitor-naplókkal](sql-database-sync-monitor-oms.md)
- [Az Azure SQL Data Synckel hibaelhárítása](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>További információ az Azure SQL Database-ről

Az SQL Database alkalmazásról további információt az alábbi cikkekben talál:

- [Az SQL Database áttekintése](sql-database-technical-overview.md)
- [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
