---
title: "SQL Server-adatbázis áttelepítése az Azure SQL Database-be | Microsoft Docs"
description: "Útmutató az SQL Server-adatbázisok felhőbeli Azure SQL Database-be történő migrálásához."
keywords: "adatbázis-áttelepítés,sql server-adatbázis áttelepítése,adatbázis-áttelepítési eszközök,adatbázis áttelepítése,sql database áttelepítése"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 9cf09000-87fc-4589-8543-a89175151bc2
ms.service: sql-database
ms.custom: migrate
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Active
ms.date: 11/07/2017
ms.author: carlrab
ms.openlocfilehash: 8a31ed948fe9387720db61018e0edded530cd900
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>SQL Server-adatbázis áttelepítése a felhőbeli SQL Database-be
Ebben a cikkben az SQL Server 2005 vagy újabb adatbázisok Azure SQL Database-be való migrálásának két elsődleges módszerét ismerheti meg. Az első módszer egyszerűbb, azonban jelentős állásidőt igényelhet a migrálás során. A második módszer bonyolultabb, viszont nem jár állásidővel a migrálás közben.

Mindkét esetben gondoskodnia kell arról, hogy kompatibilis legyen az Azure SQL Database használata-e a forrásadatbázis a [adatok áttelepítési Segéd (DMA-)](https://www.microsoft.com/download/details.aspx?id=53595). SQL Database 12-es közeledik [szolgáltatásparitás](sql-database-features.md) az SQL Server eltérő kiszolgálószintű és az adatbázisok közötti műveleteivel kapcsolatos problémákat. A [részben támogatott vagy nem támogatott funkciókra](sql-database-transact-sql-information.md) támaszkodó adatbázisokat és alkalmazásokat [némileg át kell alakítani](sql-database-cloud-migrate.md#resolving-database-migration-compatibility-issues) ezen inkompatibilitások kijavítása érdekében, még mielőtt áttelepíthetné az SQL Server-adatbázist.

> [!NOTE]
> Nem SQL Server-adatbázis (beleértve a Microsoft Access-, Sybase-, MySQL Oracle- és DB2-adatbázisokat) az Azure SQL Database-be történő áttelepítéséhez lásd [az SQL Server áttelepítési segédet](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/) ismertető cikket.
> 

## <a name="method-1-migration-with-downtime-during-the-migration"></a>1. módszer: Állásidővel járó migrálás

 Akkor használja ezt a módszert, ha megengedhet valamennyi állásidő, vagy ha tesztelési céllal migrál egy éles adatbázist a későbbi migráláshoz. Az oktatóanyagok esetén lásd: [egy SQL Server-adatbázis áttelepítése](sql-database-migrate-your-sql-server-database.md).

Az alábbi lista tartalmazza az SQL Server adatbázis-migrálásának általános munkafolyamatát ezzel a módszerrel.

  ![VSSSDT áttelepítési ábra](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Értékeléséhez](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) az adatbázis kompatibilitási legújabb verziójának használatával az [adatok áttelepítési Segéd (DMA-)](https://www.microsoft.com/download/details.aspx?id=53595).
2. A szükséges javításokat Transact-SQL szkriptekként készítse elő.
3. Tranzakciós úton konzisztens másolat készítése a forrásadatbázis áttelepítendő -, és győződjön meg arról, amely nincs további módosul a forráshely adatbázisára (vagy hogy kézzel alkalmazhatja az ilyen jellegű változtatásokat az áttelepítés befejezése után). Sokféleképpen elérheti az adatbázisok fokozatos leválasztását, az ügyfélkapcsolat letiltásától [adatbázis-pillanatfelvételének](https://msdn.microsoft.com/library/ms175876.aspx) létrehozásáig.
4. Helyezze üzembe a Transact-SQL szkripteket a javítások alkalmazásához az adatbázis másolatán.
5. [Telepítse át](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) az adatbázis másolatát egy új Azure SQL Database segítségével az adatok áttelepítési Segéd.

### <a name="optimizing-data-transfer-performance-during-migration"></a>Az adatátviteli teljesítmény optimalizálása migrálás közben 

Az alábbi lista az importálási teljesítmény optimalizálására vonatkozó javaslatokat tartalmaz.

* Válassza ki a költségvetésének megfelelő legmagasabb szolgáltatási szintet és teljesítményszintet a maximális átviteli teljesítmény érdekében. A migrálás után vertikális leskálázással pénzt takaríthat meg. 
* Minimalizálása érdekében a BACPAC fájl és a cél adatközpont közötti távolságot.
* Tiltsa le az automatikus statisztikákat a migrálás alatt.
* Particionálja a táblákat és az indexeket.
* Vesse el, majd a folyamat befejezése után hozza létre újra az indexelt nézeteket.
* A ritkán lekérdezett előzményadatokat helyezze át másik adatbázisba, majd telepítse át ezeket az előzményadatokat egy külön Azure SQL Database-adatbázisba. Ezután lekérdezheti ezeket az előzményadatokat a [rugalmas lekérdezések](sql-database-elastic-query-overview.md) használatával.

### <a name="optimize-performance-after-the-migration-completes"></a>A teljesítmény optimalizálása a migrálás befejezése után

A migrálás befejezése után végezzen teljes vizsgálatot a [statisztikák frissítéséhez](https://msdn.microsoft.com/library/ms187348.aspx).

## <a name="method-2-use-transactional-replication"></a>2. módszer: Tranzakciós replikáció használata

Ha nem helyezheti üzemen kívül az SQL Server-adatbázist a migrálás ideje alatt, az SQL Server tranzakciós replikációját is használhatja a migráláshoz. A módszer használatának feltétele, hogy a forrásadatbázisnak meg kell felelnie a [tranzakciós replikáció követelményeinek](https://msdn.microsoft.com/library/mt589530.aspx), valamint kompatibilisnek kell lennie az Azure SQL Database-szel is. Az AlwaysOn az SQL-replikációval kapcsolatos információkért lásd: [replikáció konfigurálása az Always On rendelkezésre állási csoportok (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

A megoldás használatához előfizetőként kell beállítania az Azure SQL Database-t a migrálni kívánt SQL Server-példányhoz. A tranzakciós replikáció terjesztője szinkronizálja az adatokat szinkronizálni kívánt adatbázisból (a közzétevőből), eközben új tranzakciók továbbra is történnek. 

Tranzakciós replikáció esetén minden adat- vagy sémamódosítás megjelenik az Azure SQL Database-ben. Amint készen áll a migrálásra a szinkronizálás befejeztével, módosítsa alkalmazásai kapcsolati sztringjét úgy, hogy az Azure SQL Database-re mutasson. Amikor a tranzakciós replikáció elvégezte az összes hátralévő forrásadatbázis-módosítást, valamint az összes alkalmazása az Azure-adatbázisra mutat, eltávolíthatja a tranzakciós replikációt. Mostantól az Azure SQL Database az éles rendszer.

 ![SeedCloudTR-diagram](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> A tranzakciós replikációval a forrásadatbázis részhalmazát is migrálhatja. Az Azure SQL Database-be replikált kiadvány a replikált adatbázis tábláinak részhalmaza is lehet. Minden replikált tábla esetén korlátozhatja az adatokat a sor- és/vagy oszlopok meghatározott részhalmazaira.
>

### <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migrálás az SQL Database-re a tranzakciós replikáció használatával – munkafolyamat

> [!IMPORTANT]
> Használja az SQL Server Management Studio legfrissebb verzióját, hogy kihasználhassa a Microsoft Azure és az SQL Database legújabb frissítései által nyújtott előnyöket. Az SQL Server Management Studio régebbi verzióiban az SQL Database nem állítható be előfizetőként. [Az SQL Server Management Studio frissítése](https://msdn.microsoft.com/library/mt238290.aspx).
> 

1. Terjesztés beállítása
   -  [SQL Server Management Studio (SSMS) használatával](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [Transact-SQL használatával](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. Kiadvány létrehozása
   -  [SQL Server Management Studio (SSMS) használatával](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [Transact-SQL használatával](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Előfizetés létrehozása
   -  [SQL Server Management Studio (SSMS) használatával](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [Transact-SQL használatával](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

### <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>Tippek és különbségek az SQL Database-be való migráláshoz

1. Helyi terjesztő használata 
   - Ekkor a teljesítményre gyakorolt hatás a kiszolgálón. 
   - Ha a teljesítményre kifejtett hatás mértéke nem elfogadható, használhat másik kiszolgálót, ez azonban megnehezíti a kezelést és az adminisztrációt.
2. Pillanatképmappa kiválasztásakor győződjön meg arról, hogy a mappában az összes replikálni kívánt tábla BCP-je elfér. 
3. A pillanatkép létrehozása a folyamat befejezéséig lezárja a hozzá társított táblákat, így megfelelően időzítse a pillanatkép-készítést. 
4. Az Azure SQL Database csak a leküldéses előfizetéseket támogatja. Előfizetőket csak a forrásadatbázisból adhat hozzá.

## <a name="resolving-database-migration-compatibility-issues"></a>Adatbázis-migrálás kompatibilitási problémáinak megoldása
Sokféle kompatibilitási problémával találkozhat a forrásadatbázis SQL Server-verziójától és az áttelepített adatbázis összetettségétől függően. Az SQL Server korábbi verziói több kompatibilitási problémával rendelkeznek. A következő forrásanyagokat érdemes használnia a keresőmotorokban végzett internetes keresés mellett:

* [Az Azure SQL Database-ben nem támogatott SQL Server-adatbázisfunkciók](sql-database-transact-sql-information.md)
* [Megszűnt adatbázismotor-funkció az SQL Server 2016-ban](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Megszűnt adatbázismotor-funkció az SQL Server 2014-ben](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Megszűnt adatbázismotor-funkció az SQL Server 2012-ben](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Megszűnt adatbázismotor-funkció az SQL Server 2008 R2-ben](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Megszűnt adatbázismotor-funkció az SQL Server 2005-ben](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Az internetes keresés és a forrásanyagok használata mellett érdemes felkeresnie az [MSDN SQL Server közösségi fórumait](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) vagy a [StackOverflow](http://stackoverflow.com/) oldalt.

## <a name="next-steps"></a>Következő lépések
* Az Azure SQL EMEA Engineers blogon lévő szkripttel [monitorozhatja a Tempdb-adatbázis felhasználását a migrálás közben](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
* Az Azure SQL EMEA Engineers blogon lévő szkripttel [monitorozhatja az adatbázis tranzakciósnapló-területét a migrálás időtartama alatt](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0).
* További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* További információ a UTC idő kezeléséről a migrálás után: [Alapértelmezett időzóna módosítása a saját időzónájára](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/).
* További információ az adatbázis alapértelmezett nyelvének migrálás utáni módosításáról: [Az Azure SQL Database alapértelmezett nyelvének módosítása](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/).


