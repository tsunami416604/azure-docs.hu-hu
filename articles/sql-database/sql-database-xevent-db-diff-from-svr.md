---
title: Bővített események SQL Database
description: Ismerteti a Azure SQL Database kiterjesztett eseményeit (Xevent típusú eseményekhez), valamint azt, hogy az esemény-munkamenetek hogyan térnek el kis mértékben a Microsoft SQL Server esemény-munkameneteitől.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: 64cfcd9451416a6eb35301268b285bd00cf0cad4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686781"
---
# <a name="extended-events-in-sql-database"></a>Bővített események SQL Database
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Ez a témakör azt mutatja be, hogy a Azure SQL Database kiterjesztett eseményeinek megvalósítása némileg eltér a Microsoft SQL Server kiterjesztett eseményeivel.

- SQL Database V12 a 2015-es naptári év második felében szerezte be a kiterjesztett események szolgáltatást.
- A SQL Server 2008 óta bővítette az eseményeket.
- A kibővített események SQL Database a SQL Server funkcióinak robusztus részhalmaza.

A *xevent típusú eseményekhez* egy nem hivatalos Felhasználónév, amelyet néha a "bővített események" a blogok és más informális helyszínek esetében is használnak.

További információ a Azure SQL Database és Microsoft SQL Server kiterjesztett eseményeiről:

