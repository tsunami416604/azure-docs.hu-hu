---
title: "Az SQL-adatbázis események kiterjesztett |} Microsoft Docs"
description: "Kiterjesztett események (Xevent) az Azure SQL Database, és hogyan esemény-munkamenet némileg eltér a Microsoft SQL Server esemény-munkamenet ismerteti."
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.topic: article
ms.date: 02/03/2017
ms.author: genemi
ms.openlocfilehash: dc4660b33d8df28fd59929838e054f6703c48107
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="extended-events-in-sql-database"></a>Az SQL-adatbázis kiterjesztett események
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Ez a témakör elmagyarázza, hogyan kiterjesztett eseményeket az Azure SQL Database végrehajtásának némileg eltérnek a Microsoft SQL Server kiterjesztett események képest.

- SQL Database 12-es szerzett a kiterjesztett események a szolgáltatás második fele naptár 2015.
- SQL Server 2008 óta kiterjesztett esemény esetében.
- Az SQL-adatbázis kiterjesztett események szolgáltatás az SQL Server funkcióinak stabil részhalmazának lesz.

*Xevent típusú eseményekhez* van egy nem hivatalos becenév, amely használható a "kiterjesztett események" blogok és egyéb személyes helyekre.

További információt az Azure SQL Database és a Microsoft SQL Server kiterjesztett események érhető el:

