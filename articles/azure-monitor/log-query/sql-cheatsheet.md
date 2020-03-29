---
title: SQL az Azure Monitor naplólekérdezési csalási lap | Microsoft dokumentumok
description: Segítség az SQL-t az Azure Monitor naplólekérdezéseinek írásában.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: c76ab145fd2fdd077075b345ecac9c6a473f2369
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75365189"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>SQL az Azure Monitor naplólekérdezési csalási lapjára 

Az alábbi táblázat segítségével a felhasználók, akik ismerik az SQL-t, hogy megtanulják a Kusto lekérdezési nyelv naplólekérdezések írása az Azure Monitorban. Tekintse meg a T-SQL parancsot a gyakori forgatókönyvek és az Azure Monitor naplólekérdezésmegfelelőinek megoldásához.

## <a name="sql-to-azure-monitor"></a>SQL az Azure Monitorra

Leírás                             |SQL-lekérdezés                                                                                          |Azure Monitor naplólekérdezése
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Táblázat összes adatának kijelölése            |`SELECT * FROM dependencies`                                                                       |<code>dependencies</code>
Adott oszlopok kijelölése táblázatból    |`SELECT name, resultCode FROM dependencies`                                                        |<code>dependencies <br>&#124; project name, resultCode</code>
100 rekord kijelölése táblából         |`SELECT TOP 100 * FROM dependencies`                                                               |<code>dependencies <br>&#124; take 100</code>
Null kiértékelés                         |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL`                                          |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
String összehasonlítás: egyenlőség             |`SELECT * FROM dependencies WHERE name = "abcde"`                                                  |<code>dependencies <br>&#124; where name == "abcde"</code>
Karakterlánc összehasonlítása: karakterlánc            |`SELECT * FROM dependencies WHERE name like "%bcd%"`                                                   |<code>dependencies <br>&#124; where name contains "bcd"</code>
Karakterlánc összehasonlítása: helyettesítő karakter             |`SELECT * FROM dependencies WHERE name like "abc%"`                                                |<code>dependencies <br>&#124; where name startswith "abc"</code>
Dátum összehasonlítása: utolsó 1 nap             |`SELECT * FROM dependencies WHERE timestamp > getdate()-1`                                         |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Dátum összehasonlítása: dátumtartomány             |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'`                 |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Logikai összehasonlítás                      |`SELECT * FROM dependencies WHERE !(success)`                                                      |<code>dependencies <br>&#124; where success == "False" </code>
Rendezés                                    |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc`                                  |<code>dependencies <br>&#124; order by timestamp asc </code>
Különböző                                |`SELECT DISTINCT name, type  FROM dependencies`                                                    |<code>dependencies <br>&#124; summarize by name, type </code>
Csoportosítás, összesítés                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Oszlopaliasok, Kiterjesztés                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Top n rekordok mérték szerint                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Union                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
Unió: feltételekkel                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5`                |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Csatlakozás                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>További lépések

- Az [Azure Monitor naplólekérdezéseinek írásával](get-started-queries.md)kapcsolatos leckék ben haladát.
