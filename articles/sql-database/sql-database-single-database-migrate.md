---
title: SQL Server adatbázis áttelepítése egyetlen/készletezett adatbázisba Azure SQL Database
description: Ismerje meg, hogyan SQL Server adatbázis-áttelepítést egy önálló adatbázisba vagy egy rugalmas készletbe Azure SQL Database-ben.
keywords: adatbázis-áttelepítés,sql server-adatbázis áttelepítése,adatbázis-áttelepítési eszközök,adatbázis áttelepítése,sql database áttelepítése
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/11/2019
ms.openlocfilehash: 67030d14670ccc51c89a04863f8b39ab6a9bb183
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687182"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>SQL Server adatbázis áttelepítése Azure SQL Databasere

Ebből a cikkből megtudhatja, hogyan telepíthet egy SQL Server 2005-es vagy újabb verziójú adatbázist egyetlen vagy készletezett adatbázisba Azure SQL Database-ben. A felügyelt példányokra való áttelepítéssel kapcsolatos információkért lásd: [áttelepítés SQL Server példányra Azure SQL Database felügyelt példányra](sql-database-managed-instance-migrate.md). A más platformokról való áttelepítéssel kapcsolatos információkért lásd: az [Azure Database áttelepítési útmutatója](https://datamigration.microsoft.com/).

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrálás önálló adatbázisba vagy készletezett adatbázisba

A SQL Server 2005-es vagy újabb verziójú adatbázisok egyetlen vagy készletezett adatbázisba való áttelepítésére két elsődleges módszer áll rendelkezésre Azure SQL Database. Az első módszer egyszerűbb, azonban jelentős állásidőt igényelhet a migrálás során. A második módszer bonyolultabb, viszont nem jár állásidővel a migrálás közben.

Mindkét esetben biztosítania kell, hogy a forrásadatbázis kompatibilis legyen Azure SQL Database a [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)használatával. SQL Database V12-es verzióban a [szolgáltatások paritása](sql-database-features.md) SQL Server, a kiszolgálói szintű és az adatbázisok közötti műveletekkel kapcsolatos problémák kivételével. A [részben támogatott vagy nem támogatott funkciókra](sql-database-transact-sql-information.md) támaszkodó adatbázisokat és alkalmazásokat [némileg át kell alakítani](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues) ezen inkompatibilitások kijavítása érdekében, még mielőtt áttelepíthetné az SQL Server-adatbázist.

> [!NOTE]
> Nem SQL Server-adatbázis (beleértve a Microsoft Access-, Sybase-, MySQL Oracle- és DB2-adatbázisokat) az Azure SQL Database-be történő áttelepítéséhez lásd [az SQL Server áttelepítési segédet](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/) ismertető cikket.

## <a name="method-1-migration-with-downtime-during-the-migration"></a>1\. módszer: Állásidővel járó migrálás

 Ezzel a módszerrel az áttelepítést egy vagy készletezett adatbázisba végezheti el, ha bizonyos állásidőt szeretne biztosítani, vagy egy éles adatbázis tesztelési célú áttelepítését hajtja végre a későbbi áttelepítéshez. Oktatóanyagért lásd: [SQL Server-adatbázis migrálása](../dms/tutorial-sql-server-to-azure-sql.md).

A következő lista az egyetlen vagy készletezett adatbázis SQL Server adatbázis-áttelepítésének általános munkafolyamatát tartalmazza ezzel a módszerrel. A felügyelt példányra való áttelepítéssel kapcsolatban lásd: [Migrálás felügyelt példányra](sql-database-managed-instance-migrate.md).

  ![VSSSDT áttelepítési ábra](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. A [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)legújabb verziójának használatával [mérje](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) fel az adatbázis kompatibilitását.
2. A szükséges javításokat Transact-SQL szkriptekként készítse elő.
3. Végezze el az áttelepített forrásadatbázis tranzakciós szempontból konzisztens másolatát, vagy állítson le új tranzakciókat a forrás-adatbázisból az áttelepítés során. Az utóbbi lehetőség végrehajtásának módszerei közé tartozik az Ügyfélkapcsolat letiltása vagy az [adatbázis-pillanatkép](https://msdn.microsoft.com/library/ms175876.aspx)létrehozása. Az áttelepítés után a tranzakciós replikáció használatával frissítheti az áttelepített adatbázisokat az áttelepítéshez szükséges kivágási pont utáni változásokkal. Lásd: [a Migrálás tranzakciós áttelepítés használatával](sql-database-single-database-migrate.md#method-2-use-transactional-replication).  
4. Helyezze üzembe a Transact-SQL szkripteket a javítások alkalmazásához az adatbázis másolatán.
5. [Telepítse át](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) az adatbázis másolatát egy új Azure SQL Databasera a Data Migration Assistant használatával.

> [!NOTE]
> A DMA használata helyett használhat BACPAC-fájlt is. Lásd: [BACPAC-fájl importálása új Azure SQL Databaseba](sql-database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Az adatátviteli teljesítmény optimalizálása migrálás közben

Az alábbi lista az importálási teljesítmény optimalizálására vonatkozó javaslatokat tartalmaz.

- Válassza ki a legmagasabb szolgáltatási szintet és számítási méretet, amelyet a költségkeret az átvitel teljesítményének maximalizálására tesz lehetővé. A migrálás után vertikális leskálázással pénzt takaríthat meg.
- Csökkentse a BACPAC-fájl és a cél adatközpont közötti távolságot.
- Tiltsa le az automatikus statisztikákat a migrálás alatt.
- Particionálja a táblákat és az indexeket.
- Vesse el, majd a folyamat befejezése után hozza létre újra az indexelt nézeteket.
- A ritkán lekérdezett előzményadatokat helyezze át másik adatbázisba, majd telepítse át ezeket az előzményadatokat egy külön Azure SQL-adatbázisba. Ezután lekérdezheti ezeket az előzményadatokat a [rugalmas lekérdezések](sql-database-elastic-query-overview.md) használatával.

### <a name="optimize-performance-after-the-migration-completes"></a>A teljesítmény optimalizálása a migrálás befejezése után

A migrálás befejezése után végezzen teljes vizsgálatot a [statisztikák frissítéséhez](https://msdn.microsoft.com/library/ms187348.aspx).

## <a name="method-2-use-transactional-replication"></a>2\. módszer: Tranzakciós replikáció használata

Ha nem helyezheti üzemen kívül az SQL Server-adatbázist a migrálás ideje alatt, az SQL Server tranzakciós replikációját is használhatja a migráláshoz. A módszer használatának feltétele, hogy a forrásadatbázisnak meg kell felelnie a [tranzakciós replikáció követelményeinek](https://msdn.microsoft.com/library/mt589530.aspx), valamint kompatibilisnek kell lennie az Azure SQL Database-szel is. További információ a mindig bekapcsolt SQL-replikációról: [replikáció konfigurálása always on rendelkezésre állási csoportokhoz (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

A megoldás használatához előfizetőként kell beállítania az Azure SQL Database-t a migrálni kívánt SQL Server-példányhoz. A tranzakciós replikáció terjesztője szinkronizálja az adatokat szinkronizálni kívánt adatbázisból (a közzétevőből), eközben új tranzakciók továbbra is történnek.

Tranzakciós replikáció esetén minden adat- vagy sémamódosítás megjelenik az Azure SQL Database-ben. Amint készen áll a migrálásra a szinkronizálás befejeztével, módosítsa alkalmazásai kapcsolati sztringjét úgy, hogy az Azure SQL Database-re mutasson. Amikor a tranzakciós replikáció elvégezte az összes hátralévő forrásadatbázis-módosítást, valamint az összes alkalmazása az Azure-adatbázisra mutat, eltávolíthatja a tranzakciós replikációt. Mostantól az Azure SQL Database az éles rendszer.

 ![SeedCloudTR-diagram](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> A tranzakciós replikációval a forrásadatbázis részhalmazát is migrálhatja. Az Azure SQL Database-be replikált kiadvány a replikált adatbázis tábláinak részhalmaza is lehet. Minden replikált tábla esetén korlátozhatja az adatokat a sor- és/vagy oszlopok meghatározott részhalmazaira.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migrálás az SQL Database-re a tranzakciós replikáció használatával – munkafolyamat

> [!IMPORTANT]
> Használja az SQL Server Management Studio legfrissebb verzióját, hogy kihasználhassa a Microsoft Azure és az SQL Database legújabb frissítései által nyújtott előnyöket. Az SQL Server Management Studio régebbi verzióiban az SQL Database nem állítható be előfizetőként. [Az SQL Server Management Studio frissítése](https://msdn.microsoft.com/library/mt238290.aspx).

1. Terjesztés beállítása
   - [SQL Server Management Studio (SSMS) használatával](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   - [Transact-SQL használatával](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)

2. Kiadvány létrehozása
   - [SQL Server Management Studio (SSMS) használatával](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   - [Transact-SQL használatával](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Előfizetés létrehozása
   - [SQL Server Management Studio (SSMS) használatával](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   - [Transact-SQL használatával](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

Tippek és különbségek az SQL Database-be való migráláshoz

- Helyi terjesztő használata
  - Ennek hatására a-kiszolgáló teljesítményére hatással lehet.
  - Ha a teljesítményre kifejtett hatás mértéke nem elfogadható, használhat másik kiszolgálót, ez azonban megnehezíti a kezelést és az adminisztrációt.
- Pillanatképmappa kiválasztásakor győződjön meg arról, hogy a mappában az összes replikálni kívánt tábla BCP-je elfér.
- A pillanatkép létrehozása a folyamat befejezéséig lezárja a hozzá társított táblákat, így megfelelően időzítse a pillanatkép-készítést.
- Az Azure SQL Database csak a leküldéses előfizetéseket támogatja. Előfizetőket csak a forrásadatbázisból adhat hozzá.

## <a name="resolving-database-migration-compatibility-issues"></a>Adatbázis-migrálás kompatibilitási problémáinak megoldása

Sokféle kompatibilitási problémával találkozhat a forrásadatbázis SQL Server-verziójától és az áttelepített adatbázis összetettségétől függően. Az SQL Server korábbi verziói több kompatibilitási problémával rendelkeznek. A következő forrásanyagokat érdemes használnia a keresőmotorokban végzett internetes keresés mellett:

- [Az Azure SQL Database-ben nem támogatott SQL Server-adatbázisfunkciók](sql-database-transact-sql-information.md)
- [Megszűnt adatbázismotor-funkció az SQL Server 2016-ban](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Megszűnt adatbázismotor-funkció az SQL Server 2014-ben](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Megszűnt adatbázismotor-funkció az SQL Server 2012-ben](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Megszűnt adatbázismotor-funkció az SQL Server 2008 R2-ben](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Megszűnt adatbázismotor-funkció az SQL Server 2005-ben](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Az internetes keresés és a forrásanyagok használata mellett érdemes felkeresnie az [MSDN SQL Server közösségi fórumait](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) vagy a [StackOverflow](https://stackoverflow.com/) oldalt.

> [!IMPORTANT]
> SQL Database felügyelt példány lehetővé teszi, hogy egy meglévő SQL Server példányt és annak adatbázisait minimálisan, kompatibilitási problémák nélkül áttelepítse. Lásd: [Mi az a felügyelt példány](sql-database-managed-instance.md).

## <a name="next-steps"></a>További lépések

- Az Azure SQL EMEA Engineers blogon lévő szkripttel [monitorozhatja a Tempdb-adatbázis felhasználását a migrálás közben](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
- Az Azure SQL EMEA Engineers blogon lévő szkripttel [monitorozhatja az adatbázis tranzakciósnapló-területét a migrálás időtartama alatt](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0).
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- További információ a UTC idő kezeléséről a migrálás után: [Alapértelmezett időzóna módosítása a saját időzónájára](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/).
- További információ az adatbázis alapértelmezett nyelvének migrálás utáni módosításáról: [Az Azure SQL Database alapértelmezett nyelvének módosítása](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/).
