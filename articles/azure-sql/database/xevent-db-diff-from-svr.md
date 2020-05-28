---
title: Bővített események
description: Ismerteti a Azure SQL Database kiterjesztett eseményeit (Xevent típusú eseményekhez), valamint azt, hogy az esemény-munkamenetek hogyan térnek el kis mértékben a Microsoft SQL Server esemény-munkameneteitől.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: 7f0f50de3f74f0e8040118035e28b3e905ed5616
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046956"
---
# <a name="extended-events-in-azure-sql-database"></a>Bővített események Azure SQL Database 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

A Azure SQL Database kiterjesztett eseményeinek szolgáltatáskészlet-készlete a SQL Server és az Azure SQL felügyelt példány funkcióinak robusztus részhalmaza.

A *xevent típusú eseményekhez* egy nem hivatalos Felhasználónév, amelyet néha a "bővített események" a blogok és más informális helyszínek esetében is használnak.

A kiterjesztett eseményekkel kapcsolatos további információk a következő címen érhetők el:

- [Gyorskonfigurálás: bővített események a SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
- [Bővített események](/sql/relational-databases/extended-events/extended-events)

## <a name="prerequisites"></a>Előfeltételek

Ez a témakör feltételezi, hogy már rendelkezik a következő ismeretekkel:

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Bővített események](/sql/relational-databases/extended-events/extended-events)

- A kiterjesztett eseményekre vonatkozó dokumentációnk nagy része a SQL Server, Azure SQL Database és az Azure SQL felügyelt példányaira vonatkozik.

