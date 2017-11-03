---
title: "SQL-adatbázis XEvent gyűrűpuffer kód |} Microsoft Docs"
description: "Egy egyszerű és gyors gyűrűpuffer célként, az Azure SQL Database használata által készített Transact-SQL kódminta biztosít."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: 2510fb3f-c8f2-437a-8f49-9d5f6c96e75b
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genemi
ms.openlocfilehash: 61251eb9b125209ffd15adafdb0bace495e7cadd
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Az SQL-adatbázis kiterjesztett események puffer cél kódját gyűrű

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

A rögzítési jelentés adatai és a kiterjesztett esemény egy teszt során gyors legegyszerűbben teljes kódminta használni szeretne. A bővítettesemény-adatok legegyszerűbb célja a [gyűrűpuffer cél](http://msdn.microsoft.com/library/ff878182.aspx).

Ebből a témakörből megismerheti a Transact-SQL kódminta, amely:

1. Táblázatot hoz létre a adataival, hogy mutassa be.
2. Hoz létre egy munkamenetet egy meglévő kiterjesztett esemény, nevezetesen **sqlserver.sql_statement_starting**.
   
   * Az esemény egy adott frissítés karakterláncot tartalmazó SQL-utasítások korlátozódik: **PÉLDÁUL a "frissítés tabEmployee %" utasítás**.
   * Úgy dönt, hogy a kimenet az esemény küldése gyűrűpuffer, típusú cél nevezetesen **package0.ring_buffer**.
3. Az esemény-munkamenet indítása.
4. Néhány egyszerű frissítés az SQL-utasítások ad ki.
5. Problémák esemény kimeneti lekérése gyűrűpuffer tartalmazó SQL SELECT utasításhoz.
   
   * **sys.dm_xe_database_session_targets** és egyéb dinamikus felügyeleti nézetek (dinamikus felügyeleti nézetek).
6. Az esemény-munkamenet leállítása.
7. Csökken a gyűrűpuffer célként, az erőforrások kijelölése.
8. Az esemény-munkamenet és bemutató táblázat esik.

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Regisztrálhat [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Létrehozhat egy olyan táblázatában adatbázisoknak.
  
  * Igény szerint is [hozzon létre egy **AdventureWorksLT** mintaadatbázis](sql-database-get-started.md) perc múlva.
* SQL Server Management Studio (ssms.exe) ideális esetben a havi frissítés letöltéséhez. 
  Letöltheti a legújabb ssms.exe:
  
  * Című témakör [töltse le az SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
  * [A letöltés egy közvetlen hivatkozást.](http://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Kódminta

Nagyon kis módosítással a következő gyűrűpuffer kódminta futtatható Azure SQL Database vagy a Microsoft SQL Server. A különbség a csomópont "ír elé _adatbázis" nevében egyes dinamikus felügyeleti nézetek (dinamikus felügyeleti nézetek), 5. lépésben a FROM záradékban használt jelenlétét. Példa:

* sys.dm_xe**ír elé _adatbázis**_session_targets
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

## <a name="ring-buffer-contents"></a>Kör puffer tartalma

Ssms.exe segítségével futtathatja a kódot.

Az eredmények megtekintéséhez azt kattintott a cella az az oszlop fejlécére **target_data_XML**.

Az eredmények ablaktábláján jelenleg kattintott a cella az az oszlop fejlécére, majd **target_data_XML**. Kattintson egy másik fájl lapon létre ssms.exe, amelyben az eredmény cella tartalmát jelent meg, XML formátumban.

A kimenet látható a következő blokkot. A jelek hosszú, de csak a két  **<event>**  elemek.

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


#### <a name="release-resources-held-by-your-ring-buffer"></a>A gyűrűpuffer birtokában kiadás erőforrások

Amikor elkészült, a kör pufferrel, távolítsa el, és a vállalati erőforrások kiadási egy **ALTER** , például a következőket:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


Az esemény-munkamenet definíciójának frissítése, de nem szakad meg. Később gyűrűpuffer egy másik példánya is hozzáadhat az esemény-munkamenethez:

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

Az Azure SQL Database-kiterjesztett események elsődleges témakör van:

* [Kiterjesztett esemény szempontok az SQL-adatbázis](sql-database-xevent-db-diff-from-svr.md), amely az ellenkezőjét szemlélteti a kiterjesztett az eseményeket, amelyek eltérnek a Microsoft SQL Server és az Azure SQL Database egyes funkcióit.

Más kód a minta témakörök kiterjesztett események a következő hivatkozások webhelyen érhetők el. Azonban rendszeresen ellenőrizni kell a minta megtekintéséhez, hogy a minta irányul-e a Microsoft SQL Server és az Azure SQL Database. Ezután eldöntheti, hogy kisebb módosításokat a minta futtatásához szükség van-e.

* Az Azure SQL Database kódminta: [Eseményfájlt cél kódot az SQL-adatbázis kiterjesztett események](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
