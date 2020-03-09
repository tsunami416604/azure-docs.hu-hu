---
title: Replikáció
description: Tudnivalók a SQL Server replikáció használatáról Azure SQL Database önálló adatbázisok és adatbázisok rugalmas készletekben való használatával
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: f718bc17b987926f4324635f096d5983acdb63fc
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359862"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replikálás egyetlen és készletezett adatbázis SQL Database

SQL Server replikáció konfigurálható egyetlen és készletezett adatbázisra [SQL Database-kiszolgálón](sql-database-servers.md) Azure SQL Database.  

## <a name="supported-configurations"></a>**Támogatott konfigurációk:**
  
- A SQL Server a felhőben futó Azure-beli virtuális gépen futtatott SQL Server példánya lehet a helyszínen vagy a SQL Server egy példányán. További információ: [SQL Server az Azure Virtual Machines áttekintése](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- Az Azure SQL Database-nek egy SQL Server közzétevő leküldéses előfizetőjának kell lennie.  
- A terjesztési adatbázis és a replikációs ügynökök nem helyezhetők el Azure SQL Database-adatbázisba.  
- A pillanatkép és az egyirányú tranzakciós replikáció támogatott. A társ-társ tranzakciós replikáció és az egyesítéses replikáció nem támogatott.
- A Azure SQL Database felügyelt példányon nyilvános előzetes verzióban érhető el a replikálás. A felügyelt példányok közzétevői, terjesztői és előfizetői adatbázisokat is futtathatnak. További információ: [replikáció SQL Database felügyelt példánnyal](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Verziók  

A helyszíni SQL Server közzétevőknek és forgalmazóknak a következő verziók egyikét kell használnia (legalább):  

- SQL Server 2016 és újabb
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) vagy [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) vagy [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Ha nem támogatott verzióval konfigurálja a replikálást, a hiba szám MSSQL_REPL20084 (a folyamat nem tudott csatlakozni az előfizetőhöz.) és a MSSQL_REPL40532 (nem nyitható meg a kiszolgáló \<neve > a bejelentkezés által kért név. A bejelentkezés sikertelen.)  

A Azure SQL Database összes funkciójának használatához a [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és a [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)legújabb verzióit kell használnia.  

  
## <a name="remarks"></a>Megjegyzések

- A replikáció [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) használatával vagy Transact-SQL-utasítások végrehajtásával konfigurálható a közzétevőn. A replikációt nem konfigurálhatja a Azure Portal használatával.  
- A replikáció csak SQL Server hitelesítési bejelentkezéseket használhat egy Azure SQL Database-adatbázishoz való kapcsolódáshoz.
- A replikált tábláknak elsődleges kulccsal kell rendelkezniük.  
- Rendelkeznie kell egy meglévő Azure-előfizetéssel.  
- Az Azure SQL Database-előfizető bármely régióban lehet.  
- SQL Server egyetlen kiadványa képes támogatni Azure SQL Database és SQL Server (helyszíni és SQL Server Azure-beli virtuális gépeken) előfizetők számára.  
- A replikációs felügyeletet, a figyelést és a hibaelhárítást a helyszíni SQL Server kell végrehajtania.  
- Csak a Azure SQL Database leküldéses előfizetések támogatottak.  
- SQL Database esetében csak `@subscriber_type = 0` támogatott **sp_addsubscription** .  
- A Azure SQL Database nem támogatja a kétirányú, azonnali, frissíthető vagy egyenrangú replikálást.

## <a name="replication-architecture"></a>Replikációs architektúra  

![replication-to-sql-database](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Forgatókönyvek  

### <a name="typical-replication-scenario"></a>Tipikus replikációs forgatókönyv  

1. Hozzon létre egy tranzakciós replikációs kiadványt egy helyszíni SQL Server adatbázison.  
2. A helyszíni SQL Server az **új előfizetés varázslóval** vagy Transact-SQL-utasításokkal hozzon létre egy leküldéses előfizetést a Azure SQL Databaseba.  
3. A Azure SQL Database önálló és készletezett adatbázisaiban a kezdeti adatkészlet olyan pillanatkép, amelyet a pillanatkép-ügynök hozott létre, és amelyet a terjesztési ügynök kioszt és alkalmaz. Felügyelt példányok adatbázisa esetén az adatbázis biztonsági másolatát is használhatja az előfizetői adatbázis kivetéséhez.

### <a name="data-migration-scenario"></a>Adatáttelepítési forgatókönyv  

1. Tranzakciós replikálás használatával replikálhatja a helyszíni SQL Server-adatbázis adatait a Azure SQL Databaseba.  
2. Az ügyfél vagy a középső rétegbeli alkalmazások átirányítása az Azure SQL Database-példány frissítésére.  
3. Állítsa le a táblázat SQL Server verziójának frissítését, és távolítsa el a kiadványt.  

## <a name="limitations"></a>Korlátozások

Azure SQL Database-előfizetések esetén a következő beállítások nem támogatottak:

- Fájlcsoportok társításának másolása  
- Táblázat particionálási sémáinak másolása  
- Index particionálási sémáinak másolása  
- Felhasználó által definiált statisztikák másolása  
- Alapértelmezett kötések másolása  
- Szabály-kötések másolása  
- Teljes szöveges indexek másolása  
- Copy XML XSD  
- XML-indexek másolása  
- Másolási engedélyek  
- Térbeli indexek másolása  
- Szűrt indexek másolása  
- Adattömörítési attribútum másolása  
- Ritka oszlop attribútumának másolása  
- FileStream konvertálása maximális adattípusra  
- Hierarchyid konvertálása maximális adattípusra  
- Térbeli érték konvertálása maximális adattípusra  
- Kiterjesztett tulajdonságok másolása  
- Másolási engedélyek  

### <a name="limitations-to-be-determined"></a>Meghatározandó korlátozások

- Rendezés másolása  
- Végrehajtás az SP szerializált tranzakciójában  

## <a name="examples"></a>Példák

Hozzon létre egy kiadványt és egy leküldéses előfizetést. További információkért lásd:
  
- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Hozzon létre egy leküldéses előfizetést](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) a Azure SQL Database kiszolgáló nevének előfizetőként való használatával (például **N'azuresqldbdns. database. Windows. net**) és az Azure SQL Database-adatbázis nevét célként megadott adatbázisként (például **AdventureWorks**).  

## <a name="see-also"></a>Lásd még:  

- [Tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md)
- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Leküldéses előfizetés létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [A replikáció típusai](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Figyelés (replikálás)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Előfizetés inicializálása](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
