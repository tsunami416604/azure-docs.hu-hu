---
title: Replikálás az Azure SQL Database |} A Microsoft Docs"
description: További tudnivalók az Azure SQL Database önálló adatbázisok és rugalmas készletekben található adatbázisokat az SQL Server-replikáció használatával
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: eae7f97799e38d5a4b09e3f193a84b423dced780
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869329"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Egyetlen vagy készletezett adatbázisok SQL-adatbázis-replikáció

SQL Server-replikáció beállítható úgy, hogy egyetlen vagy készletezett adatbázisok az olyan [logikai kiszolgáló](sql-database-logical-servers.md) Azure SQL Database-ben.  

## <a name="supported-configurations"></a>**Támogatott konfigurációk:**
  
- Az SQL Servert futtató helyszíni vagy a felhőben egy Azure virtuális gépen futó SQL Server-példány SQL Server-példány lehet. További információkért lásd: [SQL Server on Azure Virtual Machines – áttekintés](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- Az Azure SQL database egy SQL Server-közzétevő leküldéses előfizetője kell lennie.  
- A terjesztési adatbázis és a replikációs ügynökök nem helyezhető egy Azure SQL database.  
- Pillanatkép és a egy tranzakciós replikáció használata támogatott. Társ-társ tranzakciós replikáció és az egyesítéses replikáció nem támogatott.
- Replikáció az Azure SQL Database felügyelt példánya a nyilvános előzetes verzióban érhető el. Felügyelt példány közzétevő terjesztője és előfizető adatbázisok is üzemeltethet. További információkért lásd: [-replikáció az SQL Database felügyelt példánya a](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Verziók  

- A közzétevő és a terjesztőn kell, hogy a következő verziók egyikét mutattuk be:  
- Az SQL Server 2017 (14.x).
- Az SQL Server 2016 (13.x)
- Az SQL Server 2014 (12.x) SP1 CU3
- Az SQL Server 2014 (12.x) RTM CU10
- Az SQL Server 2012 (11.x) SP2 CU8 vagy SP3
- Próbál egy régebbi verzióját használja, a replikáció konfigurálása számának MSSQL_REPL20084 (a folyamat nem tudott kapcsolódni előfizető.) és MSSQL_REPL40532 hibát eredményez (nem nyitható meg a kiszolgáló \<név > a bejelentkezés által kért. A bejelentkezés sikertelen volt.).  
- Azure SQL Database összes funkciójának használatához kell használnia a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és [SQL Server Data Tools](https://docs.microsoft.com//sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017).  
  
## <a name="remarks"></a>Megjegyzések

- Replikáció használatával konfigurálható [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) vagy a közzétevő a Transact-SQL-utasítások végrehajtásával. Replikáció nem konfigurálhatja az Azure portal használatával.  
- Replikációs csak használhatja az SQL Server-hitelesítési bejelentkezés az Azure SQL Database-adatbázishoz csatlakozni.
- Replikált táblák elsődleges kulccsal kell rendelkeznie.  
- Meglévő Azure-előfizetéssel kell rendelkeznie.  
- Az Azure SQL database-előfizető bármelyik régióban is lehetnek.  
- Az SQL Server egyes közzétételekre Azure SQL Database és SQL Server (helyszíni és az SQL Server Azure virtuális gépeken) előfizetők is támogatja.  
- Replikációs kezelési, figyelési, és a hibaelhárítási kell végrehajtani a helyszíni SQL Serverről.  
- Az Azure SQL Database csak leküldéses előfizetéseket támogatja.  
- Csak `@subscriber_type = 0` támogatott **sp_addsubscription tárolt eljárás** SQL Database-hez.  
- Az Azure SQL Database nem támogatja a kétirányú, azonnali, frissíthető vagy társ-társ replikációt.

## <a name="replication-architecture"></a>Replikáció architektúrája  

![replikáció és az sql-adatbázisok](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Forgatókönyvek  

### <a name="typical-replication-scenario"></a>Tipikus replikációs forgatókönyv  

1. Hozzon létre egy tranzakciós replikáció kiadvány egy helyszíni SQL Server-adatbázisból.  
2. A helyszíni SQL Server használata a **új előfizetés varázsló** , vagy hozzon létre egy leküldéses előfizetés az Azure SQL Database Transact-SQL utasításokkal.  
3. A kezdeti adatokat, általában egy pillanatképet a pillanatkép-kezelő ügynök által létrehozott és az elosztott és a terjesztési ügynök által alkalmazott. A kezdeti adatkészlet biztonsági vagy egyéb módon, például az SQL Server Integration Services is meg kell adni.  

### <a name="data-migration-scenario"></a>Adatok áttelepítési forgatókönyv  

1. Tranzakciós replikáció használatával replikálja az adatokat a helyszíni SQL Server-adatbázisból az Azure SQL Database.  
2. Irányítsa át az ügyfél vagy a középső rétegbeli alkalmazásokhoz az Azure SQL adatbázis-példány frissítése.  
3. Állítsa le a táblázat az SQL Server-verzió frissítése, és távolítsa el a kiadványhoz.  

## <a name="limitations"></a>Korlátozások

A következő beállítások nem támogatottak az Azure SQL Database-előfizetések:

- Másolja a fájltársítási csoportok  
- Particionálási sémákat tábla másolása  
- Másolja a particionálási sémát index  
- Másolja ki a felhasználó által definiált statisztika  
- Másolja ki az alapértelmezett kötések  
- A szabály kötések másolása  
- Másolja ki a teljes szöveges indexek  
- XML-XSD másolása  
- Másolja az XML-indexek  
- Engedélyek másolása  
- A térbeli indexek másolása  
- Másolja a szűrt indexek  
- Másolja ki az adatok tömörítés attribútum  
- Másolja a ritkaoszlop-attribútum  
- A filestream átalakítása maximális adattípusok  
- Hierarchyid átalakítása maximális adattípusok  
- Konvertálja a térbeli adattípusok maximális száma  
- Kiterjesztett tulajdonságok másolása  
- Engedélyek másolása  

### <a name="limitations-to-be-determined"></a>Meghatározott korlátozások

- Másolja a rendezési sorrend  
- Az SP-szerializált tranzakciók végrehajtása  

## <a name="examples"></a>Példák

Hozzon létre egy kiadvány és a egy leküldéses előfizetés. További információkért lásd:
  
- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Hozzon létre egy leküldéses előfizetés](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) előfizetőként a Azure SQL database logikai kiszolgáló neve használatával (például **N'azuresqldbdns.database.windows.net "**) és az Azure SQL-adatbázis neve, mint a cél-adatbázis ( például **AdventureWorks**).  

## <a name="see-also"></a>Lásd még:  

- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Hozzon létre egy leküldéses előfizetés](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Replikáció típusa](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitoring (replikáció)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Előfizetés inicializálása](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  