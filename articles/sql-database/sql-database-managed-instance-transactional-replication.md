---
title: Az Azure SQL logikai kiszolgáló és az Azure SQL felügyelt példánya a tranzakciós replikáció |} A Microsoft Docs"
description: Ismerje meg az SQL Server tranzakciós replikációját használata Azure SQL Database logikai kiszolgálóiról és a felügyelt SQL-példány.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/08/2019
ms.openlocfilehash: d94173f9b1940613c26451658b90c956c71876fb
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353243"
---
# <a name="transactional-replication-with-azure-sql-logical-server-and-azure-sql-managed-instance"></a>Tranzakciós replikáció az Azure SQL logikai kiszolgáló és az Azure SQL felügyelt példánya

Tranzakciós replikáció funkciója egy Azure SQL Database, a felügyelt példány és, amely lehetővé teszi, hogy az adatok replikálása az Azure SQL Database egyik táblájában az SQL Server vagy SQL Server helyezi a távoli adatbázis tábláit. Ez a funkció lehetővé teszi több táblájából különböző adatbázisok szinkronizálása.

## <a name="when-to-use-transactional-replication"></a>Tranzakciós replikáció használata

Tranzakciós replikáció a következő esetekben hasznos:
- Egy vagy több táblát egy adatbázisban végrehajtott módosítások közzététele, és terjessze azokat egy vagy több SQL Server- vagy Azure SQL-adatbázisok, amelyek a módosítások az előfizetett.
- Ne több elosztott adatbázisok Szinkronizáltsági állapotával.
- Adatbázisok migrálása SQL Server vagy a felügyelt példány egy másik adatbázisba folyamatosan tegye közzé a módosításokat.

## <a name="overview"></a>Áttekintés 
A legfontosabb összetevők, a tranzakciós replikáció a következő képen látható:  

![a replikáció az SQL Database szolgáltatással](media/replication-to-sql-database/replication-to-sql-database.png)


A **közzétevő** egy olyan példányt vagy a kiszolgálót, amely közzéteszi az egyes táblák (cikk) végzett módosításokat, ha a frissítéseket küld a Terjesztőn. Közzététel az Azure SQL Database egy helyszíni SQL Serverről az SQL Server következő verzióiban támogatott:
    - Az SQL Server 2019 (előzetes verzió)
    - Az SQL Server 2016 SQL 2017-ben
    - Az SQL Server 2014 SP1 CU3 vagy nagyobb (12.00.4427)
    - Az SQL Server 2014 RTM CU10 (12.00.2556)
    - Az SQL Server 2012 SP3 vagy nagyobb (11.0.6020)
    - SQL Server 2012 SP2 CU8 (11.0.5634.0)
    - Egyéb verziói esetén az SQL Server, amelyek nem támogatják a közzétételt az objektumok az Azure-ban, is lehet használni a [adatok újbóli közzététele](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) metódus adatok áthelyezése az SQL Server újabb verzióit. 

A **terjesztő** példány vagy-kiszolgálót, amely a cikkekben módosítások gyűjti össze a közzétevő osztja el azokat az előfizetők számára. A terjesztő lehet az Azure SQL Database felügyelt példányába vagy az SQL Server (bármilyen verzió, ahogy azt a hosszú az egyenlő vagy nagyobb, mint a közzétevő verzió). 

A **előfizető** egy olyan példányt, vagy a módosítások a közzétevő a fogadó kiszolgálón. Előfizetők lehetnek, vagy az Azure SQL Database logikai kiszolgáló/Managed Instance, vagy az SQL Server. Egy logikai kiszolgálón az előfizető leküldéses előfizetőként kell konfigurálni. 

| Szerepkör | A logikai kiszolgáló | Felügyelt példány |
| :----| :------------- | :--------------- |
| **Közzétevő** | Nem | Igen | 
| **Terjesztő** | Nem | Igen|
| **Pull-előfizető** | Nem | Igen|
| **Push előfizetői**| Igen | Igen|
| &nbsp; | &nbsp; | &nbsp; |

Különböző [replikációtípusok](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication?view=sql-server-2017):


