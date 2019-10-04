---
title: Kapacitási korlátok – Azure SQL Data Warehouse | Microsoft Docs
description: A Azure SQL Data Warehouse különböző összetevői számára engedélyezett maximális értékek.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/14/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 4443f94df9095da3a7ec0e9694b8089033c8d177
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686440"
---
# <a name="sql-data-warehouse-capacity-limits"></a>SQL Data Warehouse kapacitás korlátai
A Azure SQL Data Warehouse különböző összetevői számára engedélyezett maximális értékek.

## <a name="workload-management"></a>Számítási feladatok kezelése
| Category | Leírás | Maximum |
|:--- |:--- |:--- |
| [Adatraktár-egységek (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Egyetlen SQL Data Warehouse maximális DWU | Gen1: DW6000<br></br>Gen2 DW30000c |
| [Adatraktár-egységek (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Alapértelmezett DTU kiszolgálónkénti |54,000<br></br>Alapértelmezés szerint minden SQL-kiszolgáló (például myserver.database.windows.net) 54 000-as DTU-kvótával rendelkezik, amely legfeljebb 9 DW6000c-t tesz lehetővé. Ez a kvóta egyszerűen egy biztonsági korlát. A kvótát megnövelheti [egy támogatási jegy létrehozásával](sql-data-warehouse-get-started-create-support-ticket.md) , és a kérelem típusaként kiválaszthatja a *kvótát* .  A DTU-szükségletek kiszámításához szorozza meg a 7,5-et a szükséges teljes DWU, vagy szorozza meg a 9,0-et a szükséges teljes cDWU. Példa:<br></br>DW6000 x 7,5 = 45 000 DTU<br></br>DW6000c x 9,0 = 54 000 DTU.<br></br>Az aktuális DTU-felhasználást a portálon, az SQL Server lehetőségnél tekintheti meg. A DTU-kvótába a szüneteltetett és a nem szüneteltetett adatbázisok is beleszámítanak. |
| Adatbázis-kapcsolat |Egyidejű nyitott munkamenetek maximális száma |1024<br/><br/>Az egyidejű nyitott munkamenetek száma a kiválasztott DWU függően változhat. A DWU600c és újabb verziók legfeljebb 1024 nyitott munkamenetet támogatnak. A DWU500c és az alatta az egyidejű nyitott munkamenetek maximális 512-os korlátját támogatja. Vegye figyelembe, hogy az egyidejűleg végrehajtható lekérdezések száma korlátozott. Ha túllépi a párhuzamossági korlátot, a kérelem egy belső várólistába kerül, ahol a rendszer feldolgozza azt. |
| Adatbázis-kapcsolat |Előkészített utasítások maximális memóriája |20 MB |
| [Számítási feladatok kezelése](resource-classes-for-workload-management.md) |Egyidejű lekérdezések maximális száma |128<br/><br/> SQL Data Warehouse legfeljebb 128 egyidejű lekérdezést és várólistán lévő lekérdezéseket futtathat.<br/><br/>Az egyidejű lekérdezések száma csökkenhet, ha a felhasználók magasabb erőforrás-osztályokhoz vannak rendelve, vagy ha a SQL Data Warehouse alacsonyabb [adatraktár-egységgel](memory-and-concurrency-limits.md) rendelkezik. Egyes lekérdezések, például a DMV-lekérdezések mindig futhatnak, és nem befolyásolják az egyidejű lekérdezési korlátot. Az egyidejű lekérdezés-végrehajtással kapcsolatos további részletekért tekintse meg a [párhuzamosságok maximális](memory-and-concurrency-limits.md#concurrency-maximums) száma című cikket. |
| [tempdb](sql-data-warehouse-tables-temporary.md) |Maximális GB |399 GB/DW100. Ezért a DWU1000 tempdb mérete 3,99 TB. |

## <a name="database-objects"></a>Adatbázis-objektumok
| Category | Leírás | Maximum |
|:--- |:--- |:--- |
| Adatbázis |Maximális méret | Gen1: 240 TB tömörített lemezen. Ez a terület független a tempdb és a naplózási területtől, ezért ez a terület állandó táblákhoz van hozzárendelve.  A fürtözött oszlopcentrikus-tömörítés becsült értéke 5X.  Ez a tömörítés lehetővé teszi, hogy az adatbázis körülbelül 1 PB-re növelje, ha az összes tábla fürtözött oszlopcentrikus (az alapértelmezett tábla típusa). <br/><br/> Gen2 240TB sortárindex létrehozását és korlátlan számú oszlopcentrikus-táblához |
| Tábla |Maximális méret |60 TB tömörített lemezen |
| Tábla |Táblák/adatbázis | 100,000 |
| Tábla |Oszlopok száma táblában |1024 oszlop |
| Tábla |Bájt/oszlop |Az oszlop [adattípusának](sql-data-warehouse-tables-data-types.md)függvénye. A határérték 8000 karakteres adattípusok esetén, 4000 nvarchar, vagy 2 GB a maximális adattípusokhoz. |
| Tábla |Bájt/sor, meghatározott méret |8060 bájt<br/><br/>A másodpercenkénti bájtok számát ugyanúgy számítjuk ki, mint az oldal tömörítéséhez SQL Server. A SQL Serverhoz hasonlóan a SQL Data Warehouse támogatja a soros túlcsordulási tárolót, amely lehetővé teszi a **változó hosszúságú oszlopok** leküldését a sorból. Ha a változó hosszúságú sorok kiküldése sorban történik, a fő rekordban csak 24 bájtos gyökér tárolódik. További információ: [8 kb-ot meghaladó, soros túlcsordulási adatok](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tábla |Partíciók száma táblában |15,000<br/><br/>A nagy teljesítmény érdekében javasoljuk, hogy minimalizálja a szükséges partíciók számát, miközben továbbra is támogatja az üzleti igényeit. Ahogy nő a partíciók száma, az adatdefiníciós nyelv (DDL) és az adatmanipulációs nyelv (DML) műveleteinek terhelése növekszik, és lassabb teljesítményt eredményez. |
| Tábla |Karakter/partíciós határ értéke. |4000 |
| Index |Nem fürtözött indexek száma táblábanként. |50<br/><br/>Csak a sortárindex létrehozását táblákra vonatkozik. |
| Index |Fürtözött indexek száma táblábanként. |1<br><br/>A sortárindex létrehozását és a oszlopcentrikus táblákra is érvényes. |
| Index |Index kulcsának mérete |900 bájt.<br/><br/>Csak a sortárindex létrehozását indexekre vonatkozik.<br/><br/>Az 900 bájtnál nagyobb maximális mérettel rendelkező varchar-oszlopok indexei akkor hozhatók létre, ha az oszlopok meglévő adata nem haladja meg a 900 bájtot az index létrehozásakor. Ha azonban később olyan műveleteket végez az oszlopokon, amelyek az 900 bájtnál nagyobb teljes méretet okoznak, a művelet sikertelen lesz. |
| Index |Kulcs oszlopai/index. |16<br/><br/>Csak a sortárindex létrehozását indexekre vonatkozik. A fürtözött oszlopcentrikus indexek tartalmazzák az összes oszlopot. |
| Statisztika |A kombinált oszlopok értékeinek mérete |900 bájt. |
| Statisztika |Oszlop/statisztika objektum. |32 |
| Statisztika |Az oszlopokon létrehozott statisztikák táblázat szerint lettek létrehozva. |30,000 |
| Tárolt eljárások |Maximális beágyazási szintek. |8 |
| Nézet |Oszlopok száma nézetben |1,024 |

## <a name="loads"></a>Betölti
| Category | Leírás | Maximum |
|:--- |:--- |:--- |
| Alapszintű terhelések |MB/sor |1<br/><br/>A kiinduló nem 1 MB-nál kisebb sorokat tölt be. Az LOB-adattípusok fürtözött Oszlopcentrikus Indextel (CCI) rendelkező táblákba való betöltése nem támogatott.<br/><br/> |

## <a name="queries"></a>Lekérdezések
| Category | Leírás | Maximum |
|:--- |:--- |:--- |
| Lekérdezés |Várólistán lévő lekérdezések a felhasználói táblákon. |1000 |
| Lekérdezés |Egyidejű lekérdezések a rendszernézeteken. |100 |
| Lekérdezés |Várólistán lévő lekérdezések rendszernézeteken |1000 |
| Lekérdezés |Maximális paraméterek |2098 |
| Batch |Maximális méret |65,536*4096 |
| Eredmények kiválasztása |Oszlop/sor |4096<br/><br/>A SELECT eredményben nem lehet több, mint 4096 oszlop/sor. Nincs garancia arra, hogy mindig 4096. Ha a lekérdezési terv egy ideiglenes táblát igényel, akkor a 1024 oszlopok száma legfeljebb Table lehet. |
| SELECT |Beágyazott allekérdezések |32<br/><br/>Egy SELECT utasításban még soha nem lehet több mint 32 beágyazott segédlekérdezés. Nincs garancia arra, hogy mindig 32. Egy illesztés például bevezethet egy allekérdezést a lekérdezési tervbe. Az allekérdezések számát a rendelkezésre álló memória is korlátozhatja. |
| SELECT |Oszlop/illesztés |1024 oszlop<br/><br/>Az ILLESZTÉSben soha nem lehet több, mint 1024 oszlop. Nincs garancia arra, hogy mindig 1024. Ha a JOIN csomaghoz egy ideiglenes tábla szükséges, amelynek több oszlopa van, mint az ILLESZTÉSi eredmény, a 1024 korlát az ideiglenes táblára vonatkozik. |
| SELECT |Bájtok csoportonként oszloponként. |8060<br/><br/>A GROUP BY záradék oszlopainak száma legfeljebb 8060 bájt lehet. |
| SELECT |Bájt/rendezés oszlopok szerint |8060 bájt<br/><br/>Az ORDER BY záradék oszlopai legfeljebb 8060 bájtot tartalmazhatnak |
| Azonosítók/utasítások |Hivatkozott azonosítók száma |65,535<br/><br/>SQL Data Warehouse korlátozza a lekérdezés egyetlen kifejezésében tárolható azonosítók számát. Ennek a számnak a túllépése SQL Server 8632-es hibát eredményez. További információ: [Internal hiba: Egy Expression Services-korlát elérte a @ no__t-0 értéket. |
| Karakterlánc-literálok | Karakterlánc-literálok száma egy utasításban | 20,000 <br/><br/>SQL Data Warehouse korlátozza a karakterlánc-konstansok számát egy lekérdezés egyetlen kifejezésében. Ennek a számnak a túllépése SQL Server 8632-es hibát eredményez.|

## <a name="metadata"></a>Metaadatok
| Rendszernézet | Sorok maximális száma |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10,000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |A legutóbbi 1000 SQL-kérelmekhez tartozó DMS-feldolgozók száma összesen |
| sys.dm_pdw_errors |10,000 |
| sys.dm_pdw_exec_requests |10,000 |
| sys.dm_pdw_exec_sessions |10,000 |
| sys.dm_pdw_request_steps |A sys. DM _pdw_exec_requests tárolt legutóbbi 1000 SQL-kérelmekre vonatkozó lépések teljes száma. |
| sys.dm_pdw_os_event_logs |10,000 |
| sys.dm_pdw_sql_requests |A sys. DM _pdw_exec_requests tárolt legutóbbi 1000 SQL-kérelmek. |

## <a name="next-steps"></a>További lépések
A SQL Data Warehouse használatára vonatkozó javaslatokért tekintse meg a [Cheat Sheet](cheat-sheet.md)című témakört.
