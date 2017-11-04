---
title: "MySQL az Azure-adatbázis a kiszolgáló naplóiban |} Microsoft Docs"
description: "Ismerteti az Azure-adatbázisban elérhető naplók a MySQL és a különböző naplózási szintek engedélyezésének a rendelkezésre álló paramétereket."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/18/2017
ms.openlocfilehash: 13b30df82c1a6c4c45a621a1f7a40148a55a7648
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2017
---
# <a name="server-logs-in-azure-database-for-mysql"></a>A MySQL-kiszolgáló naplóit, az Azure-adatbázis
A MySQL adatbázis Azure, a lassú lekérdezés napló érhető el a felhasználók számára. A tranzakciós napló elérése nem támogatott. A lassú lekérdezés napló segítségével hibaelhárítási szűk keresztmetszetek azonosítása. 

A MySQL lassú lekérdezés napló kapcsolatos további információkért tekintse meg a MySQL hivatkozás manuális [napló lekérdezésszakaszt lassú](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Hozzáférés a kiszolgálói naplókhoz
Listáról, és töltse le az Azure-adatbázis a MySQL-kiszolgáló naplók az Azure-portálon, és az Azure parancssori felület használatával.

Az Azure-portálon válassza ki a MySQL-kiszolgálóhoz tartozó Azure-adatbázis. Az a **figyelés** elemcsoportban válassza ki a **kiszolgáló naplóiban** lap.

Azure parancssori felület további információkért lásd: [beállítása és hozzáférést kiszolgálónaplókban olvashatók Azure parancssori felület használatával](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Napló megőrzési
Érhetők el naplók a létrehozástól hét napig. Ha a naplók összesített mérete meghaladja a 7.5 GB, akkor a legrégebbi törlésre kerül mindaddig, amíg elérhető lemezterület. 

Naplók legyenek-e elforgatva minden 24 óra vagy 7.5 GB, amelyik előbb eléri.


## <a name="configure-logging"></a>Naplózás konfigurálása 
A lassú lekérdezés napló alapértelmezés szerint le van tiltva. Az engedélyezéshez slow_query_log beállítása ON.

Más paramétereket, módosíthatja a következők:

- **long_query_time**: Ha a lekérdezés hosszabb időbe telik long_query_time (másodpercben), hogy a lekérdezés kerül. Az alapértelmezett érték 10 másodperc.
- **log_slow_admin_statements**: Ha ON rendszergazdai utasítások, mint például a ALTER_TABLE és ANALYZE_TABLE tartalmazza a slow_query_log írni az utasításokban.
- **log_queries_not_using_indexes**: meghatározza, hogy indexek nem használó lekérdezések naplózza a slow_query_log
- **log_throttle_queries_not_using_indexes**: Ez a paraméter csak írható a lassú lekérdezés napló nem index lekérdezések számának korlátozása. Ez a paraméter lép érvénybe, amikor log_queries_not_using_indexes ON értékre van állítva.

Tekintse meg a MySQL [lassú a lekérdezési napló dokumentáció](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) lassú lekérdezésparaméter napló részletes leírását.

## <a name="next-steps"></a>Következő lépések
- [Hogyan lehet konfigurálni, és hozzáférést kiszolgálónaplókban az Azure parancssori felület](howto-configure-server-logs-in-cli.md).
