---
title: Bővített események SQL Database-ben |} A Microsoft Docs
description: Ismerteti az Azure SQL Database bővített események (XEvents), és hogyan esemény-munkamenet némileg eltér a Microsoft SQL Server esemény-munkamenet.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 2dd3b9f0d1d8d61b2311977774c8b0f7267caa9e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090610"
---
# <a name="extended-events-in-sql-database"></a>Az SQL Database bővített események
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Ez a témakör elmagyarázza, hogyan megvalósítása az Azure SQL Database bővített események kissé eltérő a Microsoft SQL Server bővített események képest.

- Az SQL Database V12-es révén a bővített események funkció a második fele naptár 2015.
- Az SQL Server 2008 óta bővített események esetében.
- A funkció olyan az SQL Database bővített események, az SQL Server funkcióinak egy robusztus részét.

*Xevent típusú eseményekhez* blogok és más informális helyeken egy informális becenév, amely használható a "bővített események" szerepel.

Az Azure SQL Database és a Microsoft SQL Server kiterjesztett eseményekkel kapcsolatos további információkat érhető el:

- [Gyors üzembe helyezés: Az SQL Server bővített események](https://msdn.microsoft.com/library/mt733217.aspx)
- [Bővített események](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Előfeltételek

Ez a témakör feltételezi, hogy már rendelkezik néhány ismeretére:

- [Az Azure SQL Database szolgáltatás](https://azure.microsoft.com/services/sql-database/).
- [Bővített események](https://msdn.microsoft.com/library/bb630282.aspx) a Microsoft SQL Server.

- A kiterjesztett eseményekkel kapcsolatos dokumentációnkat nagy az SQL Server és az SQL Database vonatkozik.

Előzetes kitéve a következő elemek hasznos, amikor az esemény fájlt kiválasztása a [cél](#AzureXEventsTargets):

- [Az Azure Storage szolgáltatás](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Azure PowerShell-lel az Azure Storage](../storage/common/storage-powershell-guide-full.md) -átfogó arról nyújt tájékoztatást, PowerShell és az Azure Storage szolgáltatást.

## <a name="code-samples"></a>Kódminták

Kapcsolódó témakörök két Kódminták rendelkezésre bocsátása:


- [Gyűrűpuffer célkódja az SQL Database bővített események](sql-database-xevent-code-ring-buffer.md)
    - Rövid egyszerű Transact-SQL parancsfájl.
    - Tudjuk, hogy végzett egy kör céljába, amikor fel kell szabadítani az erőforrásait egy alter vidd végrehajtásával kód minta témakör hangsúlyozzák `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` utasítást. Később hozzáadhat egy másik példánya által gyűrűpuffer `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Eseményfájl célkódja az SQL Database bővített események](sql-database-xevent-code-event-file.md)
    - 1. fázis a PowerShell segítségével hozzon létre egy Azure Storage-tárolóba.
    - 2. fázis a Transact-SQL az Azure Storage-tárolót használó.

## <a name="transact-sql-differences"></a>A Transact-SQL eltérései


- Végrehajtása közben a [munkamenet esemény létrehozása](https://msdn.microsoft.com/library/bb677289.aspx) parancsot SQL-kiszolgálón használja a **ON SERVER** záradékban. Azonban az SQL Database használata a **ON adatbázis** záradék helyett.


- A **ON adatbázis** záradék is vonatkozik a [ALTER esemény munkamenet](https://msdn.microsoft.com/library/bb630368.aspx) és [DROP esemény munkamenet](https://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL-parancsokat.


- Ajánlott eljárás, hogy tartalmazzák az esemény-munkamenet lehetőség a **STARTUP_STATE = ON** a a **munkamenet esemény létrehozása** vagy **ALTER esemény munkamenet** utasításokat.
    - A **= ON** értéket támogatja az automatikus újraindítást egy újrakonfigurálása a logikai adatbázishoz, mert a feladatátvétel után.

## <a name="new-catalog-views"></a>Új Rendszerkatalógus-nézetek

A bővített események támogatja több [nézetek katalógus](https://msdn.microsoft.com/library/ms174365.aspx). Rendszerkatalógus-nézetek tájékoztatni a *metaadatok vagy definíciókat* a felhasználó által létrehozott esemény-munkamenetek az aktuális adatbázisban. A nézetek nem adott vissza aktív esemény-munkamenet példányaira vonatkozó információkat.

| Neve<br/>Katalógus megtekintése | Leírás |
|:--- |:--- |
| **sys.database_event_session_actions** |Minden egyes esemény egy esemény-munkamenet minden művelet egy sort ad vissza. |
| **sys.database_event_session_events** |Egy esemény-munkamenet minden egyes esemény egy sort adja vissza. |
| **sys.database_event_session_fields** |Minden egyes testreszabása – képes oszlop, amelyet kifejezetten az események és a célokat egy sort adja vissza. |
| **sys.database_event_session_targets** |Egy esemény-munkamenet minden egyes esemény cél egy sort adja vissza. |
| **sys.database_event_sessions** |Minden egyes esemény-munkamenet az SQL Database-adatbázis egy sort adja vissza. |

A Microsoft SQL Server, hasonló katalógusnézetekre tartalmazó névvel rendelkeznek *.server\_*  helyett *.database\_*. A minta olyan, mintha **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvshttpsmsdnmicrosoftcomlibraryms188754aspx"></a>Új dinamikus felügyeleti nézetek [(DMV-kkel)](https://msdn.microsoft.com/library/ms188754.aspx)

Az Azure SQL Database rendelkezik [dinamikus felügyeleti nézetekkel (DMV-kkel)](https://msdn.microsoft.com/library/bb677293.aspx) , amely támogatja a bővített események. Dinamikus felügyeleti nézetek tájékoztatni a *aktív* esemény-munkamenet.

| DMV neve | Leírás |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Esemény-munkamenet műveleteivel kapcsolatos információkat ad vissza. |
| **sys.dm_xe_database_session_events** |Munkamenet-eseményekkel kapcsolatos információkat ad vissza. |
| **sys.dm_xe_database_session_object_columns** |Vannak kötve egy munkamenet-objektumok konfigurációs értékeit jeleníti meg. |
| **sys.dm_xe_database_session_targets** |Munkamenet célok kapcsolatos információkat ad vissza. |
| **sys.dm_xe_database_sessions** |Az aktuális adatbázisra minden egyes esemény-munkamenet hatókörű egy sort adja vissza. |

A Microsoft SQL Server, hasonló katalógusnézetekre nevű nélkül a  *\_adatbázis* része a nevét, például:

- **sys.dm_xe_sessions**, a neve helyett<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>Gyakori, mind a DMV-kkel
Bővített események nincsenek további dinamikus felügyeleti nézetek, amelyek közösek az Azure SQL Database és a Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Keresse meg a rendelkezésre álló bővített események, műveletek és célok

Futtathat egy egyszerű SQL **kiválasztása** a rendelkezésre álló események, műveletek és a cél egy listájának beszerzése érdekében.

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Az SQL Database esemény-munkamenet célértékei

Az alábbiakban rögzítheti az esemény-munkamenet az SQL Database eredményeinek tárolók:

- [Gyűrűpuffer céljába](https://msdn.microsoft.com/library/ff878182.aspx) -rövid ideig tárolja az eseményadatokat a memóriában.
- [Esemény számláló cél](https://msdn.microsoft.com/library/ff878025.aspx) – minden bővített események munkamenet során előforduló események száma.
- [Eseményfájl céljába](https://msdn.microsoft.com/library/ff878115.aspx) – teljes pufferek ír egy Azure Storage-tárolóba.

A [esemény-nyomkövetése Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) API nem érhető el az SQL Database bővített események.

## <a name="restrictions"></a>Korlátozások

Van néhány biztonsággal kapcsolatos különbségek az SQL Database a felhőalapú környezet befitting:

- Bővített események alapja az egybérlős elkülönítési modellt. Egy esemény-munkamenet egy adatbázis nem érhető el adatok vagy események másik adatbázisból.
- Nem adható ki egy **munkamenet esemény létrehozása** utasítás kontextusában a **fő** adatbázis.

## <a name="permission-model"></a>Engedélyezési modellekkel

Rendelkeznie kell **vezérlő** engedéllyel kibocsátani egy **munkamenet esemény létrehozása** utasítást. Az adatbázis-tulajdonos (dbo) rendelkezik **vezérlő** engedéllyel.

### <a name="storage-container-authorizations"></a>Storage-tároló engedélyek

A SAS-jogkivonatot hoz létre az Azure Storage-tároló meg kell adnia a **rwl** engedélyek. A **rwl** érték a következő engedélyeket biztosítja:

- Olvasás
- Írás
- Lista

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Ahol a bővített események nagy számításigényű használatát több aktív memória, mint a teljes rendszer kifogástalan felhalmozhat forgatókönyv közül választhat. Az Azure SQL Database rendszer ezért dinamikusan állítja be, és beállítja a határértékeket is összegyűjthetők egy esemény-munkamenet aktív memória mennyisége. Sok tényező befolyásolja a dinamikus számítási lépnek.

Arról, hogy a memória maximális kényszerítve lenne hibaüzenetet kap, ha néhány elvégezhető javítási műveletek a következők:

- Futtassa a kevesebb egyidejű esemény-munkamenet.
- Keresztül a **létrehozás** és **ALTER** utasításokat az esemény-munkamenet, csökkentse a memóriamennyiség, amelyet meg kell adnia a **maximális\_memória** záradékban.

### <a name="network-latency"></a>Hálózati késleltetés

A **Eseményfájl** cél tapasztalhat, hálózati késés vagy hibák során az adatok megtartását az Azure Storage-blobokat. SQL Database-ben az eseményeket késésével addig azokat a hálózati kommunikáció végrehajtásához. Ez a késleltetés is csökkentheti a számítási feladatok.

- A teljesítmény kockázat csökkentése érdekében, elkerülheti a beállítás a **EVENT_RETENTION_MODE** beállítást **NO_EVENT_LOSS** az esemény-munkamenet definíciókban.

## <a name="related-links"></a>Kapcsolódó hivatkozások

- [Az Azure PowerShell-lel és az Azure Storage](../storage/common/storage-powershell-guide-full.md).
- [Az Azure Storage-parancsmagok](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Azure PowerShell-lel az Azure Storage](../storage/common/storage-powershell-guide-full.md) -átfogó arról nyújt tájékoztatást, PowerShell és az Azure Storage szolgáltatást.
- [A Blob storage a .NET használatával](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayias blogbejegyzések arról a Microsoft SQL Server bővített események](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Az Azure *szolgáltatásfrissítések* weblapon, paraméterben az Azure SQL Database leszűkül:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Bővített események minta kódja témaköröket érhetők el az alábbi hivatkozásokon. Azonban bármelyik mintát megtekintéséhez, hogy a minta célozza meg, a Microsoft SQL Server és Azure SQL Database rendszeresen ellenőrizze. Ezután eldöntheti, hogy kisebb módosításokat végez a minta futtatásához szükséges.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
