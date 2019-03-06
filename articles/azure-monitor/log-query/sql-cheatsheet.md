---
title: SQL-t az Azure Monitor log-lekérdezés Adatlap |} A Microsoft Docs
description: Segít a jól ismert SQL-felhasználók számára az Azure Monitor log-lekérdezések írásának módját.
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
ms.openlocfilehash: 6a63ddc6b9a1640c8e0986a221c8da6f694b2800
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454136"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>SQL-t az Azure Monitor log-lekérdezés – Adatlap 

Az alábbi táblázat segít a felhasználóknak, akik ismerik az SQL-napló lekérdezéseket írni az Azure monitorban Kusto lekérdezési nyelv tudnivalók. Rendelkezik egy pillantást a T-SQL parancsot egy olyan gyakori helyzetet és az Azure Monitor log-lekérdezést az azzal egyenértékű megoldására.

## <a name="sql-to-azure-monitor"></a>SQL-t az Azure Monitor

Leírás                             |SQL Query                                                                                          |Az Azure Monitor naplólekérdezés
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
Grouping, Aggregation                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Oszlopaliasnévként kiterjesztése                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Felső n rekordok mérték által                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Union                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
Union: feltételekkel                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions value < 5`              |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Csatlakozás                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>További lépések

- A lecke meg a [log lekérdezések írásának módját az Azure monitorban](get-started-queries.md).