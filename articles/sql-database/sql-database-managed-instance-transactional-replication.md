---
title: Tranzakciós replikáció Azure SQL Databasesal | Microsoft Docs "
description: Ismerje meg, hogyan használhatja SQL Server tranzakciós replikációt egyetlen, készletezett és példány-adatbázissal Azure SQL Databaseokban.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: db295f7644cae96eb00670cecf6e4eeba9bb6bed
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567238"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Tranzakciós replikáció egyetlen, készletezett és példány-adatbázissal Azure SQL Database

A tranzakciós replikáció a Azure SQL Database és SQL Server egyik funkciója, amely lehetővé teszi az adatok replikálását Azure SQL Database vagy SQL Server a távoli adatbázisokra helyezett táblákba. Ez a funkció lehetővé teszi több tábla szinkronizálását különböző adatbázisokban.

## <a name="when-to-use-transactional-replication"></a>Mikor kell használni a tranzakciós replikációt

A tranzakciós replikáció a következő esetekben hasznos:
- Egy adatbázis egy vagy több táblájában végzett módosítások közzététele, és a módosítások előfizetése egy vagy több SQL Server vagy Azure SQL-adatbázisba.
- Több elosztott adatbázis tartása szinkronizált állapotban.
- Az adatbázisok áttelepíthetők egy SQL Server vagy felügyelt példányból egy másik adatbázisba a módosítások folyamatos közzétételekor.

## <a name="overview"></a>Áttekintés

A tranzakciós replikáció legfontosabb összetevői a következő képen láthatók:  

![replikálás SQL Database](media/replication-to-sql-database/replication-to-sql-database.png)

A **közzétevő** egy olyan példány vagy kiszolgáló, amely közzéteszi az egyes táblákon végrehajtott módosításokat, és elküldi a frissítéseket a terjesztőnek. A helyszíni SQL Server összes Azure SQL Database-adatbázisának közzétételét a SQL Server következő verziói támogatják:

