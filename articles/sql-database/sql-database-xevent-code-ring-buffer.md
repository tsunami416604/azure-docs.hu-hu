---
title: Az SQL Database XEvent gyűrűpuffer kód |} A Microsoft Docs
description: A segítségével történik a gyűrűpuffer cél az Azure SQL Database gyors és egyszerű fényében, amelyek a Transact-SQL-mintakódot biztosít.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: f47c6b63ce5f18aa896b086ec884b9a3020cadd2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993278"
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Gyűrűpuffer célkódja az SQL Database bővített események

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Egy teszt során egy kiterjesztett esemény rögzítése és a jelentés információk gyors legegyszerűbb szeretne egy teljes körű kódmintát. A legegyszerűbb cél bővítettesemény-adatok a [kör céljába](https://msdn.microsoft.com/library/ff878182.aspx).

Ez a témakör egy kódmintát a Transact-SQL, amely:

1. Egy táblát hoz létre mutassa be az adatokat.
2. Nevezetesen hoz létre egy meglévő bővítettesemény-munkamenet **sqlserver.sql_statement_starting**.
   
   * Az esemény egy adott frissítés karakterláncot tartalmazó SQL-utasítások korlátozódik: **utasítás a(z) "% frissítés tabEmployee %" PÉLDÁUL**.
   * Úgy dönt, hogy elküldheti a kimenetet az esemény a cél típusa gyűrűpuffer, nevezetesen **package0.ring_buffer**.
3. Az esemény-munkamenet indítása.
4. FRISSÍTÉS az SQL-utasítások egyszerű néhány problémák.
5. Kibocsát egy kimeneti esemény lekérése gyűrűpuffer SQL SELECT utasításhoz.
   
   * **sys.dm_xe_database_session_targets** és más dinamikus felügyeleti nézetekkel (DMV-kkel) csatlakoznak.
6. Leállítja az esemény-munkamenet.
7. Csökken a gyűrűpuffer cél az erőforrások felszabadítása érdekében.
8. Az esemény-munkamenet, és a bemutató táblázat csökken.

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Regisztrálhat [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Létrehozhat egy táblát a bármely olyan adatbázisába.
  
  * Igény szerint is [hozzon létre egy **AdventureWorksLT** mintaadatbázis](sql-database-get-started.md) percek alatt.
* Az SQL Server Management Studio (ssms.exe), ideális legújabb havi frissítése verzióját. 
  Letöltheti a legújabb ssms.exe származó:
  
  * Című témakör [SQL Server Management Studio letöltését](https://msdn.microsoft.com/library/mt238290.aspx).
  * [A letöltésére mutató közvetlen hivatkozást.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Kódminta

Csak apró módosítással a következő gyűrűpuffer kódminta futtatható az Azure SQL Database vagy a Microsoft SQL Server. A különbség a "adatbá_zis" be néhány dinamikus felügyeleti nézetekkel (DMV-kkel) nevét az 5. lépés a FROM záradékban használt csomópont jelenléte. Példa:

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

## <a name="ring-buffer-contents"></a>Kör puffer tartalma

Ssms.exe segítségével futtathatja a kódot.

Az eredmények megtekintéséhez, hogy kattintott a cella alapján az oszlop fejlécére **target_data_XML**.

Az eredmények ablaktábláján azt kattintott a cella alapján az oszlop fejlécére, majd **target_data_XML**. Kattintson egy másik fájl lap, amelyben az eredmény cella tartalma zobrazilo, XML-fájlként ssms.exe hoz létre.

A kimenet a következő kódblokk jelenik meg. Hosszú úgy tűnik, de csak a két **<event>** elemeket.

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


#### <a name="release-resources-held-by-your-ring-buffer"></a>A gyűrűpuffer által tárolt erőforrások felszabadítása

Amikor végzett a gyűrűpuffer, távolítsa el, és a vállalati erőforrások felszabadítása egy **ALTER** , például a következőket:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


Az esemény-munkamenet definíciójának frissítése, de nem szakad meg. Később is hozzáadhat egy másik példánya gyűrűpuffer az esemény-munkamenethez:

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

Az Azure SQL Database bővített események elsődleges témakör a következő:

* [Esemény szempontok az SQL Database kiterjesztett](sql-database-xevent-db-diff-from-svr.md), amely egyes funkcióit, amelyek eltérnek a Microsoft SQL Server és Azure SQL Database bővített események ellentéte.

Bővített események minta kódja témaköröket érhetők el az alábbi hivatkozásokon. Azonban bármelyik mintát megtekintéséhez, hogy a minta célozza meg, a Microsoft SQL Server és Azure SQL Database rendszeresen ellenőrizze. Ezután eldöntheti, hogy kisebb módosításokat végez a minta futtatásához szükséges.

* Az Azure SQL Database-mintakód: [Eseményfájl célkódja az SQL Database bővített események](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