- [Gyorskonfigurálás: bővített események a SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Bővített események](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Előfeltételek

Ez a témakör feltételezi, hogy már rendelkezik a következő ismeretekkel:

- [Azure SQL Database szolgáltatás](https://azure.microsoft.com/services/sql-database/).
- [Kiterjesztett események](https://msdn.microsoft.com/library/bb630282.aspx) a Microsoft SQL Serverban.

- A kiterjesztett eseményekre vonatkozó dokumentációnk nagy része a SQL Server és a SQL Database is vonatkozik.

A következő elemek előtti expozíció hasznos lehet az Eseménynapló kiválasztásakor [célként](#AzureXEventsTargets):

- [Azure Storage szolgáltatás](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Azure PowerShell használata az Azure Storage](../storage/common/storage-powershell-guide-full.md) -ban – átfogó információkat nyújt a PowerShell és az Azure Storage szolgáltatásról.

## <a name="code-samples"></a>Kódminták

A kapcsolódó témakörök két mintakód-mintát biztosítanak:


- [A kibővített eseményekhez tartozó gyűrűs pufferek SQL Database](sql-database-xevent-code-ring-buffer.md)
    - Rövid egyszerű Transact-SQL-szkript.
    - Kiemeljük a kód minta témakörben, hogy ha a gyűrűs puffer céljával készült, akkor az erőforrásait az Alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` utasítás végrehajtásával kell felszabadítani. Később a gyűrűs puffer egy másik példányát is hozzáadhatja `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Az eseménynaplóban a kiterjesztett események SQL Database](sql-database-xevent-code-event-file.md)
    - Az 1. fázis a PowerShell egy Azure Storage-tároló létrehozásához.
    - A 2. fázis a Transact-SQL, amely az Azure Storage-tárolót használja.

## <a name="transact-sql-differences"></a>A Transact-SQL eltérései


- Ha az esemény- [munkamenet létrehozása](https://msdn.microsoft.com/library/bb677289.aspx) parancsot SQL Serveron hajtja végre, használja a **on Server** záradékot. SQL Database azonban a **on Database** záradékot használja.


- A **on Database** záradék az [Alter Event-munkamenetre](https://msdn.microsoft.com/library/bb630368.aspx) és a [drop Event Session](https://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL parancsokra is vonatkozik.


- Az ajánlott eljárás a **STARTUP_STATE = on** esemény-munkamenet-beállítás belefoglalása az **esemény-munkamenet létrehozása** vagy az **esemény-munkamenet** -utasítások módosítása.
    - A **= on** érték a feladatátvétel miatti automatikus újraindítást is támogatja a logikai adatbázis újrakonfigurálása után.

## <a name="new-catalog-views"></a>Új katalógus nézetei

A kiterjesztett események funkciót számos [katalógus-nézet](https://msdn.microsoft.com/library/ms174365.aspx)támogatja. A katalógus nézeteiből megtudhatja, hogy az aktuális adatbázisban a felhasználó által létrehozott esemény-munkamenetek *metaadatai vagy definíciói* szerepelnek-e. A nézetek nem adnak vissza adatokat az aktív esemény-munkamenetek példányairól.

| Neve<br/>Katalógus nézet | Leírás |
|:--- |:--- |
| **sys. database_event_session_actions** |Minden művelet sorát adja vissza egy esemény-munkamenet minden eseményén. |
| **sys. database_event_session_events** |Egy adott esemény-munkamenetben lévő események sorát adja vissza. |
| **sys. database_event_session_fields** |Egy sort ad vissza minden olyan testre szabható oszlophoz, amely explicit módon be van állítva az eseményekre és a célokra. |
| **sys. database_event_session_targets** |Egy adott esemény-munkamenethez tartozó összes esemény céljának sorát adja vissza. |
| **sys. database_event_sessions** |Egy sort ad vissza a SQL Database-adatbázisban található minden egyes esemény-munkamenethez. |

Microsoft SQL Server hasonló katalógus-nézetek olyan nevekkel rendelkeznek, mint a. *Server\_* a *. Database\_* helyett. A név mint **sys. server_event_%** .

## <a name="new-dynamic-management-views-dmvshttpsmsdnmicrosoftcomlibraryms188754aspx"></a>Új dinamikus felügyeleti nézetek [(DMV)](https://msdn.microsoft.com/library/ms188754.aspx)

A Azure SQL Database [dinamikus felügyeleti nézetekkel (DMV) rendelkeznek](https://msdn.microsoft.com/library/bb677293.aspx) , amelyek támogatják a kiterjesztett eseményeket. DMV az *aktív* esemény-munkamenetekről.

| DMV neve | Leírás |
|:--- |:--- |
| **sys. DM _xe_database_session_event_actions** |Az esemény-munkamenet műveletekkel kapcsolatos adatokat adja vissza. |
| **sys. DM _xe_database_session_events** |A munkamenet-eseményekre vonatkozó adatokat adja vissza. |
| **sys. DM _xe_database_session_object_columns** |Megjeleníti a munkamenethez kötött objektumok konfigurációs értékeit. |
| **sys. DM _xe_database_session_targets** |A munkamenet-célokkal kapcsolatos adatokat adja vissza. |
| **sys. DM _xe_database_sessions** |Az aktuális adatbázisra hatókörben lévő minden esemény-munkamenet sorát adja vissza. |

A Microsoft SQL Server hasonló katalógus-nézetek neve nem a név *\_adatbázis* része, például:

- **sys. DM _xe_sessions**, név helyett<br/>**sys. DM _xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMV közös
A kiterjesztett eseményeknél további DMV is léteznek, amelyek a Azure SQL Database és Microsoft SQL Server egyaránt közösek:

- **sys. DM _xe_map_values**
- **sys. DM _xe_object_columns**
- **sys. DM _xe_objects**
- **sys. DM _xe_packages**

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

## <a name="targets-for-your-sql-database-event-sessions"></a>A SQL Database esemény-munkamenetek céljai

Az alábbi célok az esemény-munkamenetek eredményét rögzíthetik SQL Databaseon:

- [Gyűrűs puffer célja](https://msdn.microsoft.com/library/ff878182.aspx) – röviden megtarthatja az események mennyiségét a memóriában.
- [Esemény-számláló célja](https://msdn.microsoft.com/library/ff878025.aspx) – a kiterjesztett események munkamenete során felmerülő összes esemény megszámlálása.
- [Event file Target](https://msdn.microsoft.com/library/ff878115.aspx) – a teljes pufferek írása egy Azure Storage-tárolóba.

A [Windows esemény-nyomkövetés (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) API nem érhető el a kibővített eseményekhez SQL Databaseon.

## <a name="restrictions"></a>Korlátozások

A SQL Database felhőalapú környezetének számos biztonsággal kapcsolatos eltérése van:

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

Vannak olyan forgatókönyvek, amelyekben a kiterjesztett események intenzív használata több aktív memóriát halmozhat fel, mint a teljes rendszer kifogástalan állapota. Ezért a Azure SQL Database rendszer dinamikusan beállítja és beállítja az esemény-munkamenetek által felhalmozható aktív memória mennyiségét. Számos tényező a dinamikus számításba lép.

Ha olyan hibaüzenetet kap, amely szerint a rendszer maximálisan kikényszeríti a memóriát, néhány javítási művelet elvégezhető:

- Kevesebb egyidejű esemény-munkamenet futtatása.
- Az esemény-munkamenetek **létrehozási** és **módosítási** utasításai csökkentik a **maximális\_memória** záradékban megadott memória mennyiségét.

### <a name="network-latency"></a>Hálózati késleltetés

Az **eseményvezérelt fájl** célja hálózati késést vagy hibát tapasztalhat, miközben az Azure Storage-Blobok számára is megőrzi az adatait. Előfordulhat, hogy a SQL Database egyéb eseményei késleltetve vannak, amíg a hálózati kommunikáció befejezésére várnak. Ez a késleltetés csökkentheti a számítási feladatokat.

- Ennek a teljesítménynek a mérséklése érdekében ne állítsa be a **EVENT_RETENTION_MODE** beállítást, hogy **NO_EVENT_LOSS** az esemény-munkamenet-definíciókban.

## <a name="related-links"></a>Kapcsolódó hivatkozások

- [Azure PowerShell használata az Azure Storage](../storage/common/storage-powershell-guide-full.md)-ban.
- [Azure Storage-parancsmagok](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Azure PowerShell használata az Azure Storage](../storage/common/storage-powershell-guide-full.md) -ban – átfogó információkat nyújt a PowerShell és az Azure Storage szolgáltatásról.
- [A blob Storage használata a .NET-keretrendszerrel](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [ESEMÉNY-munkamenet létrehozása (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayias ' blogbejegyzések a bővített eseményekről Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Az Azure *Service Updates* weboldala, a paraméterrel leszűkítve Azure SQL Database:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


A kiterjesztett eseményekre vonatkozó egyéb mintakód-témakörök a következő hivatkozásokon érhetők el. A mintavételt azonban rendszeresen ellenőriznie kell, hogy látható-e a minta célja Microsoft SQL Server szemben Azure SQL Database. Ezután eldöntheti, hogy szükség van-e kisebb módosításokra a minta futtatásához.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
