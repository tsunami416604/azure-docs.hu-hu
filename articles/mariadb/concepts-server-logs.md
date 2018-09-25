---
title: Az Azure Database for MariaDB webkiszolgáló-naplókkal
description: A témakör ismerteti az Azure Database-ben elérhető naplók MariaDB, valamint a különböző naplózási szintek engedélyezésének a rendelkezésre álló paramétereket.
author: rachel-msft
ms.author: raagyema
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d982af64517ed452d907f62a39e975d472951392
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992983"
---
# <a name="server-logs-in-azure-database-for-mariadb"></a>Kiszolgálói naplók az Azure Database for MariaDB
Az Azure Database for MariaDB a lassú lekérdezések naplója a felhasználók számára érhető el. A tranzakciós naplóba való hozzáférés nem támogatott. A lassú lekérdezések naplója segítségével azonosíthatja a szűk keresztmetszeteket hibaelhárításhoz.

A lassú lekérdezések naplója kapcsolatos további információkért lásd: MariaDB dokumentációját [lassú lekérdezések naplója](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-server-logs"></a>Hozzáférés a kiszolgálói naplókhoz
Listán, és töltse le az Azure Database for MariaDB naplóit az Azure Portalon, és az Azure CLI használatával.

Az Azure Portalon válassza ki az Azure Database for MariaDB-kiszolgáló. Alatt a **figyelés** szakaszban kattintson a **kiszolgálónaplók** lapot.

<!-- For more information on Azure CLI, see [Configure and access server logs using Azure CLI](howto-configure-server-logs-in-cli.md).-->

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

Tekintse meg a MariaDB [lassú lekérdezési napló dokumentáció](https://mariadb.com/kb/en/library/slow-query-log-overview/) teljes leírását a lassú lekérdezések naplója paramétereket.

## <a name="next-steps"></a>További lépések
- [Kiszolgálói naplók elérése az Azure Portalról és konfigurálása](howto-configure-server-logs-portal.md).