- SQL Server 2019 (előzetes verzió)
- SQL Server 2016 – SQL 2017
- SQL Server 2014 SP1 CU3 vagy újabb (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 vagy újabb (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Az olyan SQL Server más verziói esetében, amelyek nem támogatják az Azure-ban lévő objektumok közzétételét, a republishing adatmódszer használatával áthelyezheti az adatátvitelt a SQL Server újabb verzióiba. [](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) 

A **terjesztő** egy olyan példány vagy kiszolgáló, amely a cikkek egy közzétevőtől származó változásait gyűjti, és elosztja azokat az előfizetőknek. A terjesztő lehet Azure SQL Database felügyelt példány vagy SQL Server (bármely verzió, ameddig a közzétevő verziója megegyezik vagy annál nagyobb). 

Az **előfizető** olyan példány vagy kiszolgáló, amely a közzétevőn végrehajtott módosításokat fogadja. Az előfizetők lehetnek önálló, készletezett és példány-adatbázisok Azure SQL Database vagy SQL Server adatbázisokban. Egy vagy készletezett adatbázis előfizetőjét leküldéses előfizetőként kell konfigurálni. 

| Role | Önálló és készletezett adatbázisok | Példány-adatbázisok |
| :----| :------------- | :--------------- |
| **Publisher** | Nem | Igen | 
| **Terjesztő** | Nem | Igen|
| **Lekéréses előfizető** | Nem | Igen|
| **Leküldéses előfizető**| Igen | Igen|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > A lekéréses előfizetés nem támogatott, ha a terjesztő egy példány-adatbázis, és az előfizető nem. 

A replikáció különböző [típusú](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):


| Replikálás | Önálló és készletezett adatbázisok | Példány-adatbázisok|
| :----| :------------- | :--------------- |
| [**Normál tranzakciós**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Igen (csak előfizetőként) | Igen | 
| [**Snapshot**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Igen (csak előfizetőként) | Igen|
| [**Replikálás egyesítése**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nem | Nem|
| [**Társ-társ**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nem | Nem|
| [**Kétirányú**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nem | Igen|
| [**Frissíthető előfizetések**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nem | Nem|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Ha a replikálást egy régebbi verzióval kísérli meg konfigurálni, a MSSQL_REPL20084 hibakódot eredményezhet (a folyamat nem tudott csatlakozni az előfizetőhöz.) és a \<MSSQ_REPL40532 (nem nyitható meg a bejelentkezés által kért kiszolgáló neve >. A bejelentkezés sikertelen.)
  > - A Azure SQL Database összes funkciójának használatához a [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és a [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)legújabb verzióját kell használnia.
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Támogatási mátrix a példány-adatbázisok és a helyszíni rendszerek számára
  A példány-adatbázisok replikálási támogatási mátrixa ugyanaz, mint a helyszíni SQL Server. 
  
  | **Publisher**   | **Terjesztő** | **Előfizető** |
| :------------   | :-------------- | :------------- |
| SQL Server 2017 | SQL Server 2017 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Követelmények

- A kapcsolat SQL-hitelesítést használ a replikációs résztvevők között. 
- Egy Azure Storage-fiók megosztása a replikáció által használt munkakönyvtárhoz. 
- A felügyelt példány alhálózatának biztonsági szabályaiban meg kell nyitni az 445 (TCP kimenő) portot az Azure-fájlmegosztás eléréséhez. 
- A 1433-as (TCP kimenő) portot meg kell nyitni, ha a közzétevő/terjesztő felügyelt példányon található, és az előfizető helyszíni.

  >[!NOTE]
  > Ha a kimenő hálózati biztonsági csoport (NSG) 445-es portja le van tiltva, ha a terjesztő egy példány-adatbázis, és az előfizető a helyszínen van, akkor a 53-es hiba léphet fel az Azure Storage-fájlhoz való csatlakozáskor. A probléma megoldásához [frissítse a VNET NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) . 

### <a name="compare-data-sync-with-transactional-replication"></a>Adatszinkronizálás összehasonlítása tranzakciós replikációval

| | Adatszinkronizálás | Tranzakciós replikáció |
|---|---|---|
| Előnyök | – Aktív-aktív támogatás<br/>– A helyszíni és a Azure SQL Database közötti kétirányú irányítás | – Alacsonyabb késés<br/>– Tranzakciós konzisztencia<br/>-Meglévő topológia újrafelhasználása az áttelepítés után |
| Hátrányai | – 5 perc vagy több késés<br/>– Nincs tranzakciós konzisztencia<br/>– Nagyobb teljesítményre gyakorolt hatás | – Nem lehet közzétenni Azure SQL Database önálló adatbázisból vagy készletezett adatbázisból<br/>– Magas karbantartási díj |
| | | |

## <a name="common-configurations"></a>Gyakori konfigurációk

Általánosságban a közzétevőnek és a terjesztőnek a felhőben vagy a helyszínen kell lennie. A következő konfigurációk támogatottak: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Közzétevő helyi terjesztővel felügyelt példányon

![Önálló példány Közzétevőként és terjesztőként](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

A közzétevő és a terjesztő egyetlen felügyelt példányon belül van konfigurálva, és a módosításokat más felügyelt példányra, önálló adatbázisra, készletezett adatbázisra vagy SQL Server a helyszínen osztja el. Ebben a konfigurációban a kiadói/forgalmazói felügyelt példány nem konfigurálható [földrajzi replikálással és automatikus feladatátvételi csoportokkal](sql-database-auto-failover-group.md).

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Közzétevő távoli terjesztővel felügyelt példányon

Ebben a konfigurációban egy felügyelt példány közzétesz egy másik felügyelt példányra helyezett terjesztőt, amely számos forrás által felügyelt példányt képes kiszolgálni, és a módosításokat egy vagy több, felügyelt példányra, önálló adatbázisra, készletezett adatbázisra vagy egy vagy több célpontra terjesztheti. SQL Server.

![A közzétevő és a terjesztő külön példányai](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

A közzétevő és a terjesztő két felügyelt példányon van konfigurálva. Ebben a konfigurációban

- Mindkét felügyelt példány ugyanazon a vNet található.
- Mindkét felügyelt példány ugyanazon a helyen található.
- A közzétett és terjesztő adatbázisokat üzemeltető felügyelt példányok nem [replikálhatók automatikus feladatátvételi csoportok használatával](sql-database-auto-failover-group.md).

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Kiadó és forgalmazó helyszíni előfizetővel egyetlen, készletezett és példányos adatbázisban 

![Azure SQL-adatbázis előfizetőként](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
Ebben a konfigurációban egy Azure SQL Database (egy, készletezett és példány-adatbázis) egy előfizető. Ez a konfiguráció támogatja a helyszíni rendszerről az Azure-ba való áttelepítést. Ha egy előfizető egyetlen vagy készletezett adatbázisban található, leküldéses módban kell lennie.  


## <a name="next-steps"></a>További lépések

1. [Konfigurálja a két felügyelt példány közötti replikálást](replication-with-sql-database-managed-instance.md). 
1. [Hozzon létre egy kiadványt](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
1. [Hozzon létre egy](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) leküldéses előfizetést a Azure SQL Database-kiszolgáló nevének előfizetőként való használatával ( `N'azuresqldbdns.database.windows.net` például a Azure SQL Database nevet a céladatbázisként (például **AdventureWorks**). )



## <a name="see-also"></a>Lásd még:  

- [Replikáció az SQL Database-be](replication-to-sql-database.md)
- [Replikálás felügyelt példányra](replication-with-sql-database-managed-instance.md)
- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Leküldéses előfizetés létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Replikálási típusok](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Figyelés (replikálás)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Előfizetés inicializálása](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
