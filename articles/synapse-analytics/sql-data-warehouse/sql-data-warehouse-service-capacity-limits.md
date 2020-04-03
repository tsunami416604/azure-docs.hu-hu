---
title: Kapacitáskorlátok – Azure Synapse Analytics (korábban SQL DW)
description: Az Azure Synapse-ban a Synapse SQL-készlet különböző összetevőihez engedélyezett maximális értékek.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: f35a5da15ca1a672046844282626a6cb7b8ecbdf
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583533"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-capacity-limits"></a>Az Azure Synapse Analytics (korábban SQL DW) kapacitáskorlátai

Az Azure Synapse különböző összetevőihez engedélyezett maximális értékek.

## <a name="workload-management"></a>Számítási feladatok kezelése

| Kategória | Leírás | Maximum |
|:--- |:--- |:--- |
| [Adattárház-egységek (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Maximális DWU egyetlen SQL-készlet (adattárház) egységhez | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Adattárház-egységek (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Alapértelmezett DTU kiszolgálónként |54,000<br></br>Alapértelmezés szerint minden SQL-kiszolgáló (például myserver.database.windows.net) 54 000 DTU-kvótával rendelkezik, amely legfeljebb DW5000c értéket tesz lehetővé. Ez a kvóta egyszerűen egy biztonsági korlát. Növelheti a kvótát egy [támogatási jegy létrehozásával,](sql-data-warehouse-get-started-create-support-ticket.md) és a *kvóta* kérelemtípusként kiválasztásával.  A DTU-igények kiszámításához szorozza meg a 7,5-öt a szükséges teljes DWU-val, vagy szorozza meg a 9,5-öt a szükséges teljes cDWU-val. Példa:<br></br>DW6000 x 7,5 = 45 000 DSZ<br></br>DW5000c x 9,5 = 47 500 DTUs.<br></br>Megtekintheti az aktuális DTU-felhasználás az SQL-kiszolgáló beállítás a portálon. A DTU-kvótába a szüneteltetett és a nem szüneteltetett adatbázisok is beleszámítanak. |
| Adatbázis-kapcsolat |Egyidejű nyílt munkamenetek maximális száma |1024<br/><br/>Az egyidejű nyitott munkamenetek száma a kiválasztott DWU-tól függően változik. A DWU600c és a felettlévők legfeljebb 1024 nyílt munkamenetet támogatnak. DWU500c és az alatt, támogatja a maximális egyidejű nyílt munkamenet határ 512. Megjegyzés: az egyidejűleg végrehajtható lekérdezések száma korlátozott. Az egyidejűségi korlát túllépése esetén a kérelem egy belső várólistába kerül, ahol a feldolgozásra vár. |
| Adatbázis-kapcsolat |Az előkészített utasítások maximális memóriája |20 MB |
| [Számítási feladatok kezelése](resource-classes-for-workload-management.md) |Egyidejű lekérdezések maximális |128<br/><br/>  Legfeljebb 128 egyidejű lekérdezés t fut le, és a fennmaradó lekérdezések várólistára kerülnek.<br/><br/>Az egyidejű lekérdezések száma csökkenhet, ha a felhasználók magasabb erőforrásosztályokhoz vannak rendelve, vagy ha az [adatraktáregység](memory-concurrency-limits.md) beállítása csökken. Egyes lekérdezések, például a DMV-lekérdezések, mindig futtathatóak, és nem befolyásolják az egyidejű lekérdezési korlátot. Az egyidejű lekérdezések végrehajtásával kapcsolatos további részletekért tekintse meg az [egyidejűségi maximumok](memory-concurrency-limits.md) című cikket. |
| [tempdb között](sql-data-warehouse-tables-temporary.md) |Maximális GB |399 GB/DW100c. Ezért a DWU1000c, tempdb mérete 3,99 TB. |
||||

## <a name="database-objects"></a>Adatbázis-objektumok

| Kategória | Leírás | Maximum |
|:--- |:--- |:--- |
| Adatbázis |Maximális méret | Gen1: 240 TB tömörített lemezen. Ez a terület független a tempdb-től vagy a naplóterülettől, ezért ez a hely állandó tábláknak van szentelve.  A fürtözött oszlopcentrikus tömörítés becsült értéke 5X.  Ez a tömörítés lehetővé teszi, hogy az adatbázis körülbelül 1 PB-re nőjön, ha az összes tábla fürtözött oszlopcentrikus (az alapértelmezett táblatípus). <br/><br/> Gen2: Korlátlan tárterület oszlopcentrikus táblák.  Az adatbázis sorcentrikus része még mindig 240 TB-ra van korlátozva a lemezen tömörítve. |
| Tábla |Maximális méret |Korlátlan méret az oszlopcentrikus táblákhoz. <br>60 TB a lemezre tömörített sortárolótáblák esetén. |
| Tábla |Táblák adatbázisonként | 100 000 |
| Tábla |Oszlopok táblázatonként |1024 oszlop |
| Tábla |Oszloponkénti bájtok |Az oszlop [adattípusától](sql-data-warehouse-tables-data-types.md)függ . Karakteradattípusok esetén a MAX Limit legfeljebb 2 GB-ot tárolhat az oldalon kívüli (sortúlcsordulás) tárolóban.  A unicode-ot nem formátumú karakterek, például a char vagy a varchar korlát 8000 az adatlapon, a Unicode karakterek (például nchar vagy nvarchar) 4000 az adatlapon.  A teljesítmény növelése érdekében használja az adatlap-tárolóméreteket. |
| Tábla |Soronkénti bájt, meghatározott méret |8060 bájt<br/><br/>A soronkénti bájtok számát a program ugyanúgy számítja ki, mint az SQL Server esetében az oldaltömörítéssel. Az SQL Server hez hasonlóan a sortúlcsordulásos tároló is támogatott, ami lehetővé teszi a **változó hosszúságú oszlopok** sorral történő lelökését. Ha a változó hosszúságú sorokat a soron kívülre tolják, a fő rekordban csak 24 bájtos gyökér tárolódik. További információ: [8 KB-ot meghaladó sortúlcsordulási adatok.](https://msdn.microsoft.com/library/ms186981.aspx) |
| Tábla |Partíciók táblaként |15 000<br/><br/>A nagy teljesítmény érdekében azt javasoljuk, hogy minimalizálja a szükséges partíciók számát, miközben továbbra is támogatja az üzleti követelményeket. A partíciók számának növekedésével az adatdefiníciós nyelv (DDL) és az adatkezelési nyelv (DML) műveletek többletterhelése nő, és lassabb teljesítményt eredményez. |
| Tábla |Karakterek partícióhatárértékenként. |4000 |
| Index |Nem fürtözött indexek táblánként. |50<br/><br/>Csak sortároló táblákra vonatkozik. |
| Index |Fürtözött indexek táblánként. |1<br><br/>A sortár és az oszlopcentrikus táblákra egyaránt vonatkozik. |
| Index |Indexkulcs mérete. |900 bájt.<br/><br/>Csak a sorcentrikus indexek vonatkozik.<br/><br/>A 900 bájtnál nagyobb maximális méretű varchar oszlopok indexei akkor hozhatók létre, ha az oszlopokban lévő adatok nem haladják meg a 900 bájtot az index létrehozásakor. A 900 bájtot meghaladó teljes méret-túllépést okozó oszlopok beszúrása vagy frissítése művelete azonban sikertelen lesz. |
| Index |Kulcsoszlopok indexenként. |16<br/><br/>Csak a sorcentrikus indexek vonatkozik. A fürtözött oszlopcentrikus indexek tartalmazzák az összes oszlopot. |
| Statisztika |A kombinált oszlopértékek mérete. |900 bájt. |
| Statisztika |Oszlopok statisztikai objektumonként. |32 |
| Statisztika |Oszlopokonként létrehozott statisztikák. |30,000 |
| Tárolt eljárások |A beágyazás maximális szintje. |8 |
| Nézet |Oszlopok nézetenként |1,024 |
||||

## <a name="loads"></a>Terhel

| Kategória | Leírás | Maximum |
|:--- |:--- |:--- |
| Polibázis terhelések |MB soronként |1<br/><br/>A Polybase 1 MB-nál kisebb sorokat tölt be. A LOB-adattípusok fürtözött oszlopcentrikus indexszel (CCI) rendelkező táblákba való betöltése nem támogatott.<br/><br/> |
||||

## <a name="queries"></a>Lekérdezések

| Kategória | Leírás | Maximum |
|:--- |:--- |:--- |
| Lekérdezés |Várólistára helyezett lekérdezések a felhasználói táblákon. |1000 |
| Lekérdezés |Egyidejű lekérdezések a rendszernézetekre. |100 |
| Lekérdezés |Várakozással várakozó lekérdezések rendszernézetekben |1000 |
| Lekérdezés |Maximális paraméterek |2098 |
| Batch |Maximális méret |65,536*4096 |
| SELECT eredmények |Oszlopok soronként |4096<br/><br/>A SELECT eredményben soha nem lehet soronként 4096-nál több oszlop. Nincs garancia arra, hogy mindig 4096 lehet. Ha a lekérdezési tervhez ideiglenes tábla szükséges, a táblankénti 1024 oszlop maximum érvényes lehet. |
| SELECT |Beágyazott allekérdezések |32<br/><br/>Egy SELECT utasításban soha nem lehet 32-nél több beágyazott allekérdezés. Nincs garancia arra, hogy mindig 32 lehet. A JOIN például segédlekérdezést vezethet be a lekérdezési tervbe. A rendelkezésre álló memória korlátozza az allekérdezések számát is. |
| SELECT |Oszlopok joinonként |1024 oszlop<br/><br/>A JOIN-ban legfeljebb 1024 oszlop lehet. Nincs garancia arra, hogy mindig 1024 lehet. Ha a JOIN tervhez a JOIN eredménynél több oszlopot tartalmazó ideiglenes tábla szükséges, az 1024-es korlát az ideiglenes táblára vonatkozik. |
| SELECT |CSOPORT SZERINT OSZLOPONkénti bájt. |8060<br/><br/>A GROUP BY záradék oszlopai legfeljebb 8060 bájtból rendelkezhetnek. |
| SELECT |Bájt rendelésenként oszlopokonként |8060 bájt<br/><br/>Az ORDER BY záradék oszlopai legfeljebb 8060 bájtból állhatnak. |
| Azonosítók kivonatonként |Hivatkozott azonosítók száma |65,535<br/><br/> A lekérdezés egyetlen kifejezésében szereplő azonosítók száma korlátozott. Ennek a számnak a túllépése az SQL Server 8632-es hibáját eredményezi. További információ: [Belső hiba: Elérte a kifejezésszolgáltatások korlátját.](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a) |
| Karakterlánc-konstansok | Karakterlánc-konstansok száma egy utasításban | 20000 <br/><br/>A lekérdezés egyetlen kifejezésében lévő karakterlánc-állandók száma korlátozott. Ennek a számnak a túllépése az SQL Server 8632-es hibáját eredményezi.|
||||

## <a name="metadata"></a>Metaadatok

| Rendszernézet | Sorok maximális |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10,000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |A legutóbbi 1000 SQL-kérelemhez a DMS-dolgozók száma összesen. |
| sys.dm_pdw_errors |10,000 |
| sys.dm_pdw_exec_requests |10,000 |
| sys.dm_pdw_exec_sessions |10,000 |
| sys.dm_pdw_request_steps |A sys.dm_pdw_exec_requests fájlban tárolt legutóbbi 1000 SQL-kérelem lépéseinek teljes száma. |
| sys.dm_pdw_os_event_logs |10,000 |
| sys.dm_pdw_sql_requests |A legutóbbi 1000 SQL-kérelmek, amelyek a sys.dm_pdw_exec_requests tárolja. |
|||

## <a name="next-steps"></a>További lépések

Az Azure Synapse használatával kapcsolatos javaslatokért tekintse meg a Cheat Sheet című [témakört.](cheat-sheet.md)
