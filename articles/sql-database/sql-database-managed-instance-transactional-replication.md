---
title: Tranzakciós replikáció
description: Ismerje meg az SQL Server tranzakciós replikációhasználatát egyetlen, készletezett és példány-adatbázisokkal az Azure SQL Database-ben.
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
ms.openlocfilehash: 2a048ddefbcd76193436da13cd3ba68b8b6ffb0a
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607597"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Tranzakciós replikáció egyetlen, készletezett és példányadatbázisokkal az Azure SQL Database-ben

A tranzakciós replikáció az Azure SQL Database és az SQL Server egyik szolgáltatása, amely lehetővé teszi az Azure SQL Database vagy az SQL Server tábláiból származó adatok replikálását a távoli adatbázisokra helyezett táblákra. Ez a szolgáltatás lehetővé teszi több tábla szinkronizálását különböző adatbázisokban.

## <a name="when-to-use-transactional-replication"></a>Mikor kell használni a tranzakciós replikációt?

A tranzakciós replikáció a következő esetekben hasznos:
- Közzéteheti az adatbázis egy vagy több táblájában végrehajtott módosításokat, és terjesztheti azokat egy vagy több SQL Server vagy Azure SQL-adatbázisba, amelyek előfizettek a módosításokra.
- Több elosztott adatbázis szinkronállapotban tartása.
- A módosítások folyamatos közzétételével telepítse át az adatbázisokat az egyik SQL Server kiszolgálóról vagy a felügyelt példányról egy másik adatbázisba.

## <a name="overview"></a>Áttekintés

A tranzakciós replikáció fő összetevői az alábbi képen láthatók:  

![replikáció az SQL Database-rel](media/replication-to-sql-database/replication-to-sql-database.png)

A **Publisher** olyan példány vagy kiszolgáló, amely a frissítések nek a Forgalmazónak történő elküldésével teszi közzé az egyes táblákon (cikkekben) végrehajtott módosításokat. Az SQL Server következő verziói támogatják az Azure SQL-adatbázisban való közzétételt egy helyszíni SQL Server kiszolgálóról:

- SQL Server 2019 (előzetes verzió)
- SQL Server 2016 –SQL 2017
- SQL Server 2014 SP1 CU3 vagy nagyobb (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 vagy nagyobb (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Az SQL Server más verziói, amelyek nem támogatják az Azure-beli objektumokban való közzétételt, az [adatok újbóli közzétételére](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) szolgáló módszer rel az SQL Server újabb verzióiba helyezhető át. 

A **Disztribútor** olyan példány vagy kiszolgáló, amely összegyűjti a cikkek változásait a Kiadótól, és továbbítja azokat az Előfizetőknek. A Disztribútor lehet Az Azure SQL Database felügyelt példánya vagy az SQL Server (bármely verzió, amíg egyenlő vagy magasabb, mint a Publisher verzió). 

Az **Előfizető** olyan példány vagy kiszolgáló, amely megkapja a Közzétevőn végrehajtott módosításokat. Az előfizetők lehetnek egy-, készletezésű és példányadatbázisok az Azure SQL Database vagy az SQL Server-adatbázisokban. Az egyvagy több szálas adatbázis előfizetőjét push-előfizetőként kell konfigurálni. 

| Szerepkör | Egy- és összevont adatbázisok | Példányadatbázisok |
| :----| :------------- | :--------------- |
| **Publisher** | Nem | Igen | 
| **Forgalmazó** | Nem | Igen|
| **Lekéréses előfizető** | Nem | Igen|
| **Leküldéses előfizető**| Igen | Igen|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > A lekéréses előfizetés nem támogatott, ha a forgalmazó egy példány adatbázis, és az előfizető nem. 

A replikációnak különböző [típusai](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)vannak:


| Replikáció | Egy- és összevont adatbázisok | Példányadatbázisok|
| :----| :------------- | :--------------- |
| [**Szokásos tranzakciós**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Igen (csak előfizetőként) | Igen | 
| [**Pillanatkép**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Igen (csak előfizetőként) | Igen|
| [**Replikáció egyesítése**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nem | Nem|
| [**Egyenrangú a(z) peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nem | Nem|
| [**Kétirányú**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nem | Igen|
| [**Updatable előfizetések**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nem | Nem|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - A replikáció régebbi verzióval történő konfigurálása a MSSQL_REPL20084 (A folyamat nem tudott csatlakozni \<az előfizetőhöz.) és MSSQ_REPL40532 (A kiszolgálónév nem nyitható meg,> a bejelentkezés kéri. A bejelentkezés nem sikerült.)
  > - Az Azure SQL Database összes szolgáltatásának használatához az [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és az SQL Server Data Tools [(SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)legújabb verzióját kell használnia.
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Támogatási mátrix a példányadatbázisokhoz és a helyszíni rendszerekhez
  A példányadatbázisok replikációs támogatási mátrixa megegyezik a helyszíni SQL Server replikációs támogatási mátrixával. 
  
| **Publisher**   | **Forgalmazó** | **Előfizető** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Követelmények

- A kapcsolat SQL-hitelesítést használ a replikációs résztvevők között. 
- Egy Azure Storage-fiók megosztás a replikáció által használt működő könyvtárhoz. 
- A 445-ös portnak (TCP kimenő) meg kell nyitva lennie a felügyelt példány alhálózatának biztonsági szabályaiban az Azure-fájlmegosztás eléréséhez. 
- Az 1433-as portot (TCP kimenő) meg kell nyitni, ha a Kiadó/Forgalmazó felügyelt példányon van, és az előfizető nem. Előfordulhat, hogy a 1433-as `allow_linkedserver_outbound` **célszolgáltatás címkéjének** felügyelt példánynkénti `virtualnetwork` `internet`NSG kimenő biztonsági szabályát is a-ról . 
- A replikációs résztvevők (kiadó, forgalmazó, lekéréses előfizető és lekéréses előfizető) minden típusa elhelyezhető felügyelt példányokon, de a közzétevőnek és a forgalmazónak a felhőben vagy a helyszínen lévőnek kell lennie.
- Ha a kiadó, a forgalmazó és/vagy az előfizető különböző virtuális hálózatokban létezik, akkor a VPN-társviszony-létesítést az egyes entitások között kell létrehozni, hogy a közzétevő és a forgalmazó között VPN-társviszony-létesítés legyen, és/vagy a forgalmazó és az előfizető között VPN-társviszony-létesítés legyen. 


>[!NOTE]
> - Előfordulhat, hogy az 53-as hiba, amikor csatlakozik egy Azure Storage-fájl, ha a kimenő hálózati biztonsági csoport (NSG) 445-ös port javallt, ha a forgalmazó egy példány adatbázis és az előfizető a helyszínen. A probléma megoldásához [frissítse a vNet NSG-t.](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) 


### <a name="compare-data-sync-with-transactional-replication"></a>Az adatszinkronizálás összehasonlítása a tranzakciós replikációval

| | Adatszinkronizálás | Tranzakciós replikáció |
|---|---|---|
| Előnyök | - Aktív-aktív támogatás<br/>- Kétirányú között helyszíni és az Azure SQL Database | - Kisebb késleltetés<br/>- Tranzakciós konzisztencia<br/>- A meglévő topológia újrafelhasználása a migráció után |
| Hátrányok | - 5 min vagy több késleltetés<br/>- Nincs tranzakciós konzisztencia<br/>- Nagyobb teljesítményhatás | - Nem lehet közzétenni az Azure SQL Database egyetlen adatbázisvagy készletbe helyezett adatbázis<br/>- Magas karbantartási költség |
| | | |

## <a name="common-configurations"></a>Gyakori konfigurációk

A közzétevőnek és a forgalmazónak általában a felhőben vagy a helyszínen kell lennie. A következő konfigurációk vannak támogatva: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Kiadó a helyi Forgalmazóval egy felügyelt példányon

![Egyetlen példány kiadóként és forgalmazóként](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

A Publisher és a forgalmazó egyetlen felügyelt példányon belül van konfigurálva, és a módosítások terjesztése más felügyelt példányok, egyetlen adatbázis, készletezött adatbázis vagy az SQL Server helyszíni terjesztésére. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Közzétevő távoli forgalmazóval egy felügyelt példányon

Ebben a konfigurációban az egyik felügyelt példány olyan módosításokat tesz közzé egy másik felügyelt példányon elhelyezett forgalmazónak, amely számos forrás felügyelt példányt képes kiszolgálni, és módosításokat terjeszthet egy vagy több példányra a felügyelt példányon, az egyetlen adatbázison, a készletezett adatbázison vagy az SQL Serveren.

![Külön példányok a Kiadó és a Forgalmazó számára](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

A közzétevő és a forgalmazó két felügyelt példányon van konfigurálva. Ezzel a konfigurációval vannak bizonyos korlátozások: 

- Mindkét felügyelt példány ugyanazon a virtuális hálózaton található.
- Mindkét felügyelt példány ugyanazon a helyen található.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Kiadó és forgalmazó helyszíni, egyetlen, összevont és példányadatbázis előfizetőjével 

![Az Azure SQL DB mint előfizető](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
Ebben a konfigurációban egy Azure SQL-adatbázis (egyetlen, készletezésű és példányadatbázis) egy előfizető. Ez a konfiguráció támogatja a helyszíni azure-ból való áttelepítést. Ha egy előfizető egyetlen vagy készletezésű adatbázisban van, leküldéses módban kell lennie.  

## <a name="with-failover-groups"></a>Feladatátvételi csoportokkal

Ha a georeplikáció engedélyezve van egy [feladatátvételi csoport](sql-database-auto-failover-group.md) **közzétevői** vagy **forgalmazói** példányán, a felügyelt példány rendszergazdájának a feladatátvétel t követően meg kell tisztítania a régi elsődleges összes kiadványt, és újra kell konfigurálnia őket az új elsődleges en. Ebben a forgatókönyvben a következő tevékenységekre van szükség:

1. Állítsa le az adatbázisban futó összes replikációs feladatot, ha vannak ilyenek.
2. A közzétevőtől származó előfizetési metaadatok at a következő parancsfájl publisher adatbázisban történő futtatásával dobja el:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Az előfizetés metaadatainak eldobása az előfizetőtől. Futtassa a következő parancsfájlt az előfizetési adatbázison az előfizetői példányon:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. A közzétett adatbázisban a következő parancsfájl futtatásával a közzétett adatbázisban a következő parancsfájl futtatásával erőszakkal elkell dobni a közzétevőtől származó összes replikációs objektumot:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Erőteljesen csepp régi forgalmazó az eredeti elsődleges példány (ha nem sikerült vissza egy régi elsődleges, hogy a korábban volt egy forgalmazó). Futtassa a következő parancsfájlt a főadatbázison a régi, forgalmazó által felügyelt példányban:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Ha a georeplikáció engedélyezve van egy **előfizetői** példányon egy feladatátvételi csoportban, a kiadványt úgy kell konfigurálni, hogy csatlakozzon az előfizető felügyelt példányának feladatátvételi csoportfigyelő végpontjához. Feladatátvétel esetén a felügyelt példány rendszergazdájának későbbi művelete a feladatátvétel típusától függ: 

- Adatvesztés nélküli feladatátvétel esetén a replikáció a feladatátvétel után is működni fog. 
- Adatvesztéssel kapcsolatos feladatátvétel esetén a replikáció is működni fog. Újra replikálja az elveszett változásokat. 
- Adatvesztéssel kapcsolatos feladatátvétel esetén, de az adatvesztés kívül esik a terjesztési adatbázis megőrzési időszakán, a felügyelt példány rendszergazdájának újra kell inicializálnia az előfizetési adatbázist. 

## <a name="next-steps"></a>További lépések

- [A rendszerhiba-közzétevő és az előfizető közötti replikáció konfigurálása](replication-with-sql-database-managed-instance.md)
- [A rendszerjelző szolgáltatás kiadójának, a hibajelző-elosztónak és az SQL Server-előfizetőknek a replikációkonfigurálása](sql-database-managed-instance-configure-replication-tutorial.md)
- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Hozzon létre egy leküldéses előfizetést](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) az Azure `N'azuresqldbdns.database.windows.net` SQL Database-kiszolgáló nevének előfizetőként való használatával (például az Azure SQL Database nevét céladatbázisként (például **Adventureworks.** )


A tranzakciós replikáció konfigurálásáról az alábbi útmutatókban talál további információt:



## <a name="see-also"></a>Lásd még:  

- [Replikáció hibajelzővel és feladatátvételi csoporttal](sql-database-managed-instance-transact-sql-information.md#replication)
- [Replikáció az SQL Database-be](replication-to-sql-database.md)
- [Replikáció a felügyelt példányba](replication-with-sql-database-managed-instance.md)
- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Leküldéses előfizetés létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [A replikáció típusai](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Figyelés (replikáció)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Előfizetés inicializálása](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