A következő elemek előtti expozíció hasznos lehet az Eseménynapló kiválasztásakor [célként](#AzureXEventsTargets):

- [Azure Storage szolgáltatás](https://azure.microsoft.com/services/storage/)

- [Azure PowerShell az Azure Storage-ban](/powershell/module/az.storage/)

## <a name="code-samples"></a>Kódminták

A kapcsolódó témakörök két mintakód-mintát biztosítanak:

- [A kibővített eseményekhez tartozó gyűrűs pufferek Azure SQL Database](xevent-code-ring-buffer.md)

  - Rövid egyszerű Transact-SQL-szkript.
  - Kiemeljük a kód minta témakörben, hogy ha a gyűrűs puffer céljával készült, akkor az erőforrásait az Alter-drop utasítás végrehajtásával kell felszabadítani `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` . Később a gyűrűs puffer egy másik példányát is hozzáadhatja `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...` .

- [Az eseménynaplóban a kiterjesztett események Azure SQL Database](xevent-code-event-file.md)

  - Az 1. fázis a PowerShell egy Azure Storage-tároló létrehozásához.
  - A 2. fázis a Transact-SQL, amely az Azure Storage-tárolót használja.

## <a name="transact-sql-differences"></a>Transact-SQL-különbségek

- Ha az esemény- [munkamenet létrehozása](/sql/t-sql/statements/create-event-session-transact-sql) parancsot SQL Serveron hajtja végre, használja a **on Server** záradékot. Azure SQL Database azonban a **on Database** záradékot használja.
- A **on Database** záradék az [Alter Event-munkamenetre](/sql/t-sql/statements/alter-event-session-transact-sql) és a [drop Event Session](/sql/t-sql/statements/drop-event-session-transact-sql) Transact-SQL parancsokra is vonatkozik.

- Az ajánlott eljárás az, hogy tartalmazza a **STARTUP_STATE =** esemény-munkamenetet az **esemény-munkamenet létrehozásakor** , vagy az esemény-munkamenet-utasítások **megváltoztatásával** .
  - A **= on** érték a feladatátvétel miatti automatikus újraindítást is támogatja a logikai adatbázis újrakonfigurálása után.

## <a name="new-catalog-views"></a>Új katalógus nézetei

A kiterjesztett események funkciót számos [katalógus-nézet](https://msdn.microsoft.com/library/ms174365.aspx)támogatja. A katalógus nézeteiből megtudhatja, hogy az aktuális adatbázisban a felhasználó által létrehozott esemény-munkamenetek *metaadatai vagy definíciói* szerepelnek-e. A nézetek nem adnak vissza adatokat az aktív esemény-munkamenetek példányairól.

| Neve<br/>Katalógus nézet | Leírás |
|:--- |:--- |
| **sys. database_event_session_actions** |Minden művelet sorát adja vissza egy esemény-munkamenet minden eseményén. |
| **sys. database_event_session_events** |Egy adott esemény-munkamenetben lévő események sorát adja vissza. |
| **sys. database_event_session_fields** |Egy sort ad vissza minden olyan testre szabható oszlophoz, amely explicit módon be van állítva az eseményekre és a célokra. |
| **sys. database_event_session_targets** |Egy adott esemény-munkamenethez tartozó összes esemény céljának sorát adja vissza. |
| **sys. database_event_sessions** |Az adatbázis minden esemény-munkamenetének sorát adja vissza. |

A Microsoft SQL Server hasonló katalógus-nézetek olyan nevekkel rendelkeznek, amelyek. *Server \_ * helyett a. *Database \_ *nevet tartalmazzák. A név mint **sys. server_event_%**.

## <a name="new-dynamic-management-views-dmvs"></a>Új dinamikus felügyeleti nézetek [(DMV)](https://msdn.microsoft.com/library/ms188754.aspx)

A Azure SQL Database [dinamikus felügyeleti nézetekkel (DMV) rendelkeznek](https://msdn.microsoft.com/library/bb677293.aspx) , amelyek támogatják a kiterjesztett eseményeket. DMV az *aktív* esemény-munkamenetekről.

| DMV neve | Leírás |
|:--- |:--- |
| **sys. dm_xe_database_session_event_actions** |Az esemény-munkamenet műveletekkel kapcsolatos adatokat adja vissza. |
| **sys. dm_xe_database_session_events** |A munkamenet-eseményekre vonatkozó adatokat adja vissza. |
| **sys. dm_xe_database_session_object_columns** |Megjeleníti a munkamenethez kötött objektumok konfigurációs értékeit. |
| **sys. dm_xe_database_session_targets** |A munkamenet-célokkal kapcsolatos adatokat adja vissza. |
| **sys. dm_xe_database_sessions** |Az aktuális adatbázisra hatókörben lévő minden esemény-munkamenet sorát adja vissza. |

A Microsoft SQL Server hasonló katalógus-nézetek neve nem a név * \_ adatbázis* része, például:

- **sys. dm_xe_sessions**, név helyett<br/>**sys. dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMV közös

A kiterjesztett események esetében további DMV, amelyek közösek a Azure SQL Database, az Azure SQL felügyelt példánya és a Microsoft SQL Server esetében:

- **sys. dm_xe_map_values**
- **sys. dm_xe_object_columns**
- **sys. dm_xe_objects**
- **sys. dm_xe_packages**

<a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>A rendelkezésre álló kiterjesztett események, műveletek és célok keresése

Futtathat egy egyszerű SQL- **választ** az elérhető események, műveletek és cél listájának beszerzéséhez.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```

<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-azure-sql-database-event-sessions"></a>A Azure SQL Database esemény-munkamenetek céljai

Az alábbi célok az esemény-munkamenetek eredményét rögzíthetik Azure SQL Databaseon:

- [Gyűrűs puffer célja](https://msdn.microsoft.com/library/ff878182.aspx) – röviden megtarthatja az események mennyiségét a memóriában.
- [Esemény-számláló célja](https://msdn.microsoft.com/library/ff878025.aspx) – a kiterjesztett események munkamenete során felmerülő összes esemény megszámlálása.
- [Event file Target](https://msdn.microsoft.com/library/ff878115.aspx) – a teljes pufferek írása egy Azure Storage-tárolóba.

A [Windows esemény-nyomkövetés (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) API nem érhető el a kibővített eseményekhez Azure SQL Databaseon.

## <a name="restrictions"></a>Korlátozások

A Azure SQL Database felhőalapú környezetének számos biztonsággal kapcsolatos eltérése van:

- A kiterjesztett események az egybérlős elkülönítési modellen alapulnak. Egy adatbázis egyik esemény-munkamenete nem fér hozzá egy másik adatbázisból származó adatokhoz vagy eseményekhez.
- A **Master** adatbázis kontextusában nem lehet **létrehozni esemény-munkamenet** -utasítást.

## <a name="permission-model"></a>Engedélyezési modell

Az adatbázis létrehozásához **vezérlési** engedéllyel kell RENDELKEZNIE az **esemény létrehozása munkamenet** -utasítás kiadásához. Az adatbázis tulajdonosa (dbo) rendelkezik **vezérlési** engedéllyel.

### <a name="storage-container-authorizations"></a>Storage-tárolók engedélyezése

Az Azure Storage-tárolóhoz létrehozott SAS-jogkivonatnak meg kell adnia az engedélyek **RWL** . A **RWL** érték a következő engedélyeket biztosítja:

- Olvasás
- Írás
- Lista

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Vannak olyan forgatókönyvek, amelyekben a kiterjesztett események intenzív használata több aktív memóriát halmozhat fel, mint a teljes rendszer kifogástalan állapota. Ezért Azure SQL Database dinamikusan állítja be és módosítja a korlátokat az esemény-munkamenetek által felhalmozható aktív memória mennyisége tekintetében. Számos tényező a dinamikus számításba lép.

Ha olyan hibaüzenetet kap, amely szerint a rendszer maximálisan kikényszeríti a memóriát, néhány javítási művelet elvégezhető:

- Kevesebb egyidejű esemény-munkamenet futtatása.
- Az esemény-munkamenetek **létrehozási** és **módosítási** utasításai segítségével csökkentheti a **maximális \_ memória** záradékban megadott memória mennyiségét.

### <a name="network-latency"></a>Hálózati késleltetés

Az **eseményvezérelt fájl** célja hálózati késést vagy hibát tapasztalhat, miközben az Azure Storage-Blobok számára is megőrzi az adatait. Előfordulhat, hogy a Azure SQL Database egyéb eseményei késleltetve vannak, amíg a hálózati kommunikáció befejezésére várnak. Ez a késleltetés csökkentheti a számítási feladatokat.

- Ennek a teljesítménynek a mérsékléséhez ne állítsa be a **EVENT_RETENTION_MODE** lehetőséget az esemény-munkamenet-definíciókban való **NO_EVENT_LOSS** .

## <a name="related-links"></a>Kapcsolódó hivatkozások

- [Azure PowerShell használata az Azure Storage](/powershell/module/az.storage/)-ban.
- [Azure Storage-parancsmagok](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Using Azure PowerShell with Azure Storage (Az Azure PowerShell és az Azure Storage együttes használata)](/powershell/module/az.storage/)
- [A blob Storage használata a .NET-keretrendszerrel](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [ESEMÉNY-munkamenet létrehozása (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayias ' blogbejegyzések a bővített eseményekről Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)
- Az Azure *Service Updates* weboldala, a paraméterrel leszűkítve Azure SQL Database:
  - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
