---
title: T-SQL különbségek-áttelepítés – Azure SQL Database feloldása |} A Microsoft Docs
description: Nem teljes mértékben támogatott Transact-SQL-utasítások az Azure SQL Database-ben
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: carlrab
ms.openlocfilehash: 440605806915d515d2a60556a9c298b29e0fca8c
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45735428"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Transact-SQL különbségek feloldása az SQL Database-migráció során   
Amikor [az adatbázis migrálása](sql-database-cloud-migrate.md) SQL Serverről Azure SQL-kiszolgálón, azt tapasztalhatja, hogy az adatbázis futtatásához szükséges néhány újratervezéséhez előtt az SQL Server telepíthetők át. Ez a cikk segítséget nyújt a végrehajtása a újratervezéséhez és miért szükséges a újratervezéséhez az alapul szolgáló okok megértéséhez is útmutatást nyújt. Kompatibilitási problémák észleléséhez használja a [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Áttekintés
A legtöbb Transact-SQL funkcióival, mellyel az alkalmazások teljes mértékben támogatja a Microsoft SQL Server és az Azure SQL Database. Például az SQL-alapösszetevők például adattípusok, a operátorok, a karakterlánc, a aritmetikai, logikai, és a kurzor funkciók, munka azonos SQL Server és SQL Database. Azonban néhány T-SQL különbségek vannak a DDL (data-definíciós nyelv) és a T-SQL-utasításokkal, és csak részlegesen támogatott lekérdezések DML (adatok adatkezelési language) elemek (Ez a cikk későbbi részében bemutatjuk).

Emellett vannak bizonyos funkciók és a szintaxis, amely nem támogatott egyáltalán, mivel a Azure SQL Database az elkülöníteni a funkciókat a főadatbázis és az operációs rendszer. Emiatt a legtöbb kiszolgálói szintű tevékenység nem illik az SQL Database. T-SQL-utasítások és beállítások nem érhető el, ha kiszolgálói szintű beállításokat vagy operációsrendszer-összetevők, konfigurálása, vagy adja meg a fájlrendszer-konfigurációt. Ilyen képességek szükségesek, ha megfelelő alternatív gyakran érhető el bármilyen egyéb módon az SQL Database-ből vagy más Azure funkciót vagy szolgáltatást. 

Például magas rendelkezésre állás beépített Azure-ban, így Always On konfigurálása nem szükséges (bár a gyorsabb helyreállítás az aktív georeplikáció konfigurálása egy esetleges vészhelyzet esetén érdemes). Tehát rendelkezésre állási csoportokkal kapcsolatos T-SQL-utasítások nem támogatottak az SQL Database, és a kapcsolódó mindig a dinamikus felügyeleti nézeteket is nem támogatott.

A támogatott és nem az SQL Database által támogatott funkciók listáját lásd: [Azure SQL Database funkcióinak összehasonlítása](sql-database-features.md). A lista ezen az oldalon, irányelvek és funkciók cikk kiegészíti, és Transact-SQL-utasítások összpontosít.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Részleges eltéréssel Transact-SQL-szintaxis utasítások
A core (adatdefiníciós nyelv) DDL-utasítások, de néhány DDL-utasítások rendelkeznie bővítmények lemez elhelyezési kapcsolatos, és nem támogatott funkciókat. 

- LÉTREHOZÁS és az ALTER DATABASE utasítás több mint három tucat lehetősége van. Az utasítások közé tartozik a fájl elhelyezési FILESTREAM és csak érvényes SQL Server service broker beállítások. Ez előfordulhat, hogy nem számít, hogy adatbázisok létrehozására, mielőtt telepíti át, de ha az áttelepítés által létrehozott adatbázisok a T-SQL-kódot hasonlítsa [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) , SQL Server szintaxissal [létrehozása ADATBÁZIS (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) , hogy az összes beállítás használata támogatott. Az Azure SQL Database-adatbázis létrehozása a szolgáltatási cél és a csak az SQL Database rugalmas méretezési beállítások is rendelkezik.
- A létrehozás és az ALTER TABLE utasítást, amely nem használható az SQL Database, mert nem támogatott a FILESTREAM FileTable lehetőség áll rendelkezésére.
- A CREATE vagy ALTER login utasítás támogatott, de az SQL Database nem kínál lehetőségekről. Az adatbázis hordozhatóságának, az SQL Database arra ösztönzi a tartalmazottadatbázis-felhasználók használata helyett a bejelentkezéseket, amikor csak lehetséges. További információkért lásd: [CREATE/ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) és [szabályozása és adatbázis-hozzáférés](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Nem támogatott az Azure SQL Database Transact-SQL-szintaxis   
A leírt nem támogatott funkciók kapcsolatos Transact-SQL utasításokkal mellett [Azure SQL Database funkcióinak összehasonlítása](sql-database-features.md), az alábbi utasítások és utasításcsoportok, nem támogatottak. Mint ilyen az adatbázis kell áttelepíteni az alábbi funkciók bármelyike használ, ha újból mérnök a T-SQL-ezeket a T-SQL-funkciók és az utasításokat.

- Rendszerobjektumok rendezése
- Kapcsolatra vonatkozó: végponti utasítások. Az SQL Database nem támogatja a Windows-hitelesítést, de támogatja a hasonló Azure Active Directory-hitelesítést. Bizonyos hitelesítési típusokhoz az SSMS legújabb verziója szükséges. További tudnivalókat a [Csatlakozás az SQL Database vagy az SQL Data Warehouse szolgáltatáshoz Azure Active Directory-hitelesítéssel](sql-database-aad-authentication.md) című cikkben találhat.
- Adatbázisközi lekérdezések három vagy négy résznévvel. (A csak olvasható adatbázisközi lekérdezéseket a [rugalmas adatbázis lekérdezése](sql-database-elastic-query-overview.md) funkció támogatja.)
- Tulajdonjog adatbázisközi láncolása, `TRUSTWORTHY` beállítása
- Az `EXECUTE AS LOGIN` helyett használja az 'EXECUTE AS USER' utasítást.
- A bővíthető kulcskezelés kivételével a titkosítás funkció támogatott
- Eseménykezelési: Események, eseményértesítések, lekérdezésértesítések
- Fájl elhelyezési: adatbázisfájlok elhelyezéséhez, mérete és automatikusan a Microsoft Azure által felügyelt adatbázis-fájlok kapcsolódó szintaxis.
- Magas rendelkezésre állás: magas rendelkezésre állást, amely a Microsoft Azure-fiókján keresztül felügyelt kapcsolódó szintaxis. Ide tartozik a biztonsági mentéshez, visszaállításhoz, Always On funkcióhoz, adatbázis-tükrözéshez, naplóküldéshez, helyreállítási módokhoz kapcsolódó szintaxis.
- Jelentkezzen az olvasó: a napló olvasó, amely nem érhető el az SQL Database támaszkodó Szintaxis: leküldéses replikáció, adatváltozások rögzítése. Az SQL Database a leküldéses replikációs tétel előfizetője lehet.
- Funkciók: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardver: Hardver kiszolgálóbeállításokhoz kapcsolódó Szintaxis: memória, munkaszálak, processzoraffinitás, például a nyomkövetési jelzők. És méretek inkább számítási szolgáltatásszintek használhat.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`, és négyrészes nevek
- .NET-keretrendszer: Az SQL Server CLR-integrációt
- Szemantikai keresés
- Kiszolgáló hitelesítő adatai: használata [adatbázishoz kötődő hitelesítő adatok](https://msdn.microsoft.com/library/mt270260.aspx) helyette.
- Kiszolgálószintű elemek: kiszolgálói szerepkörök területen `sys.login_token`. A `GRANT`, `REVOKE`, és `DENY` kiszolgálószintű engedélyek nem érhetők el, bár van köztük olyan, amelyet adatbázisszintű engedélyek helyettesítenek. Néhány hasznos kiszolgálószintű dinamikus felügyeleti nézet rendelkezik egyenértékű adatbázisszintű dinamikus felügyeleti nézettel.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` lehetőségek és `RECONFIGURE`. Egyes lehetőségek elérhetők az [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx) (adatbázis-hatókörű konfiguráció változtatása) utasítással.
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: Az SQL Server Agent vagy MSDB-adatbázisra támaszkodó Szintaxis: riasztások, kezelők, központi felügyeleti kiszolgálók. Használjon helyette parancsfájlkezelőt, mint például az Azure PowerShell.
- SQL Server-naplózás: az SQL Database naplózási helyette.
- SQL Server-nyomkövetés
- Nyomkövetési jelzők: egyes nyomkövetési jelző elemek lettek áthelyezve kompatibilitási módban.
- Transact-SQL-hibakeresés
- Eseményindítók: kiszolgálói hatókörű vagy bejelentkezési eseményindítók
- `USE` utasítás: Ha másik adatbázissal szeretné felváltani az adatbázis-környezetet, új kapcsolódást kell létesítenie az új adatbázissal.

## <a name="full-transact-sql-reference"></a>A Transact-SQL teljes leírása
A Transact-SQL-szintaxisról és használatáról további információk és példák találhatók az SQL Server Online könyvek [A Transact-SQL leírása (adatbázismotor)](https://msdn.microsoft.com/library/bb510741.aspx) című részében. 

### <a name="about-the-applies-to-tags"></a>Az „Érvényes” címkék
A Transact-SQL referencia kapcsolódó SQL Server-verziók 2008, a jelen cikk tartalmaz. A cikk címe alatt van egy ikon sáv, listázás, a négy SQL Server-platformot, és hogy Alkalmazhatóság. A rendelkezésre állási csoportok például az SQL Server 2012-ben jelentek meg. A [CREATE AVAILABILTY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) a cikk azt jelzi, hogy az utasítás érvényes **(2012-től induló) SQL Server**. Az utasítás nem érvényes az SQL Server 2008, az SQL Server 2008 R2, az Azure SQL Database, az Azure SQL Data Warehouse és a Parallel Data Warehouse esetében.

Bizonyos esetekben egy cikk általános tárgya használható egy termékben, de a termékek között kisebb különbségek vannak. A különbségek a cikkben a megfelelő középpontoknál szükséges. Bizonyos esetekben egy cikk általános tárgya használható egy termékben, de a termékek között kisebb különbségek vannak. A különbségek a cikkben a megfelelő középpontoknál szükséges. A CREATE TRIGGER cikk például az SQL Database érhető el. De a **összes kiszolgáló** kiszolgálói szintű eseményindítók beállítást, azt jelzi, hogy a kiszolgálói szintű eseményindítók SQL Database-ben nem használható. Használja helyette az adatbázisszintű eseményindítók.

## <a name="next-steps"></a>További lépések

A támogatott és nem az SQL Database által támogatott funkciók listáját lásd: [Azure SQL Database funkcióinak összehasonlítása](sql-database-features.md). A lista ezen az oldalon, irányelvek és funkciók cikk kiegészíti, és Transact-SQL-utasítások összpontosít.

