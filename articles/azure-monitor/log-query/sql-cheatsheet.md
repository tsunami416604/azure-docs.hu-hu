---
title: SQL – Azure Monitor log Query – Cheat Sheet | Microsoft Docs
description: Súgó az SQL-t használó felhasználók számára a Azure Monitorban található naplók írásakor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 4acf3c2f8cee3ca9e679915eec677b6dd92792bf
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932914"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>SQL – Azure Monitor log Query – Cheat Adatlap 

Az alábbi táblázat segíti a felhasználókat, akik ismerik az SQL-t, hogy megismerjék a Kusto lekérdezési nyelvét, hogy írási lekérdezéseket írjanak Azure Monitor. Tekintse meg a T-SQL-parancsot a gyakori forgatókönyvek megoldásához és az azzal egyenértékű Azure Monitor log lekérdezésben.

## <a name="sql-to-azure-monitor"></a>SQL – Azure Monitor

Leírás                             |SQL-lekérdezés                                                                                          |Azure Monitor napló lekérdezése
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Összes adatok kijelölése egy táblából            |`SELECT * FROM dependencies`                                                                       |<code>dependencies</code>
Adott oszlopok kiválasztása egy táblából    |`SELECT name, resultCode FROM dependencies`                                                        |<code>dependencies <br>&#124; project name, resultCode</code>
Válasszon 100 rekordot egy táblából         |`SELECT TOP 100 * FROM dependencies`                                                               |<code>dependencies <br>&#124; take 100</code>
NULL Értékelés                         |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL`                                          |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Karakterlánc-összehasonlítás: egyenlőség             |`SELECT * FROM dependencies WHERE name = "abcde"`                                                  |<code>dependencies <br>&#124; where name == "abcde"</code>
Karakterlánc-összehasonlítás: alkarakterlánc            |`SELECT * FROM dependencies WHERE name like "%bcd%"`                                                   |<code>dependencies <br>&#124; where name contains "bcd"</code>
Karakterlánc-összehasonlítás: helyettesítő karakter             |`SELECT * FROM dependencies WHERE name like "abc%"`                                                |<code>dependencies <br>&#124; where name startswith "abc"</code>
Dátum összehasonlítása: az elmúlt 1 nap             |`SELECT * FROM dependencies WHERE timestamp > getdate()-1`                                         |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Dátum összehasonlítása: dátumtartomány             |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'`                 |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Logikai összehasonlítás                      |`SELECT * FROM dependencies WHERE !(success)`                                                      |<code>dependencies <br>&#124; where success == "False" </code>
Rendezés                                    |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc`                                  |<code>dependencies <br>&#124; order by timestamp asc </code>
Különböző                                |`SELECT DISTINCT name, type  FROM dependencies`                                                    |<code>dependencies <br>&#124; summarize by name, type </code>
Csoportosítás, összesítés                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Oszlop aliasnevei, kiterjesztés                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Legfontosabb n rekordok mérték szerint                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Union                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
Unió: feltételekkel                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5`                |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Csatlakozás                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Következő lépések

- Ugorjon végig a [Azure monitorban található naplók írásához](get-started-queries.md).
