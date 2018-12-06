---
title: Lekérdezési statisztikák gyűjtemény az Azure Database for PostgreSQL kiszolgáló lekérdezési statisztikák gyűjtése optimalizálása
description: Ez a cikk bemutatja, hogyan optimalizálhatók a lekérdezési statisztikák gyűjtése az egy Azure Database for PostgreSQL-kiszolgálóhoz.
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 076442d85d7f628504cca95c36f3e99f4d0c5117
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966687"
---
# <a name="optimize-query-statistics-collection-in-azure-database-for-postgresql-server"></a>Lekérdezési statisztikák gyűjtését az Azure Database for PostgreSQL-kiszolgáló optimalizálása 
Ez a cikk ismerteti a lekérdezési statisztikák gyűjtését az Azure Database for PostgreSQL-kiszolgáló-optimalizálása érdekében.

## <a name="using-pgstatsstatements"></a>Pg_stats_statements használatával
**Pg_stat_statements** egy PostgreSQL-bővítmény, amely a PostgreSQL-hez készült Azure Database-ben alapértelmezés szerint engedélyezve van. A bővítmény megadja egy azt jelenti, hogy a kiszolgáló által végrehajtott összes SQL-utasítások végrehajtási statisztikák nyomon követéséhez. Azonban ez a modul be minden lekérdezés-végrehajtás csatlakozik, és nem triviális teljesítményt is tartalmaz. Engedélyezés **pg_stat_statements** kényszeríti a lekérdezés szöveges írások a lemezen tárolt fájlok.

Az ügyfelek, amelyek hosszú lekérdezés szövege egyedi lekérdezések vagy nem aktívan figyeli **pg_stat_statements**, javasoljuk, hogy letiltása **pg_stat_statements** beállításávalalegjobbteljesítményérdekében`pg_stat_statements.track = NONE`.

Az egyes ügyfél munkaterheléseinek úgy találtuk, legfeljebb 50 százalékos teljesítményjavulást letiltásával **pg_stat_statements**. Azonban az egyensúlyt a egy teszi pg_stat_statements letiltásával, teljesítménnyel kapcsolatos problémáinak elhárítása nem.

Beállítása `pg_stat_statements.track = NONE`:

- Az Azure Portalon lépjen a [PostgreSQL erőforrás-kezelés lapon, és válassza a kiszolgáló paraméterei panelen](howto-configure-server-parameters-using-portal.md).

![PostgreSQL-kiszolgáló paraméter panel](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Használatával [Azure CLI-vel](howto-configure-server-parameters-using-cli.md), az postgres server configuration set `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="using-query-store"></a>Query Store használata 
A [Query Store](concepts-query-store.md) szolgáltatásával az Azure-adatbázis PostgreSQL lehetőséget biztosít a több nagy teljesítményű lekérdezési statisztikák nyomon követésére, és használata helyett javasoljuk *pg_stats_statements*. 

## <a name="next-steps"></a>További lépések
Érdemes lehet `pg_stat_statements.track = NONE` a a [az Azure Portal](howto-configure-server-parameters-using-portal.md) vagy [Azure CLI-vel](howto-configure-server-parameters-using-cli.md).

Tekintse meg a [Query Store használati forgatókönyvek](concepts-query-store-scenarios.md) és [Query Store ajánlott eljárások](concepts-query-store-best-practices.md) további információt. 
