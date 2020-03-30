---
title: Bővített események
description: A cikk az Azure SQL Database kiterjesztett eseményeit (XEvents) ismerteti, valamint azt, hogy az eseménymunkamenetek hogyan térnek el kis mértékben a Microsoft SQL Server eseménymunkameneteitől.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: cb4eb4474ad074a3e69dc146c97b48d54343595b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213945"
---
# <a name="extended-events-in-sql-database"></a>Kiterjesztett események az SQL Database-ben
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Ez a témakör bemutatja, hogy az Azure SQL Database-ben a kiterjesztett események megvalósítása hogyan különbözik a Microsoft SQL Server kiterjesztett eseményeitől.

- Az SQL Database V12 a 2015-ös naptár második felében nyerte el a kiterjesztett események funkciót.
- Az SQL Server 2008 óta rendelkezik kiterjesztett eseményekkel.
- Az SQL Database kiterjesztett eseményeinek szolgáltatáskészlete az SQL Server szolgáltatásainak robusztus része.

*Az XEvents* egy informális becenév, amelyet néha "kiterjesztett eseményekre" használnak blogokban és más informális helyszíneken.

Az Azure SQL Database és a Microsoft SQL Server kiterjesztett eseményeiről a következő elérhetőségeken talál további információt:

- [Rövid útmutató: Bővített események az SQL Server ben](https://msdn.microsoft.com/library/mt733217.aspx)
- [Bővített események](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Előfeltételek

Ez a témakör feltételezi, hogy már van némi ismerete:

- [Az Azure SQL Database szolgáltatás.](https://azure.microsoft.com/services/sql-database/)
- [Kiterjesztett események](https://msdn.microsoft.com/library/bb630282.aspx) a Microsoft SQL Server rendszerben.

- A kiterjesztett eseményekről szóló dokumentációnagy része az SQL Server és az SQL Database rendszerre egyaránt vonatkozik.

A következő elemeknek való előzetes kitettség akkor hasznos, ha az Eseményfájlt [választja célként:](#AzureXEventsTargets)

- [Azure Storage szolgáltatás](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Az Azure PowerShell használata az Azure Storage szolgáltatással](../storage/common/storage-powershell-guide-full.md) – Átfogó információkat nyújt a PowerShellről és az Azure Storage-szolgáltatásról.

## <a name="code-samples"></a>Kódminták

A kapcsolódó témakörök két kódmintát tartalmaznak:


- [Gyűrűpuffer célkódja az SQL Database kiterjesztett eseményeihez](sql-database-xevent-code-ring-buffer.md)
    - Rövid egyszerű Transact-SQL script.
    - A kódminta témakörben hangsúlyozzuk, hogy ha elkészült egy gyűrűpuffer-céllal, akkor `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` az alter-drop utasítás végrehajtásával fel kell szabadítania az erőforrásait. Később hozzáadhatja a gyűrűpuffer `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`egy másik példányát a.


- [Eseményfájl célkódja az SQL Database kiterjesztett eseményeihez](sql-database-xevent-code-event-file.md)
    - 1. fázis a PowerShell egy Azure Storage-tároló létrehozásához.
    - 2. fázis Transact-SQL, amely az Azure Storage-tárolót használja.

## <a name="transact-sql-differences"></a>Transact-SQL különbségek


- Amikor a [CREATE EVENT SESSION](https://msdn.microsoft.com/library/bb677289.aspx) parancsot hajtja végre az SQL Server kiszolgálón, az **ON SERVER** záradékot használja. De az SQL Database-ben az **ON DATABASE** záradékot kell használni.


- Az **ON DATABASE** záradék az ALTER EVENT [SESSION](https://msdn.microsoft.com/library/bb630368.aspx) és DROP [EVENT SESSION](https://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL parancsokra is vonatkozik.


- Ajánlott eljárás: a STARTUP_STATE = **ON** eseménymunkamenet-beállítás szerepeljen a **CREATE EVENT SESSION** vagy **alter event session** utasításokban.
    - Az **= ON** érték támogatja az automatikus újraindítást a logikai adatbázis feladatátvétel miatti újrakonfigurálása után.

## <a name="new-catalog-views"></a>Új katalógusnézetek

A kiterjesztett események funkciót több [katalógusnézet](https://msdn.microsoft.com/library/ms174365.aspx)is támogatja. A katalógusnézetek az aktuális adatbázisban a felhasználó által létrehozott *eseménymunkamenetek metaadatait vagy definícióit mesélik* el. A nézetek nem adnak vissza információt az aktív eseménymunkamenetek példányairól.

| A neve<br/>katalógus nézet | Leírás |
|:--- |:--- |
| **sys.database_event_session_actions** |Az eseménymunkamenet minden egyes eseményének minden művelethez egy sort ad vissza. |
| **sys.database_event_session_events** |Egy eseménymunkamenet minden eseményének egy sorát adja vissza. |
| **sys.database_event_session_fields** |Minden testreszabható oszlophoz egy sort ad vissza, amely kifejezetten eseményekre és célokra lett beállítva. |
| **sys.database_event_session_targets** |Egy eseményszakasz minden eseménycéljához egy sort ad eredményül. |
| **sys.database_event_sessions** |Az SQL Database adatbázis minden eseménymunkamenetéhez egy sort ad vissza. |

A Microsoft SQL Server rendszerben a hasonló katalógusnézetek neve *.server\_ * t tartalmaz az *.database\_* helyett. A név minta olyan, mint **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvs"></a>Új dinamikus felügyeleti nézetek [(DMV-k)](https://msdn.microsoft.com/library/ms188754.aspx)

Az Azure SQL Database [dinamikus felügyeleti nézeteket (DMV-k)](https://msdn.microsoft.com/library/bb677293.aspx) rendelkezik, amelyek támogatják a kiterjesztett eseményeket. A DMV-k az *aktív* eseménymunkamenetekről szólnak.

| DMV neve | Leírás |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Az eseménymunkamenet-műveletekre vonatkozó információkat ad vissza. |
| **sys.dm_xe_database_session_events** |A munkamenet-eseményekre vonatkozó információkat adja vissza. |
| **sys.dm_xe_database_session_object_columns** |A munkamenethez kötött objektumok konfigurációs értékeit jeleníti meg. |
| **sys.dm_xe_database_session_targets** |A munkamenet-célokra vonatkozó információkat adja vissza. |
| **sys.dm_xe_database_sessions** |Az aktuális adatbázishatókörrel tartozó minden egyes eseménymunkamenethez egy sort ad vissza. |

A Microsoft SQL Server rendszerben a * \_* hasonló katalógusnézetek neve a név adatbázisrésze nélkül történik, például:

- **sys.dm_xe_sessions**, név helyett<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMVs közös mindkét
A kiterjesztett eseményekhez további DMV-k is vannak, amelyek az Azure SQL Database és a Microsoft SQL Server számára is közösek:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>A rendelkezésre álló kiterjesztett események, műveletek és célok megkeresése

Futtathat egy egyszerű SQL **SELECT-t** az elérhető események, műveletek és cél listájának beszerzéséhez.

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Az SQL Database eseménymunkameneteinek céljai

Az alábbiakban olyan célokat olvashat, amelyek rögzítik az SQL Database eseménymunkameneteinek eredményeit:

- [Gyűrűpuffer-cél](https://msdn.microsoft.com/library/ff878182.aspx) – Röviden tárolja az eseményadatokat a memóriában.
- [Eseményszámláló-cél](https://msdn.microsoft.com/library/ff878025.aspx) – megszámolja a kiterjesztett események munkamenete során előforduló összes eseményt.
- [Eseményfájl-tároló](https://msdn.microsoft.com/library/ff878115.aspx) – teljes puffereket ír egy Azure Storage-tárolóba.

Az [Eseménykövetés Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) API nem érhető el az SQL Database kiterjesztett eseményeihez.

## <a name="restrictions"></a>Korlátozások

Az SQL Database felhőkörnyezetéhez illő biztonsági különbségek közül néhány megfelel:

- A kiterjesztett események az egybérlős elkülönítési modellen alapulnak. Az egyik adatbázis ban lévő eseménymunkamenet nem férhet hozzá egy másik adatbázis adataihoz vagy eseményeihez.
- A **főadatbázis** környezetében nem adhat ki **CREATE EVENT SESSION** utasítást.

## <a name="permission-model"></a>Engedélymodell

A **CREATE EVENT SESSION** utasítás kiállításához **vezérlőengedéllyel** kell rendelkeznie az adatbázishoz. Az adatbázis tulajdonosa (dbo) **rendelkezik vezérlési** engedéllyel.

### <a name="storage-container-authorizations"></a>Tároló engedélyezése

Az Azure Storage-tárolóhoz létrehozott SAS-jogkivonatnak meg kell adnia az **engedélyek rwl-jét.** Az **rwl** érték a következő engedélyeket biztosítja:

- Olvasás
- Írás
- Lista

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Vannak olyan esetek, ahol a kiterjesztett események intenzív használata több aktív memóriát halmozhat fel, mint ateljes rendszer számára. Ezért az Azure SQL Database rendszer dinamikusan beállítja és beállítja az eseménymunkamenet által felhalmozható aktív memória mennyiségére vonatkozó korlátokat. Számos tényező kerül be a dinamikus számításba.

Ha olyan hibaüzenet jelenik meg, amely szerint a memória maximális maximális értékét érvényesítették, néhány korrekciós műveletet végrehajthat:

- Kevesebb egyidejű eseménymunkamenet futtatása.
- Az eseménymunkamenetek **CREATE** és **ALTER** utasításain keresztül csökkentse a **MAX\_MEMÓRIA** záradékban megadott memóriamennyiséget.

### <a name="network-latency"></a>Hálózati késleltetés

Az **eseményfájl-tároló** hálózati késést vagy hibákat tapasztalhat, miközben megőrzi az Azure Storage-blobok adatait. Az SQL Database egyéb eseményei késhetnek, amíg a hálózati kommunikáció befejezésére várnak. Ez a késleltetés lelassíthatja a munkaterhelést.

- A teljesítménykockázat csökkentése érdekében ne adja meg a **EVENT_RETENTION_MODE** **beállítást,** hogy NO_EVENT_LOSS az eseménymunkamenet-definíciókban.

## <a name="related-links"></a>Kapcsolódó hivatkozások

- [Az Azure PowerShell használata az Azure Storage szolgáltatással.](../storage/common/storage-powershell-guide-full.md)
- [Azure storage-parancsmagok](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Az Azure PowerShell használata az Azure Storage szolgáltatással](../storage/common/storage-powershell-guide-full.md) – Átfogó információkat nyújt a PowerShellről és az Azure Storage-szolgáltatásról.
- [A Blob storage használata a .NET-ről](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [ESEMÉNYMUNKAMENET LÉTREHOZÁSA (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayias blogbejegyzései a Microsoft SQL Server ben történt kiterjesztett eseményekről](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Az Azure *Service Updates* weblap, paraméter szerint szűkül vethetve az Azure SQL Database-re:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


A kiterjesztett eseményekhez kapcsolódó egyéb kódmintatémakörök az alábbi hivatkozásokon érhetők el. Azonban rendszeresen ellenőriznie kell a mintát, hogy a minta a Microsoft SQL Server és az Azure SQL Database célzatait célozza-e. Ezután eldöntheti, hogy szükség van-e kisebb módosításokra a minta futtatásához.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
