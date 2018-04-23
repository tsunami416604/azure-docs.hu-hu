---
title: Hogyan használja sys_schema teljesítményhangolás és MySQL az Azure-adatbázis adatbázis-karbantartás
description: Ez a cikk ismerteti, hogyan sys_schema segítségével található teljesítményproblémákat és MySQL az Azure-adatbázis adatbázis karbantartása.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 2e5b6b859df06d686a97fc1b134da8d66df6783e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-use-sysschema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>A MySQL használatáról sys_schema teljesítményének hangolása és adatbázis-karbantartás Azure-adatbázis

A MySQL performance_schema, az első elérhető MySQL 5.5 biztosít instrumentation a számos fontos server erőforrások, például a memóriafoglalás, a tárolt programok, a metaadatok zárolási, stb. Azonban a performance_schema több mint 80 táblát tartalmaz, és gyakran a szükséges információ igényel, a performance_schema belül táblák, valamint a entitástulajdonos táblák való csatlakozás. Felépítése performance_schema és entitástulajdonos, a sys_schema egy hatékony gyűjteményét biztosítja [felhasználóbarát nézetek](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) egy csak olvasható adatbázisban és teljes mértékben engedélyezve van az Azure-adatbázisban a MySQL verzió 5.7-es verzióját.

![a sys_schema nézetei](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

A sys_schema 52 nézetek szerepelnek, és mindegyik nézetről rendelkezik-e az alábbi előtagokat:

- Host_summary vagy IO: i/o-kapcsolódó késések fordulnak elő.
- InnoDB: InnoDB puffer állapota és zárolások.
- Felhasznált memória: A gazdagép és a felhasználók által memória mennyisége
- Séma: Séma-kapcsolatos információkat, például automatikus lépések, indexek, stb.
- Utasítást: SQL-utasítások; információk lehet, hogy a tábla teljes vizsgálatot, vagy hosszú lekérdezési idő eredményező utasítás.
- Felhasználó: Erőforrások fel, és felhasználók szerint csoportosított. Többek között az i/o, kapcsolatok, és a memória.
- Várjon, amíg: Várja meg a gazdagép vagy a felhasználó csoportosíthatók az események.

Most már a sys_schema néhány gyakori használati szokásokról vizsgáljuk meg. Először azt kell csoportosítani a használati minták két kategóriába sorolhatók: **teljesítményhangolás** és **adatbázis-karbantartási**.

## <a name="performance-tuning"></a>Teljesítmény-finomhangolás

### <a name="sysusersummarybyfileio"></a>*sys.user_summary_by_file_io*

IO az a legköltségesebb művelet az adatbázisban. A Microsoft talál az átlagos IO várakozási lekérdezésével a *sys.user_summary_by_file_io* nézet. Az alapértelmezett kiosztott tárolás, az I/O várakozási ideje 125 GB legyen körülbelül 15 másodperc.

![i/o várakozási ideje: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

MySQL az Azure-adatbázis IO méretezi tekintetében tárolási, miután növelte a kiépített tárolási és 1 TB, mert a I/O várakozási ideje csökkenti a 26 X teljesítmény növelését képviselő 571 MS!

![i/o várakozási ideje: 1 TB-os](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschematableswithfulltablescans"></a>*sys.schema_tables_with_full_table_scans*

Annak ellenére, hogy gondosan meg kell tervezni, sok lekérdezések továbbra is teljes táblázatbeolvasás eredményez. További információt az indexek és optimalizálása azokat, olvassa el a cikkben: [hibaelhárítása a lekérdezési teljesítmény](./howto-troubleshoot-query-performance.md). Teljes táblázatbeolvasás erőforrás-igényes, és az adatbázis teljesítményét. A leggyorsabb módja teljes táblázatbeolvasás táblákban található lekérdezés a *sys.schema_tables_with_full_table_scans* nézet.

![teljes rekordból](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysusersummarybystatementtype"></a>*sys.user_summary_by_statement_type*

Adatbázis-teljesítménnyel kapcsolatos problémák elhárításához az eseményeket az adatbázis belül történik, és ezáltal hasznos lehet a *sys.user_summary_by_statement_type* nézet csak hajtsa végre az körben.

![összefoglalás utasítás alapján](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

Ebben a példában a MySQL az Azure-adatbázis töltött a slog lekérdezés napló kiürítése 44579 alkalommal 53 perc. Ez sok időt és sok IOs. A lassú lekérdezés napló csökkentheti a tevékenység által tiltsa vagy lassú lekérdezés bejelentkezési Azure-portálon gyakoriságának csökkentése.

## <a name="database-maintenance"></a>Adatbázis-karbantartás

### <a name="sysinnodbbufferstatsbytable"></a>*sys.innodb_buffer_stats_by_table*

A InnoDB pufferkészlet memória fájlcsoportban helyezkedik el, és a fő gyorsítótár mechanizmus az adatbázis-kezelő és a tároló között. A méret a InnoDB pufferkészlet teljesítményszint van kötve, és nem módosítható, kivéve, ha egy másik termékkel SKU van kiválasztva. Az operációs rendszer memória, a régi lapok van cserélve hogy helyet biztosítsanak az naprakészebb adatok. Tudni táblák felhasználását a InnoDB puffer memóriakészletet többségét, lekérheti a *sys.innodb_buffer_stats_by_table* nézet.

![InnoDB puffer állapota](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

A fenti ábrán is látható, hogy eltérő rendszer táblák és nézetek, minden táblában a mysqldatabase033 adatbázis, amely saját WordPress-webhelyek egyikét futtatja, 16 KB-os vagy 1 lapon, a memória adatok által elfoglalt.

### <a name="sysschemaunusedindexes--sysschemaredundantindexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Indexek kiváló eszközök olvasási teljesítmény javítása érdekében, de azok költségnövekedéssel beszúrások és tárolására. *Sys.schema_unused_indexes* és *sys.schema_redundant_indexes* nem használt vagy ismétlődő indexek betekintést.

![nem használt indexek](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![redundáns indexek](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Összegzés

Összefoglalva a sys_schema meg mindkét teljesítményének hangolása és adatbázis-karbantartás. Ügyeljen arra, hogy az Azure-adatbázis a MySQL esetében ez a funkció előnyeit. 

## <a name="next-steps"></a>További lépések
- A legtöbb érintett kérdésekre adott válaszok társ, vagy egy új kérdés-válasz utáni, látogasson el a [MSDN fórum](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) vagy [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
