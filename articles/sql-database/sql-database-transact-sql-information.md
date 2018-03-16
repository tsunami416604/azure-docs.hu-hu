---
title: "T-SQL különbségek-áttelepítés – Azure SQL Database feloldása |} Microsoft Docs"
description: "Nem teljes mértékben támogatott Transact-SQL-utasítások az Azure SQL Database-ben"
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: article
ms.date: 10/23/2017
ms.author: carlrab
ms.openlocfilehash: acc2c2556763650e0a0aff93fd247d882cb37852
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>SQL-adatbázishoz való áttelepítés során a feloldó Transact-SQL különbségek   
Ha [az adatbázis áttelepítése](sql-database-cloud-migrate.md) az SQL Serverről az Azure SQL-kiszolgálón, azt tapasztalhatja, hogy az adatbázis futtatásához szükséges néhány átalakításának az SQL Server áttelepítés előtt. Ez a cikk segítséget nyújt a mind a átalakításának végrehajtása, és miért szükséges a átalakításának az alapul szolgáló okok megértéséhez útmutatást nyújt a. Kompatibilitási problémák észlelése, használja a [adatok áttelepítési Segéd (DMA-)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Áttekintés
Microsoft SQL Server és az Azure SQL Database is teljes mértékben támogatja a legtöbb Transact-SQL funkciókat használják. Például adattípusok, a kezelők, a karakterlánc, a számtani, logikai, és a kurzor funkciók, például SQL alapösszetevőket munka azonos SQL Server és SQL-adatbázis. Azonban néhány T-SQL különbségek vannak DDL (adatok-definition language) és a T-SQL-utasítások és lekérdezések csak részben támogatott DML (adatok adatkezelési language) elemek (Ez a cikk későbbi részében tárgyaljuk).

Emellett vannak bizonyos funkciók és szintaxis nem támogatott, mivel az Azure SQL Database célja, hogy különítse el a függőségeket a master adatbázis és az operációs rendszer szolgáltatásait. A legtöbb kiszolgálószintű tevékenységek, SQL-adatbázis nem megfelelőek. T-SQL-utasítások és a beállítások nem érhetők el, ha konfigurálja kiszolgálói szintű beállításokat, operációs rendszer összetevőit, vagy adja meg a rendszer-konfigurációs fájlt. Ilyen tulajdonságokkal szükség, ha megfelelő alternatív gyakran érhető el más módon SQL-adatbázis vagy egy másik Azure szolgáltatás vagy szolgáltatás. 

Például magas rendelkezésre állású be van építve a Azure-ra, így mindig a konfigurálás nem szükséges (bár előfordulhat, hogy konfigurálni szeretné a gyorsabb helyreállítás aktív georeplikáció legyen katasztrófahelyzet esetén). Igen rendelkezésre állási csoportokkal kapcsolatos T-SQL-utasítások nem támogatottak az SQL-adatbázis, és a dinamikus felügyeleti nézetekkel kapcsolatos mindig a még nem támogatott.

Támogatott, és SQL-adatbázis által nem támogatott funkciók listáját lásd: [Azure SQL Database szolgáltatás összehasonlító](sql-database-features.md). Ezen a lapon a listán, irányelvek és a szolgáltatások cikkben kiegészíti, és Transact-SQL-utasítások összpontosít.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Részleges eltérésekkel Transact-SQL-szintaxis utasítások
A mag (dll) DDL-utasításokban érhetők el, de néhány DDL-utasításokban kell bővítmények lemez elhelyezési kapcsolódik, és nem támogatott funkciókat. 

- Hozzon létre és az ALTER DATABASE utasítás több mint három tucat lehetősége van. Fájl elhelyezését, FILESTREAM és csak érvényes SQL Server service broker beállítások közé tartoznak az utasítások Ez lehet, hogy nem számít, hogy adatbázisok létrehozására, mielőtt telepíti át, de ha az áttelepítés által létrehozott adatbázisok T-SQL-kódot kell összehasonlítani [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) a következő SQL Server használatával [(SQL Server Transact-SQL) adatbázis létrehozása](https://msdn.microsoft.com/library/ms176061.aspx) győződjön meg arról, használja az összes beállítások támogatottak. Az Azure SQL Database-adatbázis létrehozása a szolgáltatási cél és csak az SQL Database rugalmas bővítést beállításokat is rendelkezik.
- A létrehozás és az ALTER TABLE utasítás közül FileTable, amely az SQL-adatbázis nem használható, mert a FILESTREAM nem támogatott.
- A CREATE vagy ALTER login utasítás használata támogatott, de az SQL-adatbázis nem biztosít a beállításokat. Ahhoz, hogy az adatbázis több hordozható, SQL-adatbázis bátorítja a tartalmazott adatbázis-felhasználók helyett bejelentkezéseket, amikor csak lehetséges. További információkért lásd: [CREATE/ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) és [szabályozása és az adatbázis elérését](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Nem támogatott az Azure SQL Database Transact-SQL-szintaxis   
Transact-SQL utasítások a leírt nem támogatott szolgáltatásokkal kapcsolatos mellett [Azure SQL Database szolgáltatás összehasonlító](sql-database-features.md), az alábbi utasítások és kimutatások csoportok nem támogatottak. Ha az adatbázis kell áttelepíteni az alábbi szolgáltatások bármelyike használ, újra tervezését a T-SQL elkerülése érdekében a T-SQL funkciókat és az utasításokat.

- Rendszerobjektumok rendezése
- A kapcsolat kapcsolódó: Endpoint utasítás. Az SQL Database nem támogatja a Windows-hitelesítést, de támogatja a hasonló Azure Active Directory-hitelesítést. Bizonyos hitelesítési típusokhoz az SSMS legújabb verziója szükséges. További tudnivalókat a [Csatlakozás az SQL Database vagy az SQL Data Warehouse szolgáltatáshoz Azure Active Directory-hitelesítéssel](sql-database-aad-authentication.md) című cikkben találhat.
- Adatbázisközi lekérdezések három vagy négy résznévvel. (A csak olvasható adatbázisközi lekérdezéseket a [rugalmas adatbázis lekérdezése](sql-database-elastic-query-overview.md) funkció támogatja.)
- Tulajdonjog adatbázisközi láncolása, `TRUSTWORTHY` beállítása
- Az `EXECUTE AS LOGIN` helyett használja az 'EXECUTE AS USER' utasítást.
- A bővíthető kulcskezelés kivételével a titkosítás funkció támogatott
- Eseménykezelő: Események, rendszeresemény-értesítéseket, a lekérdezési értesítések
- Helye: szintaxis kapcsolódó adatbázis helye, méretének és automatikusan kezeli a Microsoft Azure adatbázisfájlok.
- Magas rendelkezésre állású: szintaxissal kapcsolatos magas rendelkezésre állású, amelyen keresztül a Microsoft Azure-fiókjához. Ide tartozik a biztonsági mentéshez, visszaállításhoz, Always On funkcióhoz, adatbázis-tükrözéshez, naplóküldéshez, helyreállítási módokhoz kapcsolódó szintaxis.
- Naplófájl olvasó: szintaxis, hogy a napló olvasó, amely nem található SQL-adatbázis alapul: leküldéses replikáció, az adatváltozások rögzítése. Az SQL Database a leküldéses replikációs tétel előfizetője lehet.
- Funkciók: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardver: Hardverrel kapcsolatos kiszolgálóbeállítások kapcsolódó Szintaxis: például memória, a munkavégző szál, a Processzor-affinitás, nyomkövetési jelzők. Használjon helyette szolgáltatási szinteket.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`, és négyrészes neve
- .NET-keretrendszer: Az SQL Server CLR-integrációt
- Szemantikai keresés
- A kiszolgáló hitelesítő adatai: használata [adatbázishoz kötődő hitelesítő adatok](https://msdn.microsoft.com/library/mt270260.aspx) helyette.
- Kiszolgálószintű elemek: kiszolgálói szerepkörök, `sys.login_token`. A `GRANT`, `REVOKE`, és `DENY` kiszolgálószintű engedélyek nem érhetők el, bár van köztük olyan, amelyet adatbázisszintű engedélyek helyettesítenek. Néhány hasznos kiszolgálószintű dinamikus felügyeleti nézet rendelkezik egyenértékű adatbázisszintű dinamikus felügyeleti nézettel.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` lehetőségek és `RECONFIGURE`. Egyes lehetőségek elérhetők az [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx) (adatbázis-hatókörű konfiguráció változtatása) utasítással.
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: Szintaxist az SQL Server Agent vagy az MSDB adatbázis alapul: riasztások, a kezelők, a központi felügyeleti kiszolgálókat. Használjon helyette parancsfájlkezelőt, mint például az Azure PowerShell.
- SQL Server-naplózás: az SQL Database naplózási helyette.
- SQL Server-nyomkövetés
- Nyomkövetési jelzők: minden nyomkövetési jelző elem helyezte kompatibilitási mód.
- Transact-SQL-hibakeresés
- Eseményindítók: kiszolgálói hatókörű vagy bejelentkezési eseményindítók
- `USE` utasítás: Ha másik adatbázissal szeretné felváltani az adatbázis-környezetet, új kapcsolódást kell létesítenie az új adatbázissal.

## <a name="full-transact-sql-reference"></a>A Transact-SQL teljes leírása
A Transact-SQL-szintaxisról és használatáról további információk és példák találhatók az SQL Server Online könyvek [A Transact-SQL leírása (adatbázismotor)](https://msdn.microsoft.com/library/bb510741.aspx) című részében. 

### <a name="about-the-applies-to-tags"></a>Az „Érvényes” címkék
A Transact-SQL 2008 SQL Server-verziók a jelen van a kapcsolódó cikkekben is vonatkozik. Hiba a cikk címe alatt ikon sáv, listázása a négy SQL Server-platformokat, és alkalmazhatósági jelző. A rendelkezésre állási csoportok például az SQL Server 2012-ben jelentek meg. A [AVAILABILTY csoport létrehozása](https://msdn.microsoft.com/library/ff878399.aspx) a cikk azt jelzi, hogy az utasítás érvényes **(2012-től induló) SQL Server**. Az utasítás nem érvényes az SQL Server 2008, az SQL Server 2008 R2, az Azure SQL Database, az Azure SQL Data Warehouse és a Parallel Data Warehouse esetében.

Bizonyos esetekben a cikk általános tárgya termék használható, de termékek közötti különbségek. A különbségek szerepelnek, szükség szerint cikkben középpont felett. Bizonyos esetekben a cikk általános tárgya termék használható, de termékek közötti különbségek. A különbségek szerepelnek, szükség szerint cikkben középpont felett. Ha például a CREATE TRIGGER cikk érhető el az SQL-adatbázis. De a **összes kiszolgáló** kiszolgálói szintű eseményindítók beállításnál azt jelzi, hogy a kiszolgálói szintű eseményindítók az SQL-adatbázis nem használható. Ehelyett használja az adatbázis-szintű eseményindítók.

## <a name="next-steps"></a>További lépések

Támogatott, és SQL-adatbázis által nem támogatott funkciók listáját lásd: [Azure SQL Database szolgáltatás összehasonlító](sql-database-features.md). Ezen a lapon a listán, irányelvek és a szolgáltatások cikkben kiegészíti, és Transact-SQL-utasítások összpontosít.

