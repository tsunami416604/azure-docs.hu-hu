---
title: Az Azure Database for MySQL Server-naplók
description: Az Azure Database-ben elérhető naplók a MySQL és a különböző naplózási szintek engedélyezésének paramétereket ismerteti.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 09/17/2018
ms.openlocfilehash: ac5be20815b552c08e5cd1054bf24d7a10b56498
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124269"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Kiszolgálói naplók az Azure Database for MySQL-hez
Az Azure Database for MySQL-hez a lassú lekérdezések naplója a felhasználók számára érhető el. A tranzakciós naplóba való hozzáférés nem támogatott. A lassú lekérdezések naplója segítségével azonosíthatja a szűk keresztmetszeteket hibaelhárításhoz. 

A MySQL lassú lekérdezések naplója kapcsolatos további információkért tekintse meg a MySQL referencia manuális [lassú lekérdezési szakasz](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Hozzáférés a kiszolgálói naplókhoz
A listában, és az Azure-adatbázis le az MySQL server-naplók az Azure Portalon, és az Azure CLI használatával.

Az Azure Portalon válassza ki az Azure Database for MySQL-kiszolgálóhoz. Alatt a **figyelés** szakaszban kattintson a **kiszolgálónaplók** lapot.

Az Azure CLI-vel további információkért lásd: [Azure CLI-vel kiszolgálónaplók konfigurálása és a hozzáférés](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Napló megőrzése
Naplók létrehozás legfeljebb hét napig érhetők el. Naplók összesített mérete meghaladja a 7 GB, ha ezután a legrégebbi fájlok törlődnek mindaddig, amíg a lemezterület áll rendelkezésre. 

Naplók vannak-e forgatni minden 24 órás vagy azt 7 GB, amelyik először bekövetkezik.


## <a name="configure-logging"></a>Naplózás konfigurálása 
A lassú lekérdezések naplója alapértelmezés szerint le van tiltva. Annak engedélyezéséhez állítsa slow_query_log ON.

Egyéb úgy módosíthatja a paraméterek a következők:

- **long_query_time**: Ha a lekérdezés hosszabb időt vesz igénybe, mint a long_query_time (másodpercben), hogy a lekérdezés a rendszer naplózza. Az alapértelmezett érték 10 másodperc.
- **log_slow_admin_statements**: Ha ON az utasításokban a slow_query_log írt felügyeleti utasítások, mint például a ALTER_TABLE és ANALYZE_TABLE tartalmazza.
- **log_queries_not_using_indexes**: azt határozza meg, hogy lekérdezések, amelyek nem használnak az indexek a slow_query_log naplózza
- **log_throttle_queries_not_using_indexes**: Ez a paraméter csak írható a lassú lekérdezések naplója nem index lekérdezések számát korlátozza. Ezt a paramétert akkor lép érvénybe, ha log_queries_not_using_indexes ON értékre van állítva.

Tekintse meg a MySQL [lassú lekérdezési napló dokumentáció](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) teljes leírását a lassú lekérdezések naplója paramétereket.

## <a name="next-steps"></a>További lépések
- [Konfigurálása és a naplók eléréséhez az Azure parancssori felületen](howto-configure-server-logs-in-cli.md).
