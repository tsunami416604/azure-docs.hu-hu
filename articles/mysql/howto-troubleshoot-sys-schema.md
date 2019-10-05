---
title: A sys_schema használata a teljesítmény finomhangolásához és a Azure Database for MySQL karbantartásához
description: Megtudhatja, hogyan használhatja a sys_schema a teljesítménnyel kapcsolatos problémák megkereséséhez és az adatbázis karbantartásához Azure Database for MySQLban.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: 7dc6b4744c74c56803127f63a8a6f29ca5a15090
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972794"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>A sys_schema használata a teljesítmény finomhangolásához és az adatbázis-karbantartáshoz Azure Database for MySQL

A MySQL 5,5-ben elsőként elérhető MySQL-performance_schema számos létfontosságú kiszolgálói erőforrást biztosít, mint például a memória kiosztása, a tárolt programok, a metaadatok zárolása stb. A performance_schema azonban több mint 80 táblát tartalmaz, és a szükséges információk beszerzéséhez gyakran szükség van a performance_schema belüli táblák, valamint a entitástulajdonos tábláihoz való csatlakozásra. A sys_schema a performance_schema és a entitástulajdonos egyaránt lehetővé teszi, hogy a [felhasználó-barát nézetek](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) hatékony gyűjteménye legyen egy írásvédett adatbázisban, és teljes mértékben engedélyezve legyen a Azure Database for MySQL 5,7-es verziójában.

![a sys_schema nézetei](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

A sys_schema 52 nézet található, és az egyes nézetek az alábbi előtagok egyikével rendelkeznek:

- Host_summary vagy IO: I/O kapcsolatos késések.
- InnoDB InnoDB-puffer állapota és zárolásai.
- Memória: A gazdagép és a felhasználók általi memóriahasználat.
- Séma Sémával kapcsolatos információk, például automatikus növekmény, indexek stb.
- Nyilatkozat Információk az SQL-utasításokról; olyan utasítás lehet, amely teljes táblázatos vizsgálatot vagy hosszú lekérdezési időt eredményezett.
- Felhasználói A felhasználók által felhasznált és csoportosított erőforrások. Ilyenek például a fájlok I/o-, a kapcsolatok és a memória.
- várj: Várakozási események gazdagép vagy felhasználó szerint csoportosítva.

Most nézzük meg a sys_schema gyakori használati mintáit. Első lépésként a használati mintákat két kategóriába csoportosítjuk: **Teljesítmény-Finomhangolás** és **adatbázis-karbantartás**.

## <a name="performance-tuning"></a>Teljesítmény-finomhangolás

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

Az IO a legdrágább művelet az adatbázisban. A *sys. user_summary_by_file_io* nézet lekérdezésével megtalálhatja az átlagos IO-késést. Az alapértelmezett 125 GB kiépített tárterület esetén az i/o-késés körülbelül 15 másodperc.

![IO-késés: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Mivel Azure Database for MySQL az i/o-méretezést a tárterület tekintetében, miután a kiépített tárterületet 1 TB-ra növelte, az i/o-késés a 571 MS-ra csökken.

![IO-késés: 1 TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

A gondos tervezés ellenére számos lekérdezés továbbra is teljes táblázatos vizsgálatokat eredményezhet. Az indexek típusaival és optimalizálásával kapcsolatos további információkért tekintse meg a következő cikket: [A lekérdezés teljesítményének megoldása](./howto-troubleshoot-query-performance.md). A teljes táblázatos vizsgálatok erőforrás-igényesek, és csökkentik az adatbázis teljesítményét. A teljes táblázatos vizsgálattal rendelkező táblák keresésének leggyorsabb módja a *sys. schema_tables_with_full_table_scans* nézet lekérdezése.

![teljes táblázatos vizsgálatok](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Az adatbázis-teljesítménnyel kapcsolatos problémák elhárítása érdekében hasznos lehet az adatbázison belül zajló események azonosítása, és a *sys. user_summary_by_statement_type* -nézet használata is csak a trükk.

![Összefoglaló utasítás szerint](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

Ebben a példában a 53 percet töltöttük a üt lekérdezési naplójának 44579-es időpontjának kiürítésével Azure Database for MySQL. Ez hosszú idő és sok IOs. Csökkentheti ezt a tevékenységet a lassú lekérdezési napló letiltásával vagy a lassú lekérdezés-bejelentkezési Azure Portal gyakoriságának csökkentésével.

## <a name="database-maintenance"></a>Adatbázis-karbantartás

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

A InnoDB-puffer a memóriában található, és az adatbázis-kezelő és a tároló fő gyorsítótárazási mechanizmusa. A InnoDB-puffer mérete a teljesítmény szintjéhez van kötve, és nem módosítható, kivéve, ha egy másik termék SKU van kiválasztva. Csakúgy, mint az operációs rendszer memóriája, a régi oldalakat felcseréli a rendszer, hogy helyet szabadítson fel a friss adatmennyiséghez. Annak megállapításához, hogy mely táblák használják fel a InnoDB-puffer teljes memóriáját, kérdezheti le a *sys. innodb_buffer_stats_by_table* nézetet.

![InnoDB-puffer állapota](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

A fenti ábrán látható, hogy a rendszertáblák és a nézetek kivételével a mysqldatabase033-adatbázis minden olyan táblája, amely az egyik WordPress-webhelyét üzemelteti, 16 KB-os vagy 1 lapot foglal le a memóriában.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Az indexek nagyszerű eszközöket biztosítanak az olvasási teljesítmény javítására, de további költségekkel járnak a lapkák és a tárolók esetében. A *sys. schema_unused_indexes* és a *sys. schema_redundant_indexes* nem használt vagy ismétlődő indexeket biztosít.

![nem használt indexek](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![redundáns indexek](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Összegzés

Összefoglalva, a sys_schema nagyszerű eszköz a teljesítmény finomhangolásához és az adatbázis-karbantartáshoz. Győződjön meg arról, hogy kihasználja ezt a funkciót a Azure Database for MySQL. 

## <a name="next-steps"></a>További lépések
- Ha a leginkább érintett kérdésekre ad választ, vagy új kérdést/választ tesz közzé, látogasson el az [MSDN fórumára](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) vagy a [stack Overflowra](https://stackoverflow.com/questions/tagged/azure-database-mysql).
