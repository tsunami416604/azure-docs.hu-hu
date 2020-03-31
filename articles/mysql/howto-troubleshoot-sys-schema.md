---
title: Használja sys_schema - Azure Database for MySQL
description: Ismerje meg, hogyan használhatja sys_schema a teljesítménnyel kapcsolatos problémák megkeresésére és az adatbázisok karbantartására az Azure Database for MySQL-ben.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: a35a586a519ff78e8b32d986b92bd008b2c6b858
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067868"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>A sys_schema használata teljesítményhangolásra és adatbázis-karbantartásra az Azure Database for MySQL-ben

A MySQL performance_schema, először elérhető a MySQL 5.5, műszerek számos létfontosságú szerver erőforrások, mint például a memória foglalás, tárolt programok, metaadatok zárolása, stb A performance_schema azonban több mint 80 táblát tartalmaz, és a szükséges információk beszerzése gyakran megköveteli a táblák összekapcsolásához a performance_schema, valamint a information_schema táblákat. A performance_schema és information_schema építve a sys_schema [felhasználóbarát nézetek](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) hatékony gyűjteményét biztosítja egy csak olvasható adatbázisban, és teljes mértékben engedélyezve van az Azure Database for MySQL 5.7-es verziójában.

![a sys_schema nézetei](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

A sys_schema 52 nézet található, és mindegyik nézet a következő előtagok egyikével rendelkezik:

- Host_summary vagy Io: I/O-val kapcsolatos késések.
- InnoDB: InnoDB puffer állapota és zárolása.
- Memória: A gazdagép és a felhasználók memóriahasználata.
- Séma: Sémával kapcsolatos információk, például automatikus növekmény, indexek stb.
- Utasítás: Információ az SQL utasításokról; ez lehet utasítás, amely teljes táblavizsgálat, vagy hosszú lekérdezési idő.
- Felhasználó: A felhasználók által felhasznált és csoportosított erőforrások. Ilyenek például a fájl i/o-i, a kapcsolatok és a memória.
- Várakozás: Várakozási események állomás vagy felhasználó szerint csoportosítva.

Most nézzük meg néhány közös használati minták a sys_schema. Először is a használati mintákat két kategóriába soroljuk: **Teljesítményhangolás** és **Adatbázis-karbantartás.**

## <a name="performance-tuning"></a>Teljesítmény-finomhangolás

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

Az IO az adatbázis legdrágább művelete. A *sys.user_summary_by_file_io* nézet lekérdezésével megtudjuk az átlagos I/O-késést. Az alapértelmezett 125 GB-os kiépített tároló, az I/O-késés körülbelül 15 másodperc.

![io késleltetés: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Mivel az Azure Database for MySQL skálázódik az IO-t a tárolás tekintetében, miután a kiosztott tárolót 1 TB-ra növeli, az I/O-késésem 571 ms-ra csökken.

![io késleltetés: 1 TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

A gondos tervezés ellenére sok lekérdezés továbbra is teljes táblavizsgálatokat eredményezhet. Az indexek típusáról és optimalizálásukról a következő cikkben olvashat: [Lekérdezési teljesítmény hibaelhárítása](./howto-troubleshoot-query-performance.md). A teljes táblavizsgálatok erőforrás-igényesek, és rontják az adatbázis teljesítményét. A teljes táblavizsgálattal rendelkező táblák keresésének leggyorsabb módja a *sys.schema_tables_with_full_table_scans nézet lekérdezése.*

![teljes táblázat vizsgál](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Az adatbázis teljesítményével kapcsolatos problémák elhárításához hasznos lehet azonosítani az adatbázison belül zajló eseményeket, és a *sys.user_summary_by_statement_type* nézet használata csak ezt a trükköt teheti meg.

![összefoglaló kivonatszerint](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

Ebben a példában az Azure Database for MySQL 53 percet töltött a slog lekérdezési napló kiürítésével 44579 alkalommal. Az hosszú idő, és sok ios. Csökkentheti ezt a tevékenységet a lassú lekérdezési napló letiltásával, vagy a lassú lekérdezési bejelentkezés gyakoriságának csökkentésével az Azure Portalon.

## <a name="database-maintenance"></a>Adatbázis karbantartása

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

Az InnoDB pufferkészlet a memóriában található, és a dbms és a tároló közötti fő gyorsítótár-mechanizmus. Az InnoDB pufferkészlet mérete a teljesítményszinthez van kötve, és csak akkor módosítható, ha másik terméktermékváltozat van kiválasztva. Az operációs rendszer memóriájához is, a régi oldalakat is kicserélik, hogy helyet adjanak a frissebb adatoknak. Annak kiderítéséhez, hogy mely táblák használják fel az InnoDB pufferkészlet memóriájának nagy részét, lekérdezheti a *sys.innodb_buffer_stats_by_table* nézetet.

![InnoDB puffer állapota](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

A fenti ábrán nyilvánvaló, hogy a rendszertáblákon és nézeteken kívül a mysqldatabase033 adatbázis minden táblája, amely az egyik WordPress webhelyet üzemelteti, 16 KB vagy 1 oldalt foglal el a memóriában lévő adatokból.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Az indexek nagyszerű eszközök az olvasási teljesítmény javítására, de további költségekkel járnak a lapkák és a tárolás számára. *A Sys.schema_unused_indexes* és *a sys.schema_redundant_indexes* betekintést nyújt a fel nem használt vagy ismétlődő indexekbe.

![nem használt indexek](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![redundáns indexek](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Összegzés

Összefoglalva, a sys_schema egy nagyszerű eszköz mind a teljesítmény hangolása és adatbázis-karbantartás. Győződjön meg róla, hogy kihasználja ezt a funkciót az Azure Database for MySQL.Make sure to advantage of this feature in your Azure Database for MySQL. 

## <a name="next-steps"></a>További lépések
- Ha szeretné megtalálni a társak válaszait a leginkább érintett kérdésekre, vagy tegye az új kérdés / válasz, látogasson el [MSDN fórum](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) vagy [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
