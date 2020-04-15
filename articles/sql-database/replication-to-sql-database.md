---
title: Replikáció
description: Az SQL Server replikáció használata az Azure SQL Database egyetlen adatbázisaival és rugalmas készletekben lévő adatbázisaival
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: f28269b067ee98d69a97799911fd2d84a7f91e34
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381146"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replikáció az SQL Database egy- és készletezésű adatbázisokba

Az SQL Server replikációja konfigurálható egy- és készletezésű adatbázisokra az Azure SQL Database SQL Database [SQL Database-kiszolgálón.](sql-database-servers.md)  

## <a name="supported-configurations"></a>**Támogatott konfigurációk:**
  
- Az SQL Server lehet a helyszíni sql server vagy a felhőben lévő Azure virtuális gépen futó SQL Server példánya. További információt az [SQL Server az Azure virtuális gépeken című témakörben talál.](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)  
- Az Azure SQL-adatbázisnak egy SQL Server-közzénivaló leküldéses előfizetőjének kell lennie.  
- A terjesztési adatbázis és a replikációs ügynökök nem helyezhetők el Az Azure SQL-adatbázis.  
- A pillanatkép és az egyirányú tranzakciós replikáció támogatott. A társközi tranzakciós replikáció és az egyesítési replikáció nem támogatott.
- A replikáció nyilvános előzetes verzióban érhető el az Azure SQL Database felügyelt példányán. A felügyelt példány közzétevői, forgalmazói és előfizetői adatbázisokat üzemeltethet. További információt a [Replikáció az SQL-adatbázis által felügyelt példánysal című témakörben talál.](replication-with-sql-database-managed-instance.md)

## <a name="versions"></a>Verziók  

A helyszíni SQL Server-kiadóknak és -forgalmazóknak (legalább) az alábbi verziók egyikét kell használniuk:  

- SQL Server 2016 és még sok más
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) vagy [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) vagy [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> A replikáció nem támogatott verzióval történő konfigurálása hibaszámot eredményezhet MSSQL_REPL20084 (A folyamat nem \<tudott csatlakozni az előfizetőhöz.) és MSSQL_REPL40532 (A bejelentkezés i> kiszolgálónév nem nyitható meg. A bejelentkezés nem sikerült.).  

Az Azure SQL Database összes szolgáltatásának használatához az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és az SQL Server Data Tools legújabb verzióját kell [használnia.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)  

  
## <a name="remarks"></a>Megjegyzések

- A replikáció konfigurálható [az SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) használatával vagy a Transact-SQL utasítások végrehajtásával a közzétevőn. A replikáció nem konfigurálható az Azure Portal használatával.  
- Replikáció csak az SQL Server hitelesítési bejelentkezések segítségével csatlakozhat egy Azure SQL-adatbázishoz.
- A replikált tábláknak elsődleges kulccsal kell rendelkezniük.  
- Meglévő Azure-előfizetéssel kell rendelkeznie.  
- Az Azure SQL-adatbázis-előfizető bármely régióban lehet.  
- Egyetlen kiadvány az SQL Server en támogatja az Azure SQL Database és az SQL Server (helyszíni és SQL Server egy Azure virtuális gépen) előfizetők.  
- A replikációkezelést, a figyelést és a hibaelhárítást a helyszíni SQL Server kiszolgálóról kell elvégezni.  
- Csak az Azure SQL Database-előfizetések leküldéses támogatottak.  
- Csak `@subscriber_type = 0` az **SQL** Database sp_addsubscription támogatja.  
- Az Azure SQL Database nem támogatja a kétirányú, azonnali, updatable vagy társ-társ replikációt.

## <a name="replication-architecture"></a>Replikációs architektúra  

![replikáció sql-adatbázisba](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Forgatókönyvek  

### <a name="typical-replication-scenario"></a>Tipikus replikációs forgatókönyv  

1. Hozzon létre egy tranzakciós replikációs kiadványt egy helyszíni SQL Server-adatbázisban.  
2. A helyszíni SQL Server használja az **Új előfizetés varázsló** vagy transact-SQL utasítások hozzon létre egy leküldéses az Azure SQL Database-előfizetés.  
3. Az Azure SQL Database-ben egy- és készletalapú adatbázisok egy pillanatkép, amely a Pillanatkép-ügynök által létrehozott és a terjesztési ügynök által elosztott és alkalmazott pillanatkép. Felügyelt példány adatbázis használatával is használhat adatbázis-biztonsági mentést az előfizetői adatbázis magzatához.

### <a name="data-migration-scenario"></a>Adatáttelepítési forgatókönyv  

1. A tranzakciós replikáció segítségével replikálja az adatokat egy helyszíni SQL Server-adatbázisból az Azure SQL Database-be.  
2. Irányítsa át az ügyfél- vagy középső rétegbeli alkalmazásokat az Azure SQL-adatbázis-példány frissítéséhez.  
3. Állítsa le a tábla SQL Server verziójának frissítését, és távolítsa el a kiadványt.  

## <a name="limitations"></a>Korlátozások

Az Azure SQL Database-előfizetések nem támogatják a következő beállításokat:

- Fájlcsoportok társításának másolása  
- Táblaparticionálási sémák másolása  
- Indexparticionálási sémák másolása  
- Felhasználó által definiált statisztikák másolása  
- Alapértelmezett kötések másolása  
- Szabálykötések másolása  
- Teljes szöveges indexek másolása  
- XML XSD másolása  
- XML-indexek másolása  
- Engedélyek másolása  
- Térbeli indexek másolása  
- Szűrt indexek másolása  
- Adattömörítési attribútum másolása  
- Ritka oszlop attribútum ának másolása  
- Fájlfolyam konvertálása MAX adattípusokká  
- Hierarchyid konvertálása MAX adattípusokká  
- Térbeli konvertálás MAX adattípussá  
- Bővített tulajdonságok másolása  
- Engedélyek másolása  

### <a name="limitations-to-be-determined"></a>Később meghatározandó korlátozások

- Rendezés másolása  
- Végrehajtás az SP szerializált tranzakciójában  

## <a name="examples"></a>Példák

Hozzon létre egy kiadványt és egy leküldéses előfizetést. További információkért lásd:
  
- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Push-előfizetés létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) az Azure SQL Database-kiszolgáló neve (például **N'azuresqldbdns.database.windows.net)** és az Azure SQL adatbázis neve (például **AdventureWorks)** használatával.  

## <a name="see-also"></a>Lásd még:  

- [Tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md)
- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Leküldéses előfizetés létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [A replikáció típusai](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Figyelés (replikáció)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Előfizetés inicializálása](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
