---
title: Lekérdezési statisztikák gyűjtése az egy Azure Database for PostgreSQL-kiszolgáló optimalizálása
description: Ez a cikk bemutatja, hogyan optimalizálhatók a lekérdezési statisztikák gyűjtése az egy Azure Database for PostgreSQL-kiszolgálóhoz.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 428a22f79008130448bef65f14322d6880b1b367
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814367"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql-server"></a>Lekérdezési statisztikák gyűjtését az Azure Database for PostgreSQL-kiszolgáló-optimalizálása 
Ez a cikk ismerteti, hogyan optimalizálható a lekérdezési statisztikák gyűjtését az Azure Database for PostgreSQL-kiszolgálót.

## <a name="use-pgstatsstatements"></a>Pg_stats_statements használata
**Pg_stat_statements** egy PostgreSQL-bővítmény, amely a PostgreSQL-hez készült Azure Database-ben alapértelmezés szerint engedélyezve van. A bővítmény megadja egy azt jelenti, hogy a kiszolgáló által végrehajtott összes SQL-utasítások végrehajtási statisztikák nyomon követéséhez. Ez a modul be minden lekérdezés-végrehajtás csatlakozik, és nem triviális teljesítményt is tartalmaz. Engedélyezés **pg_stat_statements** kényszeríti a lekérdezés szöveges írások a lemezen tárolt fájlok.

Ha hosszú lekérdezés szövege egyedi lekérdezések van, vagy nem aktívan figyeljük **pg_stat_statements**, tiltsa le **pg_stat_statements** a legjobb teljesítmény érdekében. Ehhez állítsa a beállítást `pg_stat_statements.track = NONE`.

Néhány ügyfél munkaterheléseinek láthatta, legfeljebb 50 százalékos teljesítményjavulást amikor **pg_stat_statements** le van tiltva. A kompromisszummal jár, győződjön meg arról, ha letiltja a pg_stat_statements megakadályozhatják a teljesítménnyel kapcsolatos problémák elhárításához.

Beállítása `pg_stat_statements.track = NONE`:

- Az Azure Portalon nyissa meg a [PostgreSQL erőforrás-kezelés lapon, és válassza a kiszolgáló paraméterei panelen](howto-configure-server-parameters-using-portal.md).

  ![PostgreSQL-kiszolgáló paraméter panel](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Használja a [Azure CLI-vel](howto-configure-server-parameters-using-cli.md) az postgres server configuration beállítása `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>A Query Store használata 
A [Query Store](concepts-query-store.md) PostgreSQL lekérdezési statisztikák nyomon hatékonyabb módszert biztosít az Azure Database szolgáltatásával. Ez a funkció használata helyett javasoljuk *pg_stats_statements*. 

## <a name="next-steps"></a>További lépések
Érdemes lehet `pg_stat_statements.track = NONE` a a [az Azure portal](howto-configure-server-parameters-using-portal.md) vagy a [Azure CLI-vel](howto-configure-server-parameters-using-cli.md).

További információkért lásd: 
- [Lekérdezéstár – használati forgatókönyvek](concepts-query-store-scenarios.md) 
- [Query Store ajánlott eljárások](concepts-query-store-best-practices.md) 
