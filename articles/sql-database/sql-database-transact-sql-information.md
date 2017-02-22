---
title: "Nem támogatott T-SQL az Azure SQL Database-ben | Microsoft Docs"
description: "Nem teljes mértékben támogatott Transact-SQL-utasítások az Azure SQL Database-ben"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: c05abd9e-28a7-4c97-9bdf-bc60d08fc92e
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 7db5939c88045ab59d7d9fb4b58f07374d7bca01
ms.openlocfilehash: ebec6ca87cfd5e9d2c4ea726d5d31d20998ef33f


---
# <a name="azure-sql-database-transact-sql-differences"></a>Azure SQL Database Transact-SQL különbségek   
A Transact-SQL legtöbb funkcióját, amelytől alkalmazások függenek, a Microsoft SQL Server és az Azure SQL Database egyaránt támogatja. Például az olyan SQL-alapösszetevők, mint az adattípusok, az operátorok, a karakterlánc, az aritmetikai, logikai és kurzorfunkciók stb., ugyanúgy működnek az Azure SQL Database-ben, mint az SQL Serverben.

## <a name="why-some-transact-sql-is-not-supported"></a>Miért vannak nem támogatott Transact-SQL-funkciók?
Az Azure SQL Database célja, hogy elszigetelje a funkciókat a főadatbázis és az operációs rendszer függőségeitől. Ezért számos kiszolgálói szintű tevékenység nem illik az SQL Database-hez. A Transact-SQL-utasítások általában nem érhetők el, ha kiszolgálói szintű beállításokat vagy operációsrendszer- komponenseket konfigurálnak, illetve fájlrendszer-konfigurációt adnak meg. Ha felhasználói adatbázison kívüli funkciókra van szükség, az SQL Database vagy más Azure-funkció, illetve -szolgáltatás gyakran kínál megfelelő alternatív megoldást más módon. 

Például az Always On funkció helyére az aktív georeplikáció kerül. Ezért az SQL Database nem támogatja sem a rendelkezésre állási csoportokkal kapcsolatos Transact-SQL-utasításokat, sem az Always On funkcióval összefüggő felügyeleti nézeteket.  

Az SQL Database által támogatott és nem támogatott funkciók listáját az [Azure SQL Database – szempontok, irányelvek és funkciók](sql-database-features.md) cikkben tekintheti meg.

Az SQL Serverben elavult szintaxist az SQL Database általában nem támogatja.

## <a name="transact-sql-syntax-partially-supported-in-sql-database"></a>Részben támogatott Transact-SQL-szintaxis az SQL Database-ben
Az SQL Database a megfelelő SQL Server 2016 Transact-SQL-utasításokban lévő argumentumok némelyikét, de nem mindegyiket támogatja. Például a `CREATE PROCEDURE` utasítás elérhető, de a `CREATE PROCEDURE` összes lehetősége nem érhető el. A teljes szintaxis leírása itt zavaró és redundáns lenne. Tekintse meg részletesen az egyes utasítások támogatott területeit az alább hivatkozott témakörökben.

- Adatbázisok: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER DATABASE](https://msdn.microsoft.com/library/ms174269.aspx)   
- Funkciók: [CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)   
- Bejelentkezések: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)   
- Tárolt eljárások: [CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)   
- Táblázatok: [CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)   
- (Egyéni) típusok: [CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)   
- Felhasználók: [CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)   
- Nézetek: [CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)   

