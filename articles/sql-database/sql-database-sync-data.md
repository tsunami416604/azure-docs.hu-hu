---
title: Adatszinkronizálás
description: Ez az áttekintés bemutatja az Azure SQL-adatszinkronizálás
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
ms.openlocfilehash: d69378b2e791732fb478a66f226c6269e2c515f3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820822"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Az adatszinkronizálás több Felhőbeli és helyszíni adatbázis között SQL-adatszinkronizálás

A SQL-adatszinkronizálás Azure SQL Database-on alapuló szolgáltatás, amely lehetővé teszi, hogy szinkronizálja a kétirányúan kiválasztott adatokat több SQL-adatbázis és SQL Server-példány között.

> [!IMPORTANT]
> Az Azure SQL-adatszinkronizálás jelenleg **nem** támogatja Azure SQL Database felügyelt példányok használatát.

## <a name="when-to-use-data-sync"></a>Mikor kell használni az adatszinkronizálást

Az adatok szinkronizálása olyan esetekben hasznos, amikor az adatoknak naprakészen kell tartaniuk több Azure SQL-adatbázis vagy SQL Server-adatbázis között. Az adatszinkronizálás fő felhasználási esetei:

- **Hibrid adatszinkronizálás:** Az adatszinkronizálással megtarthatja a helyszíni adatbázisok és az Azure SQL-adatbázisok közötti szinkronizálást a hibrid alkalmazások engedélyezéséhez. Ez a képesség olyan ügyfelek számára lehet fellebbezni, akik a felhőbe való áttérést fontolgatják, és az Azure-ban szeretnék üzembe helyezni az alkalmazásaikat.
- **Elosztott alkalmazások:** Sok esetben előnyös a különböző számítási feladatok különböző adatbázisokban való elkülönítése. Ha például nagy üzemi adatbázissal rendelkezik, de az adatokon jelentéskészítési vagy elemzési feladatokat is futtatnia kell, akkor hasznos, ha egy második adatbázissal rendelkezik ehhez a további számítási feladathoz. Ez a megközelítés csökkentheti a teljesítményre gyakorolt hatást az éles munkaterhelésen. Az adatszinkronizálás használatával megtarthatja a két adatbázis szinkronizálását.
- **Globálisan elosztott alkalmazások:** Számos vállalat számos régiót és akár több országot/régiót is kiterjedhet. A hálózati késés csökkentése érdekében a legjobb, ha az adatai egy régióhoz közel vannak. Az adatszinkronizálással könnyedén megtarthatja az adatbázisokat a világ különböző régióiban szinkronizálva.

Az adatok szinkronizálása nem az előnyben részesített megoldás a következő esetekben:

