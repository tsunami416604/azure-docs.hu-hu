---
title: Az SQL Server-adatbázis áttelepítése az Azure SQL Database-ben egyetlen vagy készletezett adatbázis |} A Microsoft Docs
description: Ismerje meg, mi a helyzet az SQL Server-adatbázis áttelepítése az egyetlen, vagy az Azure SQL Database rugalmas készlet.
keywords: adatbázis-áttelepítés,sql server-adatbázis áttelepítése,adatbázis-áttelepítési eszközök,adatbázis áttelepítése,sql database áttelepítése
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: 2feece21644f8b79b4e5fc74331944cdddbdee4a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57996423"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Az SQL Server-adatbázis áttelepítése az Azure SQL Database

Ebben a cikkben megismerkedhet az áttelepítés egy SQL Server 2005 vagy újabb adatbázisok Azure SQL Database-ben egyetlen vagy készletezett adatbázis elsődleges módszere. Egy felügyelt példányába történő migrálás információkért lásd: [SQL Server-példány az Azure SQL Database felügyelt példányába történő áttelepítés](sql-database-managed-instance-migrate.md). Áttelepítési más platformokon az áttelepítéssel kapcsolatos információkért lásd: [Azure adatbázis-Migrálási útmutató](https://datamigration.microsoft.com/).

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Egyetlen, vagy egy készletezett adatbázis áttelepítése

Nincsenek két elsődleges módszere egy SQL Server 2005 vagy újabb adatbázisok Azure SQL Database-ben egyetlen vagy készletezett adatbázis-ba való migrálás. Az első módszer egyszerűbb, azonban jelentős állásidőt igényelhet a migrálás során. A második módszer bonyolultabb, viszont nem jár állásidővel a migrálás közben.

Mindkét esetben győződjön meg arról, hogy a forrásadatbázis kompatibilis az Azure SQL Database-adatbázishoz kell a [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). Az SQL Database V12-es közeledik [szolgáltatásparitást](sql-database-features.md) az SQL Serverrel, a kiszolgálószintű és adatbázisok közötti műveletekkel kapcsolatos problémák megoldása. A [részben támogatott vagy nem támogatott funkciókra](sql-database-transact-sql-information.md) támaszkodó adatbázisokat és alkalmazásokat [némileg át kell alakítani](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues) ezen inkompatibilitások kijavítása érdekében, még mielőtt áttelepíthetné az SQL Server-adatbázist.

> [!NOTE]
> Nem SQL Server-adatbázis (beleértve a Microsoft Access-, Sybase-, MySQL Oracle- és DB2-adatbázisokat) az Azure SQL Database-be történő áttelepítéséhez lásd [az SQL Server áttelepítési segédet](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/) ismertető cikket.

## <a name="method-1-migration-with-downtime-during-the-migration"></a>1. módszer: Migrálás leállás az áttelepítés során

 Ez a módszer segítségével áttelepítheti az egyetlen vagy készletezett adatbázisként, ha Ön megengedhet valamennyi állásidőt vagy hajt végre egy teszt migrálás egy éles adatbázist a későbbi migráláshoz. Foglalkozó oktatóanyagért lásd: [egy SQL Server-adatbázis Migrálása](../dms/tutorial-sql-server-to-azure-sql.md).

Az alábbi lista tartalmazza az általános munkafolyamata az egy adott SQL Server adatbázis-migrálásra vagy egy készletezett adatbázis ezzel a módszerrel. Migrálás felügyelt példányra, lásd: [Migrálás felügyelt példányra](sql-database-managed-instance-migrate.md).

  ![VSSSDT áttelepítési ábra](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Mérje fel](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) az adatbázis kompatibilitását a legújabb verziójának használatával a [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. A szükséges javításokat Transact-SQL szkriptekként készítse elő.
3. Tranzakciós szempontból konzisztens másolatot készít a forrásadatbázis áttelepítés alatt álló vagy új tranzakciók során az áttelepítés történik a forrásadatbázis bekövetkezését halt. Módszerrel érhető el ez utóbbi lehetőség ügyfélkapcsolat létrehozása, vagy tartalmaznak egy [adatbázis-pillanatkép](https://msdn.microsoft.com/library/ms175876.aspx). Az áttelepítés után frissítse a kitűzött pont az áttelepítés után elvégzett módosításokat a migrált adatbázisok tranzakciós replikáció használatával is lehet. Lásd: [tranzakciós áttelepítéssel át](sql-database-single-database-migrate.md#method-2-use-transactional-replication).  
4. Helyezze üzembe a Transact-SQL szkripteket a javítások alkalmazásához az adatbázis másolatán.
5. [Migrate](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) az adatbázis másolatát egy új Azure SQL Database használatával a Data Migration Assistant szolgáltatást.

> [!NOTE]
> Ahelyett, hogy a DMA segítségével egy BACPAC-fájlba is használhatja. Lásd: [BACPAC-fájl importálása egy új Azure SQL Database](sql-database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Az adatátviteli teljesítmény optimalizálása migrálás közben

Az alábbi lista az importálási teljesítmény optimalizálására vonatkozó javaslatokat tartalmaz.

- Válassza ki a legmagasabb szolgáltatási rétegben, és a számítási méret a költségvetésének az átviteli teljesítmény maximalizálása érdekében. A migrálás után vertikális leskálázással pénzt takaríthat meg.
- Minimalizálja a távolságot a BACPAC-fájlt, és a Céladatközpont között.
- Tiltsa le az automatikus statisztikákat a migrálás alatt.
- Particionálja a táblákat és az indexeket.
- Vesse el, majd a folyamat befejezése után hozza létre újra az indexelt nézeteket.
- A ritkán lekérdezett előzményadatokat helyezze át másik adatbázisba, majd telepítse át ezeket az előzményadatokat egy külön Azure SQL Database-adatbázisba. Ezután lekérdezheti ezeket az előzményadatokat a [rugalmas lekérdezések](sql-database-elastic-query-overview.md) használatával.

### <a name="optimize-performance-after-the-migration-completes"></a>A teljesítmény optimalizálása a migrálás befejezése után

A migrálás befejezése után végezzen teljes vizsgálatot a [statisztikák frissítéséhez](https://msdn.microsoft.com/library/ms187348.aspx).

## <a name="method-2-use-transactional-replication"></a>2. módszer: Tranzakciós replikáció használata

Ha nem helyezheti üzemen kívül az SQL Server-adatbázist a migrálás ideje alatt, az SQL Server tranzakciós replikációját is használhatja a migráláshoz. A módszer használatának feltétele, hogy a forrásadatbázisnak meg kell felelnie a [tranzakciós replikáció követelményeinek](https://msdn.microsoft.com/library/mt589530.aspx), valamint kompatibilisnek kell lennie az Azure SQL Database-szel is. Az AlwaysOn az SQL-replikációval kapcsolatos további információkért lásd: [replikáció konfigurálása AlwaysOn rendelkezésre állási csoportok számára (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

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
  - Ekkor a teljesítményre gyakorolt hatás a kiszolgálón.
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
> SQL Database felügyelt példánya lehetővé teszi egy meglévő SQL Server-példány és az adatbázisokhoz való áttelepítését a kompatibilitási problémák minimális. Lásd: [mi egy felügyelt példányra](sql-database-managed-instance.md).

## <a name="next-steps"></a>További lépések

- Az Azure SQL EMEA Engineers blogon lévő szkripttel [monitorozhatja a Tempdb-adatbázis felhasználását a migrálás közben](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
- Az Azure SQL EMEA Engineers blogon lévő szkripttel [monitorozhatja az adatbázis tranzakciósnapló-területét a migrálás időtartama alatt](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0).
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- További információ a UTC idő kezeléséről a migrálás után: [Alapértelmezett időzóna módosítása a saját időzónájára](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/).
- További információ az adatbázis alapértelmezett nyelvének migrálás utáni módosításáról: [Az Azure SQL Database alapértelmezett nyelvének módosítása](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/).