| Replikáció | A logikai kiszolgáló | Felügyelt példány |
| :----| :------------- | :--------------- |
| [**tranzakciós**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Igen (csak előfizető) | Igen | 
| [**Snapshot**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Igen (csak előfizető) | Igen|
| [**Replikációs egyesítés**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nem | Nem|
| [**Társ-társ**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nem | Nem|
| **One-way** | Igen | Igen|
| [**Kétirányú**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nem | Igen|
| [**Frissíthető előfizetések**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nem | Nem|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Kísérlet a replikálást egy régebbi verzióját használja (a folyamat nem tudott kapcsolódni előfizető.) szám MSSQL_REPL20084 és MSSQ_REPL40532 hiba eredményezhet (nem nyitható meg a kiszolgáló <name> a bejelentkezés által kért. A bejelentkezés sikertelen volt.)
  > - Azure SQL Database összes funkciójának használatához kell használnia a legújabb [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) és [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017).

## <a name="requirements"></a>Követelmények
- Kapcsolat SQL-hitelesítést használ a replikációs résztvevők között. 
- Egy Azure Storage-fiókot a munkakönyvtárban replikáció által használt megosztást. 
- 445-ös (TCP, kimenő) porton keresztül kell megnyitni a biztonsági szabályok az Azure-fájlmegosztás eléréséhez a felügyelt példány alhálózatára kell. 
- 1433 (TCP, kimenő) porton kell nyitható meg, ha az előfizető a helyszíni, és a közzétevő forgalmazó egy felügyelt példányon. 

## <a name="common-configurations"></a>Általános beállításai
Általánosságban véve a közzétevő és a terjesztőn kell lennie a felhőben vagy helyszíni. A következő konfigurációk támogatottak: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Felügyelt példány helyi terjesztő közzétevő

![Gyártó és forgalmazó, egyetlen példány ](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Gyártó és forgalmazó egyetlen felügyelt példány-ban van konfigurálva, és közzéteszi a módosításokat más felügyelt példányra, önálló adatbázis vagy SQL Server helyszíni. Ebben a konfigurációban a felügyelt példánynak közzétevő/terjesztő nem konfigurálható a [georeplikációja és automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md).

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>A felügyelt példány a távoli terjesztőt közzétevő

Ebben a konfigurációban egy felügyelt példány elhelyezett egy másik felügyelt példányt, amely képes több forrás felügyelt példány szolgál, és a felügyelt példányhoz, önálló adatbázis vagy SQL Server egy vagy több cél változásait terjesztheti a terjesztőhöz módosításokat tesz közzé.

![A közzétevő és a Terjesztőn különálló példány](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Gyártó és forgalmazó két felügyelt példány konfigurálása. Ebben a konfigurációban

- Felügyelt példány is ugyanazon a virtuális hálózaton van.
- Felügyelt példány is ugyanazon a helyen vannak.
- Felügyelt példányok üzemeltető közzétett és a terjesztő adatbázist nem lehet [georeplikált automatikus feladatátvételi-csoportok használatával](sql-database-auto-failover-group.md).

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-managed-instance-or-logical-server"></a>Gyártó és forgalmazó a helyszínen a felügyelt példány vagy logikai kiszolgálón található az előfizető 

![Az Azure SQL DB-előfizetőként](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
Ebben a konfigurációban az Azure SQL Database (a felügyelt példány vagy a logikai kiszolgáló) az előfizető. Ez a konfiguráció támogatja az áttelepítés a helyszínről az Azure-bA. Ha az előfizető logikai kiszolgálón leküldési módban kell lennie.  

## <a name="next-steps"></a>További lépések
1. [Konfigurálja a felügyelt példány tranzakciós replikáció](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance#configure-publishing-and-distribution-example). 
1. [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
1. [Hozzon létre egy leküldéses előfizetés](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) az előfizetők az Azure SQL Database logikai kiszolgáló neve használatával (például `N'azuresqldbdns.database.windows.net` és a cél adatbázis az Azure SQL Database nevére (például **Adventureworks**. )


## <a name="see-also"></a>Lásd még:  

- [Replikáció az SQL Database-be](replication-to-sql-database.md)
- [Felügyelt példány történő replikálásához](replication-with-sql-database-managed-instance.md)
- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Hozzon létre egy leküldéses előfizetés](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Replikáció típusa](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitoring (replikáció)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Előfizetés inicializálása](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