- [– Első lépések: SQL Server kiterjesztett események](http://msdn.microsoft.com/library/mt733217.aspx)
- [Kiterjesztett események](http://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Előfeltételek

Ez a témakör feltételezi, hogy már rendelkezik bizonyos:

- [Az Azure SQL Database szolgáltatás](https://azure.microsoft.com/services/sql-database/).
- [Események kiterjesztett](http://msdn.microsoft.com/library/bb630282.aspx) a Microsoft SQL Server kiszolgálón.

- A kiterjesztett eseményekről dokumentációban nagy az SQL Server és SQL-adatbázis vonatkozik.

A következő elemek előzetes kitéve akkor hasznos, ha az esemény fájl kiválasztása a [cél](#AzureXEventsTargets):

- [Azure Storage service](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Azure PowerShell használata az Azure Storage](../storage/common/storage-powershell-guide-full.md) -PowerShell és az Azure Storage szolgáltatás átfogó információkat nyújt.

## <a name="code-samples"></a>Kódminták

Kapcsolódó témakörök nyújtanak két mintakódok:


- [Az SQL-adatbázis kiterjesztett események puffer cél kódját gyűrű](sql-database-xevent-code-ring-buffer.md)
    - Rövid egyszerű Transact-SQL-parancsfájlt.
    - Azt, hogy amikor elkészült, gyűrűpuffer cél, fel kell szabadítani az erőforrásait hajtja végre az alter vidd kód a minta témakör hangsúlyozzák `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` utasítást. Később hozzáadhat egy másik példánya által gyűrűpuffer `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Fájl cél eseménykód kiterjesztett események az SQL-adatbázis](sql-database-xevent-code-event-file.md)
    - 1. fázis egy Azure Storage-tároló létrehozása a PowerShell.
    - 2. fázis Transact-SQL Azure tárolót használó.

## <a name="transact-sql-differences"></a>A Transact-SQL eltérései


- Amikor a [munkamenet esemény létrehozása](http://msdn.microsoft.com/library/bb677289.aspx) parancsot SQL-kiszolgálón használja a **ON SERVER** záradékban. Azonban az SQL-adatbázis használata a **ON adatbázis** záradék helyett.


- A **ON adatbázis** záradék is vonatkozik a [ALTER esemény munkamenet](http://msdn.microsoft.com/library/bb630368.aspx) és [DROP esemény munkamenet](http://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL-parancsokat.


- Ajánlott a az esemény-munkamenet kapcsolóhoz **STARTUP_STATE = ON** a a **munkamenet esemény létrehozása** vagy **ALTER esemény munkamenet** utasításokat.
    - A **= ON** értéket támogatja az automatikus újraindítást egy újrakonfigurálása az logikai adatbázisból, mert a feladatátvétel után.

## <a name="new-catalog-views"></a>Új katalógusnézetekre

A kiterjesztett események támogatja több [nézetek katalógus](http://msdn.microsoft.com/library/ms174365.aspx). Katalógusnézetekre tájékoztatni a *metaadatok vagy definíciók* a felhasználó által létrehozott esemény-munkamenetek az aktuális adatbázisban. A nézetek aktív esemény-munkamenet példányaira vonatkozó információk nem adják vissza.

| Neve<br/>katalógusnézet használatával derítheti ki | Leírás |
|:--- |:--- |
| **sys.database_event_session_actions** |Egy esemény-munkamenet eseményeket minden egyes művelethez egy sort adja vissza. |
| **sys.database_event_session_events** |Egy esemény-munkamenet minden esemény egy sort adja vissza. |
| **sys.database_event_session_fields** |Az explicit módon lett-e beállítva a következő események és célok testreszabásához-képes oszlopok egy sort adja vissza. |
| **sys.database_event_session_targets** |Egy esemény-munkamenet egy sor minden esemény cél beolvasása. |
| **sys.database_event_sessions** |Minden esemény-munkamenethez, az SQL Database adatbázisban egy sort adja vissza. |

A Microsoft SQL Server, hasonló katalógusnézetekre tartalmazó névvel rendelkeznek *.server\_*  helyett *.database\_*. A minta nem például **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvshttpmsdnmicrosoftcomlibraryms188754aspx"></a>Új dinamikus felügyeleti nézetekkel [(dinamikus felügyeleti nézetek)](http://msdn.microsoft.com/library/ms188754.aspx)

Az Azure SQL-adatbázis [dinamikus felügyeleti nézetekkel (dinamikus felügyeleti nézetek)](http://msdn.microsoft.com/library/bb677293.aspx) , amely támogatja a bővített események. Dinamikus felügyeleti nézetek tájékoztatni a *aktív* esemény-munkamenet.

| DMV neve | Leírás |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Az esemény-munkamenet műveletek információt ad vissza. |
| **sys.dm_xe_database_session_events** |Munkamenet-események információt ad vissza. |
| **sys.dm_xe_database_session_object_columns** |A munkamenet kötött objektumok konfigurációs értékeit tartalmazza. |
| **sys.dm_xe_database_session_targets** |Munkamenet célok információt ad vissza. |
| **sys.dm_xe_database_sessions** |Az aktuális adatbázisra minden esemény-munkamenet, melyek hatókörébe tartozik egy sort adja vissza. |

A Microsoft SQL Server, hasonló katalógus nézetek nevű nélkül a  *\_adatbázis* részét, például:

- **sys.dm_xe_sessions**, a neve helyett<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>Dinamikus felügyeleti nézetek egyaránt előforduló
Kiterjesztett események számos további dinamikus felügyeleti nézetek, amelyek az Azure SQL Database és a Microsoft SQL Server egyaránt.

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Keresés a rendelkezésre álló kiterjesztett események, műveletek és célok

Egy egyszerű SQL futtatása **válasszon** a rendelkezésre álló események, a műveletek és a cél listájának beszerzése.

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Az SQL-adatbázis esemény-munkamenet célok

Az alábbiakban rögzítheti az esemény-munkamenet az SQL Database eredményeinek tárolók:

- [Ring pufferbe cél](http://msdn.microsoft.com/library/ff878182.aspx) -eseményadatok röviden, amely a memóriában tárolja.
- [Esemény számláló cél](http://msdn.microsoft.com/library/ff878025.aspx) -kiterjesztett események munkamenet során előforduló összes események száma.
- [Esemény cél](http://msdn.microsoft.com/library/ff878115.aspx) -teljes pufferek ír egy Azure Storage-tárolót.

A [esemény Windows (nyomkövetés)](http://msdn.microsoft.com/library/ms751538.aspx) API nem érhető el az SQL-adatbázis kiterjesztett események.

## <a name="restrictions"></a>Korlátozások

Többféle befitting a felhőalapú környezetben SQL-adatbázis biztonsági különbségek:

- Kiterjesztett események alapja a single-bérlő elkülönítési modellt. Egy esemény-munkamenet egy adatbázis nem érhető el adatok vagy események másik adatbázisból.
- Nem adható ki egy **munkamenet esemény létrehozása** utasítás kontextusában a **fő** adatbázis.

## <a name="permission-model"></a>Engedély modell

Rendelkeznie kell **vezérlő** engedéllyel az adatbázishoz ki egy **munkamenet esemény létrehozása** utasítást. Az adatbázis-tulajdonosi (dbo) rendelkezik **vezérlő** engedéllyel.

### <a name="storage-container-authorizations"></a>Tárolási tároló engedélyek

A SAS-jogkivonatot hoz létre az Azure Storage-tároló meg kell **rwl** az engedélyek. A **rwl** érték a következő engedélyeket biztosítja:

- Olvasás
- Írás
- Lista

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Ott, ahol kiterjesztett események intenzív használja, mint a teljes rendszer kifogástalan több aktív memória felhalmozhat forgatókönyv áll. Az Azure SQL Database rendszer ezért dinamikusan állítja be, és beállítja a vonatkozó korlátozások is összegyűjthetők, az esemény-munkamenet aktív memória mennyiségét. Számos tényező közé tartoznak a dinamikus számítási kísérhet.

Arról, hogy a maximális memória kényszerítve lenne hibaüzenetet kap, ha néhány elvégezhető javítási műveletek a következők:

- Futtassa a kevesebb egyidejű esemény-munkamenet.
- Keresztül a **létrehozása** és **ALTER** nyilatkozatait, esemény-munkamenet, csökkentse a memóriamennyiség, meg kell adnia a **maximális\_memória** záradékban.

### <a name="network-latency"></a>Hálózati késleltetés

A **Eseményfájlt** cél tapasztalhat, hálózati késés vagy hibák során az Azure Storage blobs adatait. Az SQL-adatbázis az eseményeket tarthat, amíg azok Várjon, amíg befejeződik a hálózati kommunikáció. Ez a késés lelassíthatja a terhelést.

- A teljesítmény kockázat csökkentése érdekében, elkerülheti a beállítás a **EVENT_RETENTION_MODE** lehetőséggel **NO_EVENT_LOSS** az esemény-munkamenet definíciókban.

## <a name="related-links"></a>Kapcsolódó hivatkozások

- [Az Azure PowerShell használata az Azure Storage](../storage/common/storage-powershell-guide-full.md).
- [Az Azure Storage-parancsmagok](http://msdn.microsoft.com/library/dn806401.aspx)
- [Azure PowerShell használata az Azure Storage](../storage/common/storage-powershell-guide-full.md) -PowerShell és az Azure Storage szolgáltatás átfogó információkat nyújt.
- [A .NET-Blob-tároló használata](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)
- [A Microsoft SQL Server kiterjesztett eseményekről visszaküldés Jonathan Kehayias blog](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Az Azure *szolgáltatásfrissítések* weblap, az Azure SQL Database paraméterrel maradt:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Más kód a minta témakörök kiterjesztett események a következő hivatkozások webhelyen érhetők el. Azonban rendszeresen ellenőrizni kell a minta megtekintéséhez, hogy a minta irányul-e a Microsoft SQL Server és az Azure SQL Database. Ezután eldöntheti, hogy kisebb módosításokat a minta futtatásához szükség van-e.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
