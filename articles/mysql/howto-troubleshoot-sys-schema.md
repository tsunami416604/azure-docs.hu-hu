---
title: Hogyan sys_schema használata a teljesítmény finomhangolásához és adatbázis-karbantartás az Azure Database for MySQL-hez
description: Ez a cikk ismerteti, hogyan keresse meg a teljesítménybeli problémák és a MySQL-hez készült Azure Database-adatbázis karbantartása sys_schema használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/01/2018
ms.openlocfilehash: 993c77056c09c1dc21d5317ddbfe8e937341718d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542849"
---
# <a name="how-to-use-sysschema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Sys_schema használata a teljesítmény hangolása és adatbázis-karbantartás az Azure Database for MySQL-hez

A MySQL performance_schema, az első elérhető MySQL 5.5-ös biztosít instrumentation számos kulcsfontosságú server források, például a memória mennyiségét, a tárolt programok, a metaadatok zárolási, stb. Azonban a performance_schema több mint 80 táblát tartalmaz, és gyakran a szükséges információk lekérése igényel, a performance_schema táblák, valamint a information_schema táblák való csatlakozás. Épület performance_schema és information_schema is, a sys_schema gyűjteményét biztosítja a hatékony [felhasználóbarát nézetek](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) egy csak olvasható adatbázisban és teljes mértékben engedélyezve van az Azure Database for MySQL 5.7-es verzióra.

![sys_schema nézetei](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Nincsenek a sys_schema 52 nézeteket, és mindegyik nézetről rendelkezik-e az alábbi előtagokat:

- Host_summary vagy IO: I/o kapcsolódó késéseket.
- InnoDB: InnoDB puffer állapota és a zárolás.
- Memória: Memóriahasználat a gazdagépen és a felhasználók által.
- Séma: Séma-kapcsolatos információkat, például automatikusan növekvő, indexek, stb.
- Utasítás: Információk az SQL-utasítások; a teljes tábla beolvasásával vagy hosszú lekérdezés idő eredményező utasítás lehet.
- Felhasználó: Felhasznált és felhasználók szerint csoportosított erőforrások. Példák fájl i/o-, kapcsolatok és memória.
- várj: Várjon, amíg események gazdagép vagy a felhasználó szerint csoportosítva.

Most nézzük meg, a sys_schema gyakori használati minták. Elsőként azt kell csoport a a használati minták két kategóriába sorolhatók: **Teljesítményhangolás** és **adatbázis-karbantartási**.

## <a name="performance-tuning"></a>Teljesítmény-finomhangolás

### <a name="sysusersummarybyfileio"></a>*sys.user_summary_by_file_io*

I/o az a leginkább drága művelet az adatbázisban. Azt is megtudhatja, az átlagos i/o várakozási lekérdezésével a *sys.user_summary_by_file_io* megtekintése. Az alapértelmezett 125 GB-os kiépített tárhely, az IO-késés van körülbelül 15 másodperc.

![IO-késés: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Mivel, Azure Database for MySQL megállapodást tárolás, i/o növelje a felhasznált tárterület 1 TB-os skálázható, saját IO-késés csökkenti a 571 ms.

![IO-késés: 1 TB-OT](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschematableswithfulltablescans"></a>*sys.schema_tables_with_full_table_scans*

Annak ellenére, hogy gondos tervezést, több lekérdezés továbbra is teljes táblázatbeolvasás eredményezhet. További információt a típusú indexeket, és hogyan optimalizálja őket ez a cikk hivatkozhatunk: [A lekérdezési teljesítmény hibaelhárítása](./howto-troubleshoot-query-performance.md). Szekvenciális rekordolvasással teljes erőforrás-igényes, és az adatbázis teljesítményét. Keresse meg a teljes tábla beolvasásával rendelkező táblák a leggyorsabb módja az, hogy lekérdezést a *sys.schema_tables_with_full_table_scans* megtekintése.

![teljes táblázatbeolvasás](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysusersummarybystatementtype"></a>*sys.user_summary_by_statement_type*

Adatbázis teljesítménnyel kapcsolatos problémák elhárításához lehet előnyös, ha az eseményeket, az adatbázis belül történik, és használja a *sys.user_summary_by_statement_type* nézet csak hajtsa végre a kört.

![utasítás összegzése](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

Ebben a példában, Azure Database for MySQL töltött kiürítette a slog lekérdezési napló 44579 alkalommal 53 perc. Ez egy hosszú ideig, és számos IOs. Vagy tiltsa le ezt a tevékenységet csökkentheti a lassú lekérdezések naplója, vagy csökkentse a gyakoriságát, a lassú lekérdezések bejelentkezés az Azure Portalon.

## <a name="database-maintenance"></a>Adatbázis-karbantartás

### <a name="sysinnodbbufferstatsbytable"></a>*sys.innodb_buffer_stats_by_table*

Az InnoDB pufferkészletben memóriájában, és az adatbázis-kezelő és a storage között a fő cache mechanizmus. Az InnoDB pufferkészlet méretét vannak kötve, a teljesítményszint, és nem módosítható, kivéve, ha egy másik termékkel SKU van kiválasztva. Csakúgy, mint az operációs rendszer memória, a régi oldalak való helybiztosítás céljából naprakészebb adatok vannak lapozó. Hogy megtudja, melyik táblákhoz felhasználása az InnoDB puffermemória készlet a legtöbb, lekérdezheti a *sys.innodb_buffer_stats_by_table* megtekintése.

![InnoDB puffer állapota](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

A fenti ábrán az látható, hogy eltérő rendszer táblák és nézetek, a mysqldatabase033 adatbázis minden táblájához, amely saját WordPress-webhelyek egyikét futtatja, 16 KB-os vagy 1 lapon, a memória adatok által elfoglalt.

### <a name="sysschemaunusedindexes--sysschemaredundantindexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Indexek nagyszerű eszközöket olvasási teljesítmény javítása érdekében, de ezek további költségekkel beszúrások és tárolására. *Sys.schema_unused_indexes* és *sys.schema_redundant_indexes* használaton kívüli vagy ismétlődő indexeket betekintést nyújtson.

![a fel nem használt index](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![redundáns indexek](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Összegzés

Összefoglalva az sys_schema mindkét teljesítmény-finomhangolási és adatbázis-karbantartás kiváló eszközei. Ellenőrizze, hogy igénybe ezt a szolgáltatást az az Azure Database for MySQL-hez. 

## <a name="next-steps"></a>További lépések
- A legtöbb érintett kérdésekre adott válaszok társ, vagy egy új kérdés-válasz küldése, látogasson el [MSDN-fórum](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) vagy [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
