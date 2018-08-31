---
title: Kapacitási korlátok – Azure SQL Data Warehouse |} A Microsoft Docs
description: Azure SQL Data Warehouse különböző összetevői számára engedélyezett maximális értékeket.
services: sql-data-warehouse
author: sachinpMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 07/26/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 7c6445624b2c03497c881b0c34bac8256fa28a98
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43302043"
---
# <a name="sql-data-warehouse-capacity-limits"></a>Az SQL Data Warehouse kapacitáskorlátait
Azure SQL Data Warehouse különböző összetevői számára engedélyezett maximális értékeket.

## <a name="workload-management"></a>Számítási feladatok kezelése
| Kategória | Leírás | Maximum |
|:--- |:--- |:--- |
| [Az Adattárházegységek (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Maximális DWU egy egyetlen SQL Data warehouse-hoz | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Az Adattárházegységek (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Dtu-k alapértelmezett kiszolgálónként |54,000<br></br>Alapértelmezés szerint minden SQL-kiszolgáló (például a myserver.database.windows.net) rendelkezik a DTU-kvótát 54 000, amely lehetővé teszi, hogy legfeljebb DW6000c. Ez a kvóta egyszerűen egy biztonsági korlát. Növelheti a kvóta által [támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md) és kiválasztásával *kvóta* a kérelem típusaként.  A DTU kiszámításához kell, DWU szükséges az összes szorozzuk meg a 7,5-öt vagy 9.0 szorzása a teljes cDWU szükséges. Példa:<br></br>DW6000 x 7.5 = 45 000 egységnyi dtu-k<br></br>9.0 = 54 000 x DW6000c dtu-k.<br></br>Az aktuális DTU-felhasználást az SQL server lehetőség a portálon is megtekintheti. A DTU-kvótába a szüneteltetett és a nem szüneteltetett adatbázisok is beleszámítanak. |
| Adatbázis-kapcsolat |Nyissa meg egyidejű munkamenetek |1024<br/><br/>Minden egyes az 1024 aktív munkamenetek küldhet, hogy az SQL Data Warehouse-adatbázis kérelmeket egy időben. Vegye figyelembe, hogy a lekérdezések, amelyekkel végrehajtható egy időben száma korlátozva van. Az egyidejűségi korlát túllépésekor a kérelem hiányzóra egy belső várólista ahol vár feldolgozásra. |
| Adatbázis-kapcsolat |Maximális memória előkészített utasításokat |20 MB |
| [Számítási feladatok kezelése](resource-classes-for-workload-management.md) |Maximális párhuzamos lekérdezések |128<br/><br/> Az SQL Data Warehouse legfeljebb 128 lekérdezést és üzenetsorok fennmaradó lekérdezéseket hajthat végre.<br/><br/>A párhuzamos lekérdezések számát csökkentheti, amikor a felhasználók vannak hozzárendelve, magasabb erőforrásosztályokat, vagy ha az SQL Data Warehouse rendelkezik egy alacsonyabb [adattárházegység](memory-and-concurrency-limits.md) beállítás. Néhány lekérdezésnél, például a DMV-lekérdezés, mindig engedélyezett futtassa, és nincs hatással a párhuzamos lekérdezés-korlátot. A párhuzamos lekérdezés-végrehajtás további részletekért tekintse meg a [párhuzamosság maximális értékeket](memory-and-concurrency-limits.md#concurrency-maximums) cikk. |
| [a TempDB](sql-data-warehouse-tables-temporary.md) |Maximális GB |DW100, 399 Gigabájtonként. Ezért DWU1000, a tempdb van méretezve, hogy 3.99 TB. |

## <a name="database-objects"></a>Adatbázis-objektumok
| Kategória | Leírás | Maximum |
|:--- |:--- |:--- |
| Adatbázis |Maximális méret | Gen1: 240 TB tömöríti a lemezen. Ez a terület független terület a tempdb vagy a naplóhoz, és ezért ez a terület dedikált állandó táblák.  Fürtözött oszlopcentrikus tömörítés becsült 5 X.  Ez a fajta tömörítés lehetővé teszi, hogy az adatbázisnak, hogy megközelítőleg 1 PB, ha minden tábla fürtözött oszlopcentrikus (az alapértelmezett táblatípus). <br/><br/> Gen2: 240TB sortárindex és korlátlan tárolási oszlopcentrikus táblák |
| Tábla |Maximális méret |A lemezen tömörített 60 TB |
| Tábla |Táblák adatbázisonként |10,000 |
| Tábla |Táblánként oszlopok |1024 oszlopot |
| Tábla |Bájt / oszlop |Oszlop függ [adattípus](sql-data-warehouse-tables-data-types.md). A határ 8000-es karakteres adattípus, nvarchar a 4000-es vagy 2 GB maximális adattípusok. |
| Tábla |Minden egyes sorára, meghatározott méret bájt |8060 bájt<br/><br/>Minden egyes sorára bájtok száma azonos módon számítható, mert az SQL Server a lap tömörítéssel. Például az SQL Server az SQL Data Warehouse támogatja a sor-túlcsordulás tároló, amely lehetővé teszi, hogy **változó hosszúságú oszloppal** kell leküldeni, soron kívüli. Változó hosszúságú sorok leküld soron kívüli, csak a 24 bájtos legfelső szintű tárolja a fő rekord. További információkért lásd: [sor-túlcsordulás adatok meghaladja a 8 KB-os](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tábla |Táblánként partíciók |15 000<br/><br/>A jobb teljesítmény érdekében javasoljuk, hogy számának minimalizálása a partíciók meg kell miközben továbbra is támogatja az üzleti követelményeinek. A partíciók számának növekedésével adatok definíciós nyelv (DDL) és az adatok adatkezelési nyelvű (DML) műveletekhez a terhelés növekszik, és kisebb teljesítményt okoz. |
| Tábla |Az karakter / értéket partíció a határhoz. |4000 |
| Index |A nem fürtözött indexek táblánként. |50<br/><br/>Csak a táblák sortárindex vonatkozik. |
| Index |Fürtözött indexek táblánként. |1<br><br/>Sortárindex és az oszlopcentrikus táblák vonatkozik. |
| Index |Az index kulcsának mérete. |900 bájt.<br/><br/>Csak a sortárindex indexek vonatkozik.<br/><br/>Több mint 900 bájtos maximális mérettel varchar oszlopok indexei is létrehozható, ha a meglévő adatok az oszlopok nem haladja 900 bájtos az index létrehozásakor. Azonban később BESZÚRÁSA vagy az oszlopokat, amelyeket a teljes mérete meghaladja a 900 bájtos okozhat a frissítési műveletek sikertelenek lesznek. |
| Index |Indexenkénti kulcsoszlopot. |16<br/><br/>Csak a sortárindex indexek vonatkozik. Fürtözött oszlopcentrikus indexek összes oszlopát tartalmazza. |
| Statisztika |A kombinált oszlopértékek mérete. |900 bájt. |
| Statisztika |Oszlopok statisztikáit objektumonkénti. |32 |
| Statisztika |A statisztika táblánként oszlopokon létrehozni. |30,000 |
| Tárolt eljárások |Maximális beágyazási szintet. |8 |
| Nézet |Soronként megtekintése |1,024 |

## <a name="loads"></a>Betöltések
| Kategória | Leírás | Maximum |
|:--- |:--- |:--- |
| A Polybase-Betöltések |Minden egyes sorára MB |1<br/><br/>A Polybase csak az 1 MB-nál kisebb, és nem tölthető be, VARCHAR(MAX), NVARCHAR(MAX) vagy VARBINARY(MAX) sort tölt be.<br/><br/> |

## <a name="queries"></a>Lekérdezések
| Kategória | Leírás | Maximum |
|:--- |:--- |:--- |
| Lekérdezés |A felhasználói tábláknál aszinkron lekérdezések. |1000 |
| Lekérdezés |Rendszernézetek az egyidejű lekérdezéseket. |100 |
| Lekérdezés |Az aszinkron lekérdezések a rendszernézetek |1000 |
| Lekérdezés |Maximális paraméterek |2098 |
| Batch |Maximális méret |65,536*4096 |
| Válassza ki az eredményeket |Oszlopok minden egyes sorára vonatkozóan |4096<br/><br/>Minden egyes sorára vonatkozóan legfeljebb csak 4096 oszlopok a SELECT eredmény soha nem rendelkezhet. Nincs garancia arra, hogy 4096 mindig rendelkezhet. Ha a lekérdezésterv ideiglenes táblát igényel, az 1024-oszlopok maximális táblánként is vonatkozhatnak. |
| SELECT |Beágyazott segédlekérdezések |32<br/><br/>Soha nem rendelkezhet több mint 32 beágyazott segédlekérdezések olyan SELECT utasításban. Nincs garancia arra, hogy mindig is rendelkezik 32. Egy ÖSSZEKAPCSOLÁS például a lekérdezésterv is tegyünk segédlekérdezés. A rendelkezésre álló memória is is korlátozhatja a segédlekérdezések száma. |
| SELECT |Csatlakozás soronként |1024 oszlopot<br/><br/>A JOIN soha nem rendelkezhet több mint 1024 oszlopot. Nincs garancia arra, hogy 1024 mindig rendelkezhet. Ha az ILLESZTÉSI csomag több oszlop, mint az ILLESZTÉS eredmény tartalmazó ideiglenes táblát igényel, az ideiglenes tábla az 1024 korlát vonatkozik. |
| SELECT |Bájtok CSOPORTOSÍTÁSI oszlopok száma. |8060<br/><br/>A GROUP BY záradékban szereplő oszlopok legfeljebb egy 8060 bájt. |
| SELECT |Oszlopok az ORDER BY / bájt |8060 bájt<br/><br/>Az oszlopok az ORDER BY záradékában legfeljebb 8060 bájtok |
| Azonosítók utasítás kiszolgálónként |Hivatkozott azonosítók száma |65,535<br/><br/>Az SQL Data Warehouse a lekérdezés egyetlen kifejezés forráselérésiútja azonosítók számának korlátozása. Meghaladja a SQL Server-hiba 8632 számot eredményez. További információkért lásd: [belső hiba: elérte a korlátozását] [belső hiba: elérte a korlátozását]. |
| Karakterlánc-literálnak | Karakterlánc-literálnak utasításban száma | 20,000 <br/><br/>Az SQL Data Warehouse a lekérdezés egyetlen kifejezés karakterlánc-állandókat számát korlátozza. Meghaladja a SQL Server-hiba 8632 számot eredményez.|

## <a name="metadata"></a>Metaadatok
| Rendszernézet | Sorok maximális száma |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10,000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Teljes száma a legutóbbi 1000 DMS feldolgozók SQL kérelmeket. |
| sys.dm_pdw_errors |10,000 |
| sys.dm_pdw_exec_requests |10,000 |
| sys.dm_pdw_exec_sessions |10,000 |
| sys.dm_pdw_request_steps |A legutóbbi 1000 SQL-kérelmek sys.dm_pdw_exec_requests tárolt lépések száma összesen. |
| sys.dm_pdw_os_event_logs |10,000 |
| sys.dm_pdw_sql_requests |A legutóbbi 1000 SQL kéréseket, amelyek sys.dm_pdw_exec_requests vannak tárolva. |

## <a name="next-steps"></a>További lépések
Javaslatok az SQL Data Warehouse használatáról, tekintse meg a [Adatlap lap](cheat-sheet.md).
