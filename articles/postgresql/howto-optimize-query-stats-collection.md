---
title: Lekérdezési statisztikák gyűjteményének optimalizálása - Azure Database for PostgreSQL - Single Server
description: Ez a cikk azt ismerteti, hogyan optimalizálhatja a lekérdezési statisztikák gyűjteményét egy Azure Database for PostgreSQL - Single Server adatbázisban
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f467f01118470eb51f7decf3bd6457917c566723
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770169"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Lekérdezési statisztikák gyűjteményének optimalizálása Egy Azure-adatbázison postgreSQL- Egykiszolgálós
Ez a cikk ismerteti, hogyan optimalizálhatja a lekérdezési statisztikák gyűjteménye egy Azure Database for PostgreSQL-kiszolgálón.

## <a name="use-pg_stats_statements"></a>A pg_stats_statements használata
**Pg_stat_statements** egy PostgreSQL-bővítmény, amely alapértelmezés szerint engedélyezve van az Azure Database for PostgreSQL-ben. A bővítmény lehetővé teszi a kiszolgáló által végrehajtott összes SQL-utasítás végrehajtási statisztikáinak nyomon követését. Ez a modul minden lekérdezés-végrehajtáshoz kapcsolódik, és nem triviális teljesítményköltséggel jár. Ha engedélyezi **pg_stat_statements** kényszeríti a lekérdezésszöveg írását a lemezen lévő fájlokba.

Ha egyedi lekérdezései vannak hosszú lekérdezésszöveggel, vagy nem figyeli aktívan **pg_stat_statements,** tiltsa le **pg_stat_statements** a legjobb teljesítmény érdekében. Ehhez módosítsa a beállítást `pg_stat_statements.track = NONE`.

Egyes ügyfél-munkaterhelések 50 százalékos teljesítményjavulást **tapasztaltak,** ha pg_stat_statements le van tiltva. A pg_stat_statements letiltása kor kötkompromisszumot a teljesítménybeli problémák elhárításának képtelensége teszi.

Beállítás: `pg_stat_statements.track = NONE`

- Az Azure Portalon nyissa meg a [PostgreSQL erőforrás-kezelési lapot, és válassza ki a kiszolgáló paramétereit.](howto-configure-server-parameters-using-portal.md)

  ![PostgreSQL szerver paraméter panel](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Használja az [Azure CLI](howto-configure-server-parameters-using-cli.md) az postgres kiszolgáló `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`konfigurációját.

## <a name="use-the-query-store"></a>A Lekérdezéstároló használata 
A [Query Store](concepts-query-store.md) szolgáltatás az Azure Database for PostgreSQL hatékonyabb módszert biztosít a lekérdezési statisztikák nyomon követésére. Ezt a funkciót a *pg_stats_statements.* 

## <a name="next-steps"></a>További lépések
Fontolja `pg_stat_statements.track = NONE` meg az [Azure Portalon](howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md)használatával történő beállítást.

További információkért lásd: 
- [Lekérdezéstár – használati forgatókönyvek](concepts-query-store-scenarios.md) 
- [Ajánlott eljárások a lekérdezéstár használatához](concepts-query-store-best-practices.md) 
