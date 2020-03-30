---
title: XEvent ring pufferkódja
description: Egy Transact-SQL-kódmintát biztosít, amely az Azure SQL Database-ben a ring buffer cél használatával egyszerűen és gyorsan használható.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: ad98b61d6339388551af93671b3d4d892942f4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213969"
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Gyűrűpuffer célkódja az SQL Database kiterjesztett eseményeihez

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Teljes kódmintát szeretne a tesztelés során egy kiterjesztett esemény adatainak rögzítésére és jelentésére szolgáló legegyszerűbb gyors módszer érdekében. A kiterjesztett eseményadatok legegyszerűbb célpontja a [gyűrűpuffer-cél.](https://msdn.microsoft.com/library/ff878182.aspx)

Ez a témakör egy Transact-SQL kódmintát mutat be, amely:

1. Létrehoz egy táblázatot, amelynek adatait be kell mutatni.
2. Munkamenetet hoz létre egy meglévő kiterjesztett eseményhez, nevezetesen **az sqlserver.sql_statement_starting.**
   
   * Az esemény egy adott frissítési karakterláncot tartalmazó SQL utasításokra korlátozódik: **A LIKE '%UPDATE tabEmployee%' utasítás.**
   * Úgy dönt, hogy az esemény kimenetét egy Ring Buffer típusú célnak, azaz **a 0.ring_buffer.**
3. Elindítja az eseménymunkamenetet.
4. Kiad néhány egyszerű SQL UPDATE utasítást.
5. SQL SELECT utasítást ad ki az eseménykimenet lekéréséhez a gyűrűpufferből.
   
   * **a sys.dm_xe_database_session_targets** és más dinamikus felügyeleti nézetek (DMV-k) összevannak kötve.
6. Leállítja az eseménymunkamenetet.
7. Eldobja a gyűrűpuffer-célt, hogy felszabadítsa az erőforrásait.
8. Eldobja az eseménymenetet és a demótáblát.

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Regisztrálhat [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Bármely adatbázis, amelyben táblát hozhat létre.
  
  * Tetszés szerint percek [alatt létrehozhat egy **AdventureWorksLT** bemutató adatbázist.](sql-database-get-started.md)
* SQL Server Management Studio (ssms.exe), ideális esetben a legújabb havi frissítési verzió. 
  A legújabb ssms.exe fájl letölthető a következő oldalról:
  
  * Az [SQL Server Management Studio letöltése](https://msdn.microsoft.com/library/mt238290.aspx)című témakörben.
  * [Közvetlen link a letöltéshez.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Kódminta

Nagyon kis módosítással a következő ring buffer kód minta futtatható az Azure SQL Database vagy a Microsoft SQL Server. A különbség a "_database" csomópont jelenléte néhány dinamikus felügyeleti nézet (DMV) nevében, amelyet az 5. Példa:

* sys.dm_xe<strong>_database</strong>_session_targets
* sys.dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;

## <a name="ring-buffer-contents"></a>Gyűrűpuffer tartalma

Az ssms.exe-t használtuk a kódminta futtatásához.

Az eredmények megtekintéséhez az oszlopfejléc alatti cellára kattintottunk **target_data_XML.**

Ezután az eredmények ablaktáblán rákattintottunk az oszlopfejléc alatti cellára **target_data_XML.** Ez a kattintás egy másik fájllapot hozott létre az ssms.exe fájlban, amelyben az eredménycella tartalma XML-ként jelent meg.

A kimenet a következő blokkban jelenik meg. Úgy néz ki, hosszú, ** \<** de ez csak két esemény>elemek.

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### <a name="release-resources-held-by-your-ring-buffer"></a>A gyűrűpuffer birtokában lévő erőforrások felszabadítása

Ha végzett a gyűrűpufferrel, eltávolíthatja azt, és felszabadíthatja az **ALTER-t** kibocsátó erőforrásait, mint például:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


Az eseménymunkamenet definíciója frissül, de nem kerül kiakadt. Később a csengetési puffer egy másik példányát is hozzáadhatja az eseménymunkamenethez:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```


## <a name="more-information"></a>További információ

Az Azure SQL Database kiterjesztett eseményeinek elsődleges témaköre a következő:

* [Kiterjesztett esemény szempontok az SQL Database,](sql-database-xevent-db-diff-from-svr.md)amely ellentétben áll az Azure SQL Database és a Microsoft SQL Server között eltérő kiterjesztett események bizonyos aspektusait.

A kiterjesztett eseményekhez kapcsolódó egyéb kódmintatémakörök az alábbi hivatkozásokon érhetők el. Azonban rendszeresen ellenőriznie kell a mintát, hogy a minta a Microsoft SQL Server és az Azure SQL Database célzatait célozza-e. Ezután eldöntheti, hogy szükség van-e kisebb módosításokra a minta futtatásához.

* Kódminta az Azure SQL Database-hez: [Eseményfájl célkód az SQL-adatbázis ban bekövetkező kiterjesztett eseményekhez](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
