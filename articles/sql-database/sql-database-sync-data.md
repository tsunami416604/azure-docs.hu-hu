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
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422528"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Az SQL Data Sync szolgáltatással több felhőalapú és helyszíni adatbázis közötti adatszinkronizálás

Az SQL Data Sync egy szolgáltatás, amely az Azure SQL Database, amely lehetővé teszi az adatokat több SQL-adatbázisok és az SQL Server-példányok kiválasztása kétirányúan szinkronizálja.

> [!IMPORTANT]
> Az Azure SQL-adatszinkronizálás jelenleg nem támogatja Azure SQL Database felügyelt példányok használatát.

## <a name="when-to-use-data-sync"></a>Mikor érdemes használni az adatok szinkronizálása

Az adatszinkronizálás olyan esetekben hasznos, amikor az egyes Azure SQL-adatbázisokban vagy SQL Server-adatbázisokban frissíteni kell az adattárolást. Az alábbiakban a fő használati esetek Data Sync számára:

- **Hibrid adatszinkronizálás:** Az adatszinkronizálással megtarthatja a helyszíni adatbázisok és az Azure SQL-adatbázisok közötti szinkronizálást a hibrid alkalmazások engedélyezéséhez. Ez a képesség is konfigurálja, hogy a ügyfelek, akik használatát fontolgatja a felhőre, és szeretné helyezni néhány alkalmazását az Azure-ban.
- **Elosztott alkalmazások:** Sok esetben előnyös a különböző számítási feladatok különböző adatbázisokban való elkülönítése. Például ha olyan nagy méretű éles adatbázist, de szükség erre az egy jelentésben vagy elemzési számítási feladatok futtatásához, hasznos ahhoz, hogy a további számítási második adatbázis. Ez a megközelítés minimálisra csökkenti a éles számítási feladatokra gyakorolt hatást. Használhatja a Data Syncet, hogy a két adatbázis szinkronizálva.
- **Globálisan elosztott alkalmazások:** Számos vállalat számos régiót és akár több országot/régiót is kiterjedhet. Hálózati késés minimalizálása érdekében a legjobb, ha az adatok egy régióban Önhöz. Az adatszinkronizálás könnyedén tarthatja adatbázisok szinkronizálása a világ különböző pontjain található régiókban.

Az adatszinkronizálás nem az előnyben részesített megoldás a következő esetekben:

| Forgatókönyv | Néhány ajánlott megoldásokat |
|----------|----------------------------|
| Vészhelyreállítás | [Azure geo-redundáns biztonsági mentések](sql-database-automated-backups.md) |
| Olvassa el a méretezési csoport | [Csak olvasható replikák használata az írásvédett lekérdezési feladatok terheléselosztásához (előzetes verzió)](sql-database-read-scale-out.md) |
| ETL (OLTP, OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) vagy [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migrálás a helyszíni SQL Serverről az Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>A SQL-adatszinkronizálás áttekintése

Data Sync szinkronizálási csoport fogalma köré alapul. Szinkronizálási csoport, a szinkronizálni kívánt adatbázisok egy csoportját.

Adatok szinkronizálása egy küllős topológia használatával adatok szinkronizálása. Megadhat egy adatbázis a szinkronizálási csoport, a központi adatbázis. Az adatbázisok a többi tag adatbázisok. Szinkronizálás csak a Hub és az egyes tagok között történik.

- A **hub-adatbázisnak** Azure SQL Databasenak kell lennie.
- A **tag-adatbázisok** lehetnek SQL-adatbázisok, helyszíni SQL Server adatbázisok vagy SQL Server példányok az Azure Virtual Machines szolgáltatásban.
- A **szinkronizálási adatbázis** tartalmazza a metaadatokat és a naplót az adatok szinkronizálásához. A szinkronizálási adatbázisnak egy olyan Azure SQL Database kell lennie, amely ugyanabban a régióban található, mint a hub-adatbázis. A Sync-adatbázis létrehozása ügyfél és a vásárlói kezelésű.

> [!NOTE]
> Ha helyszíni adatbázist használ tagként adatbázisként, [telepítenie és konfigurálnia kell egy helyi szinkronizáló ügynököt](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Adatok szinkronizálása adatbázisok között](media/sql-database-sync-data/sync-data-overview.png)

Szinkronizálási csoport a következő tulajdonságokkal rendelkezik:

- A **szinkronizálási séma** leírja, hogy mely adatokat szinkronizálja a rendszer.
- A **szinkronizálás iránya** lehet kétirányú, vagy csak egy irányba lehet flow. Vagyis a szinkronizálás iránya lehet a *tag*, a tag vagy a két *csomópont*is.
- A szinkronizálási **időköz** leírja, hogy milyen gyakran történjen a szinkronizálás.
- Az **ütközés-feloldási házirend** egy csoport szintű házirend, amely lehet *hub WINS* vagy *tag WINS*.

## <a name="how-does-data-sync-work"></a>Hogyan működik az adatszinkronizálás?

- **Az adatváltozások nyomon követése:** Az adatszinkronizálás nyomon követi a módosításokat INSERT, Update és DELETE eseményindítók használatával. A módosítások a felhasználói adatbázisban egy oldali táblában vannak rögzítve. Vegye figyelembe, hogy az BULK INSERT alapértelmezés szerint nem indít el tüzet. Ha FIRE_TRIGGERS nincs megadva, a beszúrási eseményindítók nem lesznek végrehajtva. Adja hozzá a FIRE_TRIGGERS lehetőség, hogy a Data Sync ezeket beillesztések nyomon követheti. 
- **Az adatszinkronizálás:** Az adatszinkronizálást egy sugaras modellben tervezték. A Hub külön-külön szinkronizál minden tagja. A központi menü módosításokat a rendszer letölti a tag, és majd a tag program feltölti a hubhoz.
- **Ütközések feloldása:** Az adatszinkronizálás két lehetőséget kínál az ütközés feloldására, a *hub WINS* vagy a *tag WINS*használatára.
  - Ha a *hub WINS*lehetőséget választja, a hub módosításai mindig felülírják a tag módosításait.
  - Ha a *tag WINS*lehetőséget választja, a tag módosításai felülírják a központban lévő módosításokat. Ha egynél több tagja van, a végső értéke attól függ, melyik tag először szinkronizálja.

## <a name="compare-data-sync-with-transactional-replication"></a>Adatszinkronizálás összehasonlítása tranzakciós replikációval

| | Adatszinkronizálás | Tranzakciós replikáció |
|---|---|---|
| Előnyök | – Aktív-aktív támogatás<br/>– A helyszíni és a Azure SQL Database közötti kétirányú irányítás | – Alacsonyabb késés<br/>– Tranzakciós konzisztencia<br/>-Meglévő topológia újrafelhasználása az áttelepítés után |
| Hátrányai | – 5 perc vagy több késés<br/>– Nincs tranzakciós konzisztencia<br/>– Nagyobb teljesítményre gyakorolt hatás | – Nem lehet közzétenni Azure SQL Database önálló adatbázisból vagy készletezett adatbázisból<br/>– Magas karbantartási díj |

## <a name="get-started-with-sql-data-sync"></a>Ismerkedés az SQL Data Sync szolgáltatással

### <a name="set-up-data-sync-in-the-azure-portal"></a>Az Azure Portal Data Sync beállítása

- [Az Azure SQL Data Sync beállítása](sql-database-get-started-sql-data-sync.md)
- Adatszinkronizálási ügynök – [Az Azure SQL-adatszinkronizálás adatszinkronizálási ügynöke](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>A PowerShell használatával a Data Sync beállítása

- [A PowerShell használata több Azure SQL-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
- [A PowerShell használata egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Az adatok szinkronizálása az ajánlott eljárások áttekintése

- [Ajánlott eljárások az Azure SQL Data Synchez](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Valami nem stimmel

- [Az Azure SQL Data Synckel hibaelhárítása](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Konzisztencia és a teljesítmény

### <a name="eventual-consistency"></a>Végleges konzisztencia

Mivel az adatszinkronizálás trigger-alapú, a tranzakciós konzisztencia nem garantált. A Microsoft garantálja, hogy az összes módosítást végül megtörténik, és az adatszinkronizálás nem okozza az adatvesztést.

### <a name="performance-impact"></a>A teljesítményt

Adatok szinkronizálása által beszúrása, frissítése és törlése eseményindítók változásainak követése. Ügyféloldali táblák létrehoz a felhasználói adatbázisban a change Tracking szolgáltatáshoz. E módosítás követési tevékenységek hatással vannak az adatbázisban munkaterhelés. A szolgáltatási szintben felmérése, és szükség esetén frissítse.

Kiépítés és megszüntetés során a szinkronizálási csoport létrehozása, frissítése és törlése is befolyásolhatja az adatbázis teljesítményét.

## <a name="sync-req-lim"></a>Követelmények és korlátozások

### <a name="general-requirements"></a>Általános követelmények

- Minden táblának elsődleges kulccsal kell rendelkeznie. Az összes sort az elsődleges kulcs értékét ne módosítsa. Ha módosítania kell egy elsődleges kulcs értékét, törölni a sort, és hozza létre újból az új elsődleges kulcs értéke.

> [!IMPORTANT]
> Egy meglévő elsődleges kulcs értékének módosítása a következő hibás viselkedést eredményezi:
> - A központ és a tag közötti adatvesztés akkor is elvész, ha a szinkronizálás nem jelent problémát.
> - A szinkronizálás sikertelen lehet, mert a követési tábla nem létező sort tartalmaz a forrástól az elsődleges kulcs módosítása miatt.

- Engedélyezni kell a pillanatkép-elkülönítést. További információ: [Pillanatkép-elkülönítés az SQL Serveren](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Általános korlátozások

- Egy táblának nem lehet olyan azonosító oszlopa, amely nem az elsődleges kulcs.
- Az elsődleges kulcs nem rendelkezhet a következő adattípusokkal: sql_variant, Binary, varbinary, képet, XML.
- Legyen óvatos elsődleges kulcsaként, az alábbi adattípusok használatakor a támogatott pontosság csak a második, mert: idő, dátum és idő, datetime2, datetimeoffset.
- Az objektumok (adatbázisok, táblák és oszlopok) nevei nem tartalmazhatják a nyomtatható karaktereket (.), a bal oldali szögletes zárójelet ([) vagy a jobb oldali szögletes zárójelet (]).
- Azure Active Directory hitelesítés nem támogatott.
- Az azonos nevű, de eltérő sémával rendelkező táblák (például dbo. Customers és Sales. Customers) nem támogatottak.
- A felhasználó által definiált adattípusú oszlopok nem támogatottak

#### <a name="unsupported-data-types"></a>Nem támogatott adattípusok

- FileStream
- AZ SQL ÉS CLR-BELI UDT
- XMLSchemaCollection (XML támogatott)
- A kurzor RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Nem támogatott oszloptípus

Adatok szinkronizálása nem tudják szinkronizálni a csak olvasható vagy rendszer által létrehozott oszlopok. Például:

- Számított oszlopok.
- A rendszer által létrehozott oszlopok időbeli verziózású táblák esetében.

#### <a name="limitations-on-service-and-database-dimensions"></a>A szolgáltatás és az adatbázis-dimenziókra korlátozások

| **Méretek**                                                  | **Korlát**              | **Áthidaló megoldás**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Szinkronizálási csoportok maximális száma bármilyen adatbázis is tartozhat.       | 5                      |                             |
| Egyetlen szinkronizálási csoport végpontok maximális száma              | 30                     |                             |
| A helyszíni végpontokig egy szinkronizálási csoportban maximális számát. | 5                      | Több szinkronizálási csoport létrehozása |
| Adatbázis-, tábla-, séma, és oszlop                       | név szerint 50 karakter hosszú lehet |                             |
| A szinkronizálási csoport táblák                                          | 500                    | Több szinkronizálási csoport létrehozása |
| Szinkronizálási csoport egyik táblájában az oszlopok                              | 1000                   |                             |
| Egy tábla adatok sor mérete                                        | 24 Mb                  |                             |
| Minimális szinkronizálási időköz                                           | 5 perc              |                             |

> [!NOTE]
> Lehet legfeljebb 30 végpontok egy szinkronizálási csoportban esetén csak egy szinkronizálási csoportot. Ha egynél több szinkronizálási csoport, a végpontok között az összes szinkronizálási csoportok száma nem lehet hosszabb 30. Ha egy adatbázis több szinkronizálási csoport tagja, több végpontot, nem pedig egy számít.

## <a name="faq-about-sql-data-sync"></a>Az SQL Data Sync szolgáltatással kapcsolatos gyakori kérdések

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Mennyibe kerül a SQL-adatszinkronizálás szolgáltatás díja

Magáért a SQL-adatszinkronizálás szolgáltatásért nem számítunk fel díjat. Azonban továbbra is gyűjthet adatátviteli díjakat a SQL Database-példányon belüli és kívüli adatáthelyezésért. További információ: [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Mely régiók támogatják az adatszinkronizálást

Az SQL Data Sync minden régióban érhető el.

### <a name="is-a-sql-database-account-required"></a>SQL Database fiók szükséges

Igen. Az Eseményközpont-adatbázis üzemeltetésére szolgáló SQL-adatbázis fiókkal kell rendelkeznie.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Használhatok adatszinkronizálást SQL Server helyszíni adatbázisok közötti szinkronizálásra

Közvetlenül nem. Szinkronizálhatja a helyszíni SQL Server-adatbázisok közötti közvetve, azonban a központi adatbázis létrehozásával az Azure-ban, és majd a helyszíni adatbázisok hozzáadása a szinkronizálási csoport.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Használhatom az adatszinkronizálást a különböző előfizetésekhez tartozó SQL-adatbázisok közötti szinkronizáláshoz

Igen. Szinkronizálhatja különböző előfizetésekhez tartozó erőforráscsoportok tartozó SQL-adatbázisok között.

- Ha az előfizetés ugyanazt bérlőhöz tartozik, és Ön jogosult az összes előfizetés, konfigurálhatja a szinkronizálási csoport az Azure Portalon.
- Ellenkező esetben rendelkezik, amelyek különböző előfizetésekhez tartoznak a szinkronizálási tagok hozzáadása a PowerShell használatával.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Használhatok adatszinkronizálást a különböző felhőkhöz tartozó SQL-adatbázisok (például az Azure Public Cloud és az Azure China 21Vianet) közötti szinkronizáláshoz

Igen. A különböző előfizetésekhez tartoznak, a szinkronizálási tagok hozzáadása a PowerShell használatával telepítette, szinkronizálhatja különböző felhőkben tartozó SQL-adatbázisok között.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Használhatom az adatszinkronizálást az éles adatbázisból származó adatok egy üres adatbázisba való kivetéséhez, majd szinkronizálni őket

Igen. A séma manuális létrehozása az új adatbázis az eredeti alkalmazott azt. Miután létrehozta a sémát, a táblák hozzáadása a szinkronizálási csoport másolja az adatokat, és legyen szinkronizálva.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Az adatbázisok biztonsági mentése és visszaállítása SQL-adatszinkronizálás használatával

Nem javasoljuk, hogy a SQL-adatszinkronizálás használatával készítsen biztonsági másolatot az adatairól. Nem lehet biztonsági mentést készíteni és visszaállítani egy adott időpontra, mert SQL-adatszinkronizálás szinkronizálások nincsenek verziója. Emellett a SQL-adatszinkronizálás nem készít biztonsági mentést más SQL-objektumokról, például tárolt eljárásokról, és nem hajtja végre gyorsan a visszaállítási művelet megfelelőjét.

Az egyik ajánlott biztonsági mentési módszer: [Azure SQL Database-adatbázis másolása](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Az adatszinkronizálás titkosított táblákat és oszlopokat képes szinkronizálni

- Ha egy adatbázis Always Encrypted használ, csak azokat a táblákat és oszlopokat szinkronizálhatja *, amelyek nincsenek titkosítva* . A titkosított oszlopokban nem lehet szinkronizálni, mert az adatszinkronizálás nem tudja visszafejteni az adatokat.
- Ha egy oszlop oszlopszintű titkosítást (CLE) használ, szinkronizálhatja az oszlop, mindaddig, amíg a sor mérete kisebb, mint 24 Mb maximális méretét. Adatszinkronizálás kezeli a normál bináris adat titkosítja a kulcsot (CLE) oszlopban. Az egyéb szinkronizálási tagok található adatok visszafejtése, ugyanazt a tanúsítványt kell.

### <a name="is-collation-supported-in-sql-data-sync"></a>SQL-adatszinkronizálás támogatja a rendezést.

Igen. Az SQL Data Sync támogatja a rendezést a következő esetekben:

- Ha a kiválasztott szinkronizálási séma táblái még nem szerepelnek a központban vagy a tagok adatbázisaiban, akkor a szinkronizálási csoport telepítésekor a szolgáltatás automatikusan létrehozza a megfelelő táblákat és oszlopokat az üres célhely-adatbázisokban kiválasztott rendezési beállításokkal.
- Ha a táblák szinkronizálható, már létezik a hub és a tag-adatbázisokban, az SQL Data Sync megköveteli, hogy az elsődleges kulcsoszlopot ugyanazt a rendezést hubot és a tag adatbázis sikeres üzembe helyezéséhez a szinkronizálási csoport között. Nem vonatkoznak rendezési korlátozások oszlopokon kívül az elsődleges kulcsoszlopot.

### <a name="is-federation-supported-in-sql-data-sync"></a>Támogatott-e az összevonás SQL-adatszinkronizálás

Összevonási Gyökéradatbázis az SQL Data Sync szolgáltatás bármilyen korlátozás nélkül használható. Az összevont adatbázis-végpont nem adható hozzá a SQL-adatszinkronizálás aktuális verziójához.

## <a name="next-steps"></a>Következő lépések

### <a name="update-the-schema-of-a-synced-database"></a>A szinkronizált adatbázis sémájának frissítéséhez

Rendelkezik egy szinkronizálási csoportban egy adatbázis sémájának frissítéséhez? A séma módosításai nem replikálódnak automatikusan. Az egyes megoldások a következő cikkekben talál:

- [A séma változásainak az Azure-ban való replikálásának automatizálása SQL-adatszinkronizálás](sql-database-update-sync-schema.md)
- [Meglévő szinkronizálási csoportban lévő szinkronizálási séma frissítése a PowerShell használatával](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorozás és hibaelhárítás

A SQL-adatszinkronizálás a várt módon működik? Tevékenység figyelése és hibáinak elhárítása: az alábbi cikkeket:

- [Az Azure-SQL-adatszinkronizálás figyelése Azure Monitor naplókkal](sql-database-sync-monitor-oms.md)
- [Az Azure SQL Data Synckel hibaelhárítása](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Ismerkedés az Azure SQL Database szolgáltatással

További információ az SQL Database a következő cikkekben talál:

- [Az SQL Database áttekintése](sql-database-technical-overview.md)
- [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