## <a name="transact-sql-syntax-not-supported-in-sql-database"></a>Nem támogatott Transact-SQL-szintaxis az SQL Database-ben   
Nem támogatottak azok a Transact-SQL-utasítások, amelyek az [Azure SQL Database – szempontok, irányelvek és funkciók](sql-database-features.md) cikkben felsorolt, nem támogatott funkciókhoz kapcsolódnak, továbbá az alábbi utasítások és utasításcsoportok.
- Rendszerobjektumok rendezése
- Kapcsolatra vonatkozó: végponti utasítások, `ORIGINAL_DB_NAME`. Az SQL Database nem támogatja a Windows-hitelesítést, de támogatja a hasonló Azure Active Directory-hitelesítést. Bizonyos hitelesítési típusokhoz az SSMS legújabb verziója szükséges. További tudnivalókat a [Csatlakozás az SQL Database vagy az SQL Data Warehouse szolgáltatáshoz Azure Active Directory-hitelesítéssel](sql-database-aad-authentication.md) című cikkben találhat.
- Adatbázisközi lekérdezések három vagy négy résznévvel. (A csak olvasható adatbázisközi lekérdezéseket a [rugalmas adatbázis lekérdezése](sql-database-elastic-query-overview.md) funkció támogatja.)
- Tulajdonjog adatbázisközi láncolása, `TRUSTWORTHY` beállítása
- A `DATABASEPROPERTY` helyett használja a `DATABASEPROPERTYEX` utasítást.
- Az `EXECUTE AS LOGIN` helyett használja az 'EXECUTE AS USER' utasítást.
- A bővíthető kulcskezelés kivételével a titkosítás funkció támogatott
- Esemény: események, eseményértesítések, lekérdezésértesítések
- Adatbázisfájlok elhelyezéséhez és méretéhez kapcsolódó szintaxis, valamint olyan adatbázisfájlokhoz kapcsolódó szintaxis, amelyeket a Microsoft Azure automatikusan kezel.
- Magas rendelkezésre álláshoz kapcsolódó szintaxis, amelyet a rendszer a Microsoft Azure-fiókján keresztül kezel. Ide tartozik a biztonsági mentéshez, visszaállításhoz, Always On funkcióhoz, adatbázis-tükrözéshez, naplóküldéshez, helyreállítási módokhoz kapcsolódó szintaxis.
- SQL Database-ben nem elérhető, naplóolvasóhoz kapcsolódó szintaxis: leküldéses replikáció, adatváltozások rögzítése. Az SQL Database a leküldéses replikációs tétel előfizetője lehet.
- SQL Server Agentre vagy MSDB-adatbázisra támaszkodó szintaxis: riasztások, kezelők, központi felügyeleti kiszolgálók. Használjon helyette parancsfájlkezelőt, mint például az Azure PowerShell.
- Funkciók: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Globális ideiglenes táblák
- Hardverrel kapcsolatos kiszolgálóbeállításokhoz kapcsolódó szintaxis: memória, munkaszálak, processzoraffinitás, követésjelzők stb. Használjon helyette szolgáltatási szinteket.
- `HAS_DBACCESS`
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`, `BULK INSERT` és négyrészes nevek
- .NET-keretrendszer [CLR-integrációs szolgáltatás az SQL Serverben](http://msdn.microsoft.com/library/ms254963.aspx)
- Szemantikus keresés
- Kiszolgáló hitelesítő adatai. Használjon helyettük adatbázishoz kötődő hitelesítő adatokat.
- Kiszolgálószintű elemek: Kiszolgálói szerepkörök, `IS_SRVROLEMEMBER`, `sys.login_token`. A `GRANT`, `REVOKE`, és `DENY` kiszolgálószintű engedélyek nem érhetők el, bár van köztük olyan, amelyet adatbázisszintű engedélyek helyettesítenek. Néhány hasznos kiszolgálószintű dinamikus felügyeleti nézet rendelkezik egyenértékű adatbázisszintű dinamikus felügyeleti nézettel.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` lehetőségek és `RECONFIGURE`. Egyes lehetőségek elérhetők az [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx) (adatbázis-hatókörű konfiguráció változtatása) utasítással.
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server-naplózás. Használja helyette az SQL Database-naplózást.
- SQL Server-nyomkövetés
- Nyomkövetési jelzők. Egyes nyomkövetési jelzők kompatibilitási üzemmódokba kerültek.
- Transact-SQL-hibakeresés
- Eseményindítók: kiszolgálói hatókörű vagy bejelentkezési eseményindítók
- `USE` utasítás: Ha másik adatbázissal szeretné felváltani az adatbázis-környezetet, új kapcsolódást kell létesítenie az új adatbázissal.

## <a name="full-transact-sql-reference"></a>A Transact-SQL teljes leírása
A Transact-SQL-szintaxisról és használatáról további információk és példák találhatók az SQL Server Online könyvek [A Transact-SQL leírása (adatbázismotor)](https://msdn.microsoft.com/library/bb510741.aspx) című részében. 

### <a name="about-the-applies-to-tags"></a>Az „Érvényes” címkék
A Transact-SQL leírása a 2008-tól mostanáig elkészült SQL Server-verziókkal kapcsolatos témaköröket tartalmazza. A témakör címe alatti ikonsáv mutatja a négy SQL Server-platformot, és hogy alkalmazható-e rajtuk az adott lehetőség. A rendelkezésre állási csoportok például az SQL Server 2012-ben jelentek meg. A [CREATE AVAILABILTY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) (rendelkezésre állási csoport létrehozása) témakör jelzi, hogy az utasítás a **(2012-től induló) SQL Serverben érvényes**. Az utasítás nem érvényes az SQL Server 2008, az SQL Server 2008 R2, az Azure SQL Database, az Azure SQL Data Warehouse és a Parallel Data Warehouse esetében.

Előfordul, hogy a témakör általános tárgya használható egy termékben, de a termékek között kisebb eltérések adódnak. Az eltéréseket a témakör a megfelelő középpontoknál jelzi.



<!--HONumber=Dec16_HO3-->


