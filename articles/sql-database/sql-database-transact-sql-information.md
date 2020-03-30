---
title: A T-SQL-különbségek feloldása-áttelepítés
description: Nem teljes mértékben támogatott Transact-SQL-utasítások az Azure SQL Database-ben
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: e0870ac9dc818ca07e149421b486136c76dd61a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208819"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Transact-SQL különbségek feloldása az SQL-adatbázisba való áttelepítés során

Amikor az adatbázist az SQL Serverről az Azure SQL Serverkiszolgálóra [telepíti át,](sql-database-single-database-migrate.md) előfordulhat, hogy az sql server áttelepítése előtt szükség van némi újratervezésre. Ez a cikk útmutatást nyújt mind az újratervezés elvégzéséhez, mind az újratervezés alapvető okainak megértéséhez. Az inkompatibilitás észleléséhez használja az [Adatáttelepítési segédet (DMA).](https://www.microsoft.com/download/details.aspx?id=53595)

## <a name="overview"></a>Áttekintés

Az alkalmazások által használt Transact-SQL legtöbb szolgáltatás teljes mértékben támogatott a Microsoft SQL Server és az Azure SQL Database rendszerben. Az SQL alapvető összetevői, például az adattípusok, operátorok, karakterláncok, aritmetikai, logikai és kurzorfüggvények például azonos módon működnek az SQL Server és az SQL Database rendszerben. Van azonban néhány T-SQL különbség a DDL (adatdefiníciós nyelv) és a DML (adatmanipulációs nyelv) elemekben, amelyek csak részben támogatott T-SQL-utasításokat és lekérdezéseket eredményeznek (amelyeket a cikk későbbi részében tárgyalunk).

Emellett vannak olyan funkciók és szintaxis, amely egyáltalán nem támogatott, mert az Azure SQL Database úgy tervezték, hogy elkülönítse a funkciókat a fő adatbázis és az operációs rendszer függőségeitől. Mint ilyen, a legtöbb kiszolgálószintű tevékenység nem megfelelő az SQL Database számára. A T-SQL utasítások és beállítások nem érhetők el, ha kiszolgálószintű beállításokat, operációsrendszer-összetevőket vagy fájlrendszer-konfigurációt adnak meg. Ha ilyen képességekre van szükség, egy megfelelő alternatíva gyakran elérhető valamilyen más módon az SQL Database vagy egy másik Azure-szolgáltatás vagy szolgáltatás.

Például a magas rendelkezésre állás az Azure SQL Database beépített technológia hasonló [mindig rendelkezésre állási csoportok.](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) A rendelkezésre állási csoportokhoz kapcsolódó T-SQL-utasításokat az SQL Database nem támogatja, és az Always On availability Groups dinamikus felügyeleti nézetei is nem támogatottak.

Az SQL Database által támogatott és nem támogatott szolgáltatások listáját az Azure SQL Database szolgáltatás összehasonlítása című [témakörben tetszetős.](sql-database-features.md) Az ezen az oldalon található lista kiegészíti az irányelvek és a cikk, és összpontosít Transact-SQL nyilatkozatokat.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Transact-SQL szintaktikai utasítások részleges eltérésekkel

Az alapvető DDL (adatdefiníciós nyelv) utasítások elérhetők, de néhány DDL-utasítás a lemezelhelyezéshez és a nem támogatott funkciókhoz kapcsolódó kiterjesztéseket tartalmaz.

- Create és ALTER ADATBÁZIS utasítások több mint három tucat lehetőség. Az utasítások közé tartoznak a fájlelhelyezés, a FILESTREAM és a szolgáltatásközvetítői beállítások, amelyek csak az SQL Server kiszolgálóra vonatkoznak. Ez nem feltétlenül számít, ha az áttelepítés előtt adatbázisokat hoz létre, de ha adatbázisokat létrehozó T-SQL-kódot telepít át, akkor össze kell hasonlítania a [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) és az SQL Server szintaxisát a [CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) rendszerben, hogy minden használt beállítás támogatott legyen. CREATE DATABASE FOR Azure SQL Database is rendelkezik szolgáltatási cél és rugalmas méretezési beállításokat, amelyek csak az SQL Database.
- A CREATE és alter table utasítások FileTable beállításokkal rendelkeznek, amelyek nem használhatók az SQL Database-ben, mert a FILESTREAM nem támogatott.
- A CREATE és az ALTER bejelentkezési utasítások támogatottak, de az SQL Database nem kínálja az összes lehetőséget. Az adatbázis hordozhatóbbá tételéhez az SQL Database a bejelentkezések helyett a tartalmazott adatbázis-felhasználók használatát javasolja, amikor csak lehetséges. További információ: [CREATE/ALTER LOGIN](https://docs.microsoft.com/sql/t-sql/statements/alter-login-transact-sql) and [Manage logins and users](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Az Azure SQL Database nem támogatja a Transact-SQL szintaxist

Az [Azure SQL Database szolgáltatás-összehasonlításában](sql-database-features.md)ismertetett nem támogatott funkciókhoz kapcsolódó Transact-SQL utasítások on kívül a következő utasítások és utasítások csoportjai nem támogatottak. Mint ilyen, ha az áttelepítendő adatbázis az alábbi szolgáltatások bármelyikét használja, a T-SQL újratervezésével távolítsa el ezeket a T-SQL funkciókat és utasításokat.

- Rendszerobjektumok rendezése
- Kapcsolattal kapcsolatos: Végpontutasítások. Az SQL Database nem támogatja a Windows-hitelesítést, de támogatja a hasonló Azure Active Directory-hitelesítést. Bizonyos hitelesítési típusokhoz az SSMS legújabb verziója szükséges. További tudnivalókat a [Csatlakozás az SQL Database vagy az SQL Data Warehouse szolgáltatáshoz Azure Active Directory-hitelesítéssel](sql-database-aad-authentication.md) című cikkben találhat.
- Adatbázisközi lekérdezések három vagy négy résznévvel. (A csak olvasható adatbázisközi lekérdezéseket a [rugalmas adatbázis lekérdezése](sql-database-elastic-query-overview.md) funkció támogatja.)
- Tulajdonjog adatbázisközi láncolása, `TRUSTWORTHY` beállítása
- Az `EXECUTE AS LOGIN` helyett használja az 'EXECUTE AS USER' utasítást.
- A bővíthető kulcskezelés kivételével a titkosítás funkció támogatott
- Eseményszervezés: Események, eseményértesítések, lekérdezési értesítések
- Fájlelhelyezés: A Microsoft Azure által automatikusan kezelt adatbázisfájl-elhelyezéshez, mérethez és adatbázisfájlokhoz kapcsolódó szintaxis.
- Magas rendelkezésre állás: A magas rendelkezésre álláshoz kapcsolódó szintaxis, amelyet a Microsoft Azure-fiókján keresztül kezel. Ide tartozik a biztonsági mentéshez, visszaállításhoz, Always On funkcióhoz, adatbázis-tükrözéshez, naplóküldéshez, helyreállítási módokhoz kapcsolódó szintaxis.
- Naplóolvasó: Szintaxis, amely a naplóolvasóra támaszkodik, amely nem érhető el az SQL Database-ben: Leküldéses replikáció, Adatrögzítés módosítása. Az SQL Database a leküldéses replikációs tétel előfizetője lehet.
- Funkciók: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardver: A hardverrel kapcsolatos kiszolgálóbeállításokkal kapcsolatos szintaxis: például memória, munkaszálak, processzoraffinitás, nyomkövetési jelzők. Használja a szolgáltatási szintek és a számítási méretek helyett.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET` `OPENDATASOURCE`, , és négyrészű nevek
- .NET Framework: CLR-integráció az SQL Server kiszolgálóval
- Szemantikus keresés
- Kiszolgálóhitelesítő adatok: Használja [inkább az adatbázis hatókörrel rendelkező hitelesítő adatait.](https://msdn.microsoft.com/library/mt270260.aspx)
- Kiszolgálószintű elemek: Kiszolgálói `sys.login_token`szerepkörök, . A `GRANT`, `REVOKE`, és `DENY` kiszolgálószintű engedélyek nem érhetők el, bár van köztük olyan, amelyet adatbázisszintű engedélyek helyettesítenek. Néhány hasznos kiszolgálószintű dinamikus felügyeleti nézet rendelkezik egyenértékű adatbázisszintű dinamikus felügyeleti nézettel.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` lehetőségek és `RECONFIGURE`. Egyes lehetőségek elérhetők az [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx) (adatbázis-hatókörű konfiguráció változtatása) utasítással.
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: Az SQL Server Agent vagy az MSDB adatbázisra támaszkodó szintaxis: riasztások, operátorok, központi felügyeleti kiszolgálók. Használjon helyette parancsfájlkezelőt, mint például az Azure PowerShell.
- SQL Server naplózás: Használja az SQL Database naplózását helyette.
- SQL Server-nyomkövetés
- Nyomkövetési jelzők: Néhány nyomkövetési jelzőelem kompatibilitási módba lett helyezve.
- Transact-SQL-hibakeresés
- Eseményindítók: kiszolgálói hatókörű vagy bejelentkezési eseményindítók
- `USE` utasítás: Ha másik adatbázissal szeretné felváltani az adatbázis-környezetet, új kapcsolódást kell létesítenie az új adatbázissal.

## <a name="full-transact-sql-reference"></a>A Transact-SQL teljes leírása

A Transact-SQL nyelvtanról, a használatról és a példákról további információt az SQL Server Books Online [Transact-SQL Reference (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx) című részében talál.

### <a name="about-the-applies-to-tags"></a>Az „Érvényes” címkék

A Transact-SQL hivatkozás az SQL Server 2008-as verzióival kapcsolatos cikkeket tartalmaz. A cikk címe alatt van egy ikonsáv, amely felsorolja a négy SQL Server platformot, és jelzi az alkalmazhatóságot. A rendelkezésre állási csoportok például az SQL Server 2012-ben jelentek meg. A [CREATE AVAILABILITY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) cikk azt jelzi, hogy a utasítás az SQL Server kiszolgálóra vonatkozik **(2012-től kezdődően).** Az utasítás nem érvényes az SQL Server 2008, az SQL Server 2008 R2, az Azure SQL Database, az Azure SQL Data Warehouse és a Parallel Data Warehouse esetében.

Bizonyos esetekben egy árucikk általános tárgya használható egy termékben, de kisebb különbségek vannak a termékek között. A különbségeket adott esetben a cikk középpontjaiban tüntetik fel. Bizonyos esetekben egy árucikk általános tárgya használható egy termékben, de kisebb különbségek vannak a termékek között. A különbségeket adott esetben a cikk középpontjaiban tüntetik fel. Például a CREATE TRIGGER cikk elérhető az SQL Database-ben. A kiszolgálószintű eseményindítók **ALL SERVER** opciója azonban azt jelzi, hogy a kiszolgálószintű eseményindítók nem használhatók az SQL Database-ben. Ehelyett használjon adatbázisszintű eseményindítókat.

## <a name="next-steps"></a>További lépések

Az SQL Database által támogatott és nem támogatott szolgáltatások listáját az Azure SQL Database szolgáltatás összehasonlítása című [témakörben tetszetős.](sql-database-features.md) Az ezen az oldalon található lista kiegészíti az irányelvek és a cikk, és összpontosít Transact-SQL nyilatkozatokat.