| Forgatókönyv | Néhány ajánlott megoldás |
|----------|----------------------------|
| Vészhelyreállítás | [Azure geo-redundáns biztonsági mentések](sql-database-automated-backups.md) |
| Olvasási skála | [Csak olvasható replikák használata az írásvédett lekérdezési feladatok terheléselosztásához (előzetes verzió)](sql-database-read-scale-out.md) |
| ETL (OLTP – OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) vagy [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Áttelepítés a helyszíni SQL Serverról a Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>A SQL-adatszinkronizálás áttekintése

Az adatszinkronizálás a szinkronizálási csoport fogalmán alapul. A szinkronizálási csoport a szinkronizálni kívánt adatbázisok egy csoportja.

Az adatszinkronizálás sugarasan és küllős topológiával szinkronizálja az adatokat. A szinkronizálási csoportban lévő adatbázisok egyikét a hub-adatbázisként kell meghatároznia. A többi adatbázis a tagok adatbázisai. A szinkronizálás csak a hub és az egyes tagok között történik.

- A **hub-adatbázisnak** Azure SQL Databasenak kell lennie.
- A **tag-adatbázisok** lehetnek SQL-adatbázisok, helyszíni SQL Server adatbázisok vagy SQL Server példányok az Azure Virtual Machines szolgáltatásban.
- A **szinkronizálási adatbázis** tartalmazza a metaadatokat és a naplót az adatok szinkronizálásához. A szinkronizálási adatbázisnak egy olyan Azure SQL Database kell lennie, amely ugyanabban a régióban található, mint a hub-adatbázis. A szinkronizálási adatbázis az ügyfél által létrehozott és az ügyfél tulajdonában van.

> [!NOTE]
> Ha helyszíni adatbázist használ tagként adatbázisként, [telepítenie és konfigurálnia kell egy helyi szinkronizáló ügynököt](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Adatszinkronizálás az adatbázisok között](media/sql-database-sync-data/sync-data-overview.png)

A szinkronizálási csoport a következő tulajdonságokkal rendelkezik:

- A **szinkronizálási séma** leírja, hogy mely adatokat szinkronizálja a rendszer.
- A **szinkronizálás iránya** lehet kétirányú, vagy csak egy irányba lehet flow. Vagyis a szinkronizálás iránya lehet a *tag*, a tag vagy a két *csomópont*is.
- A szinkronizálási **időköz** leírja, hogy milyen gyakran történjen a szinkronizálás.
- Az **ütközés-feloldási házirend** egy csoport szintű házirend, amely lehet *hub WINS* vagy *tag WINS*.

## <a name="how-does-data-sync-work"></a>Hogyan működik az adatszinkronizálás?

- **Az adatváltozások nyomon követése:** Az adatszinkronizálás nyomon követi a módosításokat INSERT, Update és DELETE eseményindítók használatával. A módosításokat a rendszer a felhasználói adatbázis egyik oldalsó táblájába rögzíti. Vegye figyelembe, hogy BULK INSERT alapértelmezés szerint nem indít el tüzet. Ha FIRE_TRIGGERS nincs megadva, a rendszer nem hajt végre beszúrt eseményindítókat. Adja hozzá a FIRE_TRIGGERS lehetőséget, hogy az adatszinkronizálás nyomon tudja követni ezeket a lapkákat. 
- **Az adatszinkronizálás:** Az adatszinkronizálást egy sugaras modellben tervezték. A központ egyenként szinkronizálja az egyes tagokat. A rendszer letölti a központból a tag felé irányuló módosításokat, majd feltölti a tagot a központba.
- **Ütközések feloldása:** Az adatszinkronizálás két lehetőséget kínál az ütközés feloldására, a *hub WINS* vagy a *tag WINS*használatára.
  - Ha a *hub WINS*lehetőséget választja, a hub módosításai mindig felülírják a tag módosításait.
  - Ha a *tag WINS*lehetőséget választja, a tag módosításai felülírják a központban lévő módosításokat. Ha több tag is van, akkor a végső érték attól függ, hogy melyik tag szinkronizálja először.

## <a name="compare-data-sync-with-transactional-replication"></a>Adatszinkronizálás összehasonlítása tranzakciós replikációval

| | Adatszinkronizálás | Tranzakciós replikáció |
|---|---|---|
| Előnyei | – Aktív-aktív támogatás<br/>– A helyszíni és a Azure SQL Database közötti kétirányú irányítás | – Alacsonyabb késés<br/>– Tranzakciós konzisztencia<br/>-Meglévő topológia újrafelhasználása az áttelepítés után |
| Hátrányai | – 5 perc vagy több késés<br/>– Nincs tranzakciós konzisztencia<br/>– Nagyobb teljesítményre gyakorolt hatás | – Nem lehet közzétenni Azure SQL Database önálló adatbázisból vagy készletezett adatbázisból<br/>– Magas karbantartási díj |
| | | |

## <a name="get-started-with-sql-data-sync"></a>Ismerkedés az SQL Data Sync szolgáltatással

### <a name="set-up-data-sync-in-the-azure-portal"></a>Adatszinkronizálás beállítása a Azure Portal

- [Az Azure SQL Data Sync beállítása](sql-database-get-started-sql-data-sync.md)
- Adatszinkronizálási ügynök – [Az Azure SQL-adatszinkronizálás adatszinkronizálási ügynöke](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Az adatszinkronizálás beállítása a PowerShell-lel

- [A PowerShell használata több Azure SQL-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
- [A PowerShell használata egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Az adatszinkronizálás ajánlott eljárásainak áttekintése

- [Ajánlott eljárások az Azure SQL Data Synchez](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Valami nem stimmel

- [Az Azure SQL Data Synckel hibaelhárítása](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Konzisztencia és teljesítmény

#### <a name="eventual-consistency"></a>Végleges konzisztencia

Mivel az adatszinkronizálás trigger-alapú, a tranzakciós konzisztencia nem garantált. A Microsoft garantálja, hogy az összes módosítást végül megtörténik, és az adatszinkronizálás nem okozza az adatvesztést.

#### <a name="performance-impact"></a>Teljesítményre gyakorolt hatás

Az adatszinkronizálás beszúrási, frissítési és törlési eseményindítókat használ a módosítások nyomon követéséhez. A változások nyomon követéséhez létrehozza a felhasználói adatbázis oldalsó táblázatait. Ezek a változás-követési tevékenységek hatással vannak az adatbázis számítási feladataira. Szükség esetén mérje fel a szolgáltatási szintet és a frissítést.

A szinkronizálási csoport létrehozása, frissítése és törlése során a kiépítés és a megszüntetés is hatással lehet az adatbázis teljesítményére. 

## <a name="sync-req-lim"></a>Követelmények és korlátozások

### <a name="general-requirements"></a>Általános követelmények

- Minden táblának rendelkeznie kell egy elsődleges kulccsal. Egyetlen sorban ne módosítsa az elsődleges kulcs értékét. Ha módosítania kell egy elsődleges kulcs értékét, törölje a sort, majd hozza létre újra az új elsődleges kulcs értékével. 

> [!IMPORTANT]
> Egy meglévő elsődleges kulcs értékének módosítása a következő hibás viselkedést eredményezi:   
>   - A központ és a tag közötti adatvesztés akkor is elvész, ha a szinkronizálás nem jelent problémát.
> - A szinkronizálás sikertelen lehet, mert a követési tábla nem létező sort tartalmaz a forrástól az elsődleges kulcs módosítása miatt.

- Engedélyezni kell a pillanatkép-elkülönítést. További információ: [Pillanatkép-elkülönítés az SQL Serveren](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Általános korlátozások

- Egy táblának nem lehet olyan azonosító oszlopa, amely nem az elsődleges kulcs.
- Az elsődleges kulcs nem rendelkezhet a következő adattípusokkal: sql_variant, Binary, varbinary, képet, XML. 
- Legyen óvatos, ha a következő adattípusokat használja elsődleges kulcsként, mert a támogatott pontosság csak a második: Time, datetime, datetime2, DateTimeOffset.
- Az objektumok (adatbázisok, táblák és oszlopok) nevei nem tartalmazhatják a nyomtatható karaktereket (.), a bal oldali szögletes zárójelet ([) vagy a jobb oldali szögletes zárójelet (]).
- Azure Active Directory hitelesítés nem támogatott.
- Az azonos nevű, de eltérő sémával (például dbo. Customers és Sales. Customers) rendelkező táblák nem támogatottak.
- A felhasználó által definiált adattípusú oszlopok nem támogatottak

#### <a name="unsupported-data-types"></a>Nem támogatott adattípusok

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (XML támogatott)
- Kurzor, RowVersion, időbélyeg, Hierarchyid

#### <a name="unsupported-column-types"></a>Nem támogatott oszlopok típusai

Az adatszinkronizálás nem tudja szinkronizálni a csak olvasható vagy a rendszer által létrehozott oszlopokat. Például:

- Számított oszlopok.
- Rendszer által generált oszlopok az ideiglenes táblákhoz.

#### <a name="limitations-on-service-and-database-dimensions"></a>A szolgáltatás-és adatbázis-méretek korlátozásai

| **Méretek**                                                      | **Korlát**              | **Áthidaló megoldás**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| A szinkronizált csoportok maximális száma, amelyhez bármely adatbázis tartozhat.       | 5                      |                             |
| Végpontok maximális száma egyetlen szinkronizálási csoportban              | 30                     |                             |
| A helyszíni végpontok maximális száma egyetlen szinkronizálási csoportban. | 5                      | Több szinkronizálási csoport létrehozása |
| Adatbázis, tábla, séma és oszlopnevek                       | 50 karakter/név |                             |
| Szinkronizálási csoport táblái                                          | 500                    | Több szinkronizálási csoport létrehozása |
| Egy szinkronizálási csoportba tartozó tábla oszlopai                              | 1000                   |                             |
| Tábla adatsorainak mérete                                        | 24 MB                  |                             |
| Minimális szinkronizálási időköz                                           | 5 perc              |                             |
|||
> [!NOTE]
> Egyetlen szinkronizálási csoportban akár 30 végpont is lehet, ha csak egy szinkronizálási csoport van. Ha több szinkronizálási csoport is van, akkor az összes szinkronizálási csoporton belüli végpontok száma nem haladhatja meg a 30-at. Ha egy adatbázis több szinkronizálási csoporthoz tartozik, a rendszer több végpontnak számít, nem egy.

## <a name="faq-about-sql-data-sync"></a>Gyakori kérdések SQL-adatszinkronizálás

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Mennyibe kerül a SQL-adatszinkronizálás szolgáltatás díja

Magának a SQL-adatszinkronizálás szolgáltatásnak nem számítunk fel díjat.  Azonban továbbra is adatátviteli díjat számítunk fel a SQL Database-példányon belüli és kívüli adatáthelyezésre. További információ: [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Mely régiók támogatják az adatszinkronizálást

SQL-adatszinkronizálás minden régióban elérhető.

### <a name="is-a-sql-database-account-required"></a>SQL Database fiók szükséges

Igen. A hub-adatbázis üzemeltetéséhez SQL Database-fiókkal kell rendelkeznie.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Használhatok adatszinkronizálást SQL Server helyszíni adatbázisok közötti szinkronizálásra

Nem közvetlenül. A SQL Server helyszíni adatbázisok között közvetve szinkronizálhat, azonban egy központi adatbázis létrehozásával az Azure-ban, majd a helyszíni adatbázisokat hozzáadja a szinkronizálási csoporthoz.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Használhatom az adatszinkronizálást a különböző előfizetésekhez tartozó SQL-adatbázisok közötti szinkronizáláshoz

Igen. A különböző előfizetések által birtokolt erőforrás-csoportokhoz tartozó SQL-adatbázisok között lehet szinkronizálni.

- Ha az előfizetések ugyanahhoz a bérlőhöz tartoznak, és Önnek van engedélye az összes előfizetéshez, a szinkronizálási csoportot a Azure Portal is konfigurálhatja.
- Ellenkező esetben a PowerShell használatával kell felvennie a különböző előfizetésekhez tartozó szinkronizálási tagokat.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china"></a>Használhatok adatszinkronizálást a különböző felhőkhöz tartozó SQL-adatbázisok (például az Azure Public Cloud és az Azure China) közötti szinkronizáláshoz

Igen. A különböző felhőkhöz tartozó SQL-adatbázisok közötti szinkronizáláshoz a PowerShell használatával kell felvennie a különböző előfizetésekhez tartozó szinkronizálási tagokat.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Használhatom az adatszinkronizálást az éles adatbázisból származó adatok egy üres adatbázisba való kivetéséhez, majd szinkronizálni őket

Igen. Hozza létre manuálisan a sémát az új adatbázisban az eredetiből származó parancsfájlok futtatásával. A séma létrehozása után vegye fel a táblákat egy szinkronizálási csoportba az adatmásoláshoz és a szinkronizálás megtartásához.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Az adatbázisok biztonsági mentése és visszaállítása SQL-adatszinkronizálás használatával

A SQL-adatszinkronizálás használatával nem ajánlott biztonsági másolatot készíteni az adatairól. Nem lehet biztonsági mentést készíteni és visszaállítani egy adott időpontra, mert SQL-adatszinkronizálás szinkronizálások nincsenek verziója. Emellett a SQL-adatszinkronizálás nem készít biztonsági mentést más SQL-objektumokról, például tárolt eljárásokról, és nem hajtja végre a visszaállítási műveletnek megfelelő műveletet.

Az egyik ajánlott biztonsági mentési módszer: [Azure SQL Database-adatbázis másolása](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Az adatszinkronizálás titkosított táblákat és oszlopokat képes szinkronizálni

- Ha egy adatbázis Always Encrypted használ, csak azokat a táblákat és oszlopokat szinkronizálhatja *, amelyek nincsenek titkosítva* . A titkosított oszlopok nem szinkronizálhatók, mert az adatszinkronizálás nem tudja visszafejteni az adattitkosítást.
- Ha egy oszlop oszlop szintű titkosítást (CLE) használ, szinkronizálhatja az oszlopot, ha a sor mérete nem éri el a maximálisan megengedett 24 MB-ot. Az adatszinkronizálási szolgáltatás a kulcs (CLE) által titkosított oszlopot normál bináris adatként kezeli. A többi szinkronizálási tag adatvisszafejtéséhez ugyanazzal a tanúsítvánnyal kell rendelkeznie.

### <a name="is-collation-supported-in-sql-data-sync"></a>SQL-adatszinkronizálás támogatja a rendezést.

Igen. A SQL-adatszinkronizálás a következő helyzetekben támogatja a rendezést:

- Ha a kiválasztott szinkronizálási séma táblái még nem szerepelnek a központban vagy a tagok adatbázisaiban, akkor a szinkronizálási csoport telepítésekor a szolgáltatás automatikusan létrehozza a megfelelő táblákat és oszlopokat az üres célhely-adatbázisokban kiválasztott rendezési beállításokkal.
- Ha a szinkronizálandó táblák már szerepelnek a központ és a tag adatbázisaiban is, SQL-adatszinkronizálás megköveteli, hogy az elsődleges kulcs oszlopai azonos rendezéssel rendelkezzenek a központi és a tagok adatbázisai között a szinkronizálási csoport sikeres telepítéséhez. Az elsődleges kulcs oszlopaitól eltérő oszlopok esetében nincsenek rendezési korlátozások.

### <a name="is-federation-supported-in-sql-data-sync"></a>Támogatott-e az összevonás SQL-adatszinkronizálás

Az összevonási gyökér adatbázisa korlátozás nélkül használható a SQL-adatszinkronizálás szolgáltatásban. Az összevont adatbázis-végpont nem adható hozzá a SQL-adatszinkronizálás aktuális verziójához.

## <a name="next-steps"></a>További lépések

### <a name="update-the-schema-of-a-synced-database"></a>Szinkronizált adatbázis sémájának frissítése

Frissítenie kell egy adatbázis sémáját egy szinkronizálási csoportban? A séma módosításait a rendszer nem replikálja automatikusan. Néhány megoldásért tekintse meg a következő cikkeket:

- [A séma változásainak az Azure-ban való replikálásának automatizálása SQL-adatszinkronizálás](sql-database-update-sync-schema.md)
- [Meglévő szinkronizálási csoportban lévő szinkronizálási séma frissítése a PowerShell használatával](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Figyelés és hibaelhárítás

A SQL-adatszinkronizálás a várt módon működik? Tevékenység figyelése és hibáinak elhárítása: az alábbi cikkeket:

- [Az Azure-SQL-adatszinkronizálás figyelése Azure Monitor naplókkal](sql-database-sync-monitor-oms.md)
- [Az Azure SQL Data Synckel hibaelhárítása](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Ismerkedés az Azure SQL Database szolgáltatással

A SQL Databaseról további információt a következő cikkekben talál:

- [Az SQL Database áttekintése](sql-database-technical-overview.md)
- [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
