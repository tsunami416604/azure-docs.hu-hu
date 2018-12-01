---
title: SQL-t az Azure Log Analytics-nyelvi segédletünkkel |} A Microsoft Docs
description: Közös funkciók használata a Log Analytics-lekérdezéseket a különböző helyzetekhez.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 9ca8bb04748a1fd94c227627516be0e8ca83409d
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679766"
---
# <a name="sql-to-log-analytics-query-language-cheat-sheet"></a>SQL-t a Log Analytics lekérdezési nyelv – Adatlap 

Az alábbi táblázat segít a felhasználóknak, akik jól ismerik a Log Analytics lekérdezési nyelv tudnivalók az SQL. Rendelkezik egy pillantást egy gyakori forgatókönyvek és a Log Analytics használatával egyenértékű megoldására szolgáló a T-SQL paranccsal.

## <a name="sql-to-log-analytics"></a>A Log Analyticshez való SQL

Leírás                             |SQL-lekérdezés                                                                                          |Az Azure Log Analytics-lekérdezés
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Válassza ki az összes adat a táblázatból            |`SELECT * FROM dependencies`                                                                       |<code>dependencies</code>
Válassza ki az adott oszlopok egy táblából    |`SELECT name, resultCode FROM dependencies`                                                        |<code>dependencies <br>&#124; project name, resultCode</code>
Egy tábla 100 rekordok kiválasztása         |`SELECT TOP 100 * FROM dependencies`                                                               |<code>dependencies <br>&#124; take 100</code>
NULL értékű kiértékelése                         |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL`                                          |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Összehasonlítási karakterlánc: egyenlő             |`SELECT * FROM dependencies WHERE name = "abcde"`                                                  |<code>dependencies <br>&#124; where name == "abcde"</code>
Összehasonlítási karakterlánc: karakterláncrészletet            |`SELECT * FROM dependencies WHERE like "%bcd%"`                                                    |<code>dependencies <br>&#124; where name contains "bcd"</code>
Összehasonlítási karakterlánc: helyettesítő karakter             |`SELECT * FROM dependencies WHERE name like "abc%"`                                                |<code>dependencies <br>&#124; where name startswith "abc"</code>
Dátum összehasonlítása: utolsó nap             |`SELECT * FROM dependencies WHERE timestamp > getdate()-1`                                         |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Dátum összehasonlítása: dátumtartománya             |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'`                 |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Logikai összehasonlítása                      |`SELECT * FROM dependencies WHERE !(success)`                                                      |<code>dependencies <br>&#124; where success == "False" </code>
Rendezés                                    |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc`                                  |<code>dependencies <br>&#124; order by timestamp asc </code>
Különböző                                |`SELECT DISTINCT name, type  FROM dependencies`                                                    |<code>dependencies <br>&#124; summarize by name, type </code>
Csoportosítás, az összesítés                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Oszlopaliasnévként kiterjesztése                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Felső n recrods mérték által                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Union                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
Union: feltételekkel                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions value < 5`              |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Csatlakozás                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>További lépések

- A lecke meg a [lekérdezések írása a Log Analytics](get-started-queries.md).