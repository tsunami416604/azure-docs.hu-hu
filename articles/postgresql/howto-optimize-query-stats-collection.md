---
title: Lekérdezési statisztikák gyűjtésének optimalizálása – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan optimalizálható a lekérdezési statisztikák gyűjtése egy Azure Database for PostgreSQL egyetlen kiszolgálón
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: a4c2051a3f52ae363d8dc704aef790d8ce77efbd
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116199"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Lekérdezési statisztikák gyűjtésének optimalizálása egy Azure Database for PostgreSQL egyetlen kiszolgálón
Ez a cikk azt ismerteti, hogyan optimalizálható a lekérdezési statisztikák gyűjteménye egy Azure Database for PostgreSQL kiszolgálón.

## <a name="use-pg_stats_statements"></a>Pg_stats_statements használata
**Pg_stat_statements** egy PostgreSQL-bővítmény, amely alapértelmezés szerint engedélyezve van a Azure Database for PostgreSQLban. A bővítmény segítségével nyomon követhető a kiszolgáló által végrehajtott összes SQL-utasítás végrehajtási statisztikája. Ez a modul minden lekérdezés-végrehajtáshoz csatlakozik, és nem triviális teljesítménnyel jár. A **pg_stat_statements** kényszeríti a szöveges írásokat a lemezen lévő fájlokba.

Ha olyan egyedi lekérdezésekkel rendelkezik, amelyek hosszú lekérdezési szöveggel rendelkeznek, vagy ha nem figyeli aktívan a **pg_stat_statements**, tiltsa le **pg_stat_statements** a legjobb teljesítmény érdekében. Ehhez módosítsa a beállítást a következőre: `pg_stat_statements.track = NONE` .

Egyes ügyfelek munkaterhelései egy 50 százalékos teljesítménybeli javulást észleltek, ha **pg_stat_statements** le van tiltva. A pg_stat_statements letiltásakor felmerülő kompromisszum nem képes a teljesítménnyel kapcsolatos problémák elhárítására.

A következő beállításhoz `pg_stat_statements.track = NONE` :

- A Azure Portal lépjen a [PostgreSQL erőforrás-kezelés lapra, és válassza a kiszolgálói paraméterek](howto-configure-server-parameters-using-portal.md)panelt.

  ![PostgreSQL-kiszolgáló paraméterének panelje](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Használja az [Azure CLI](howto-configure-server-parameters-using-cli.md) az postgres Server Configuration set to `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE` .

## <a name="use-the-query-store"></a>A lekérdezési tároló használata 
A Azure Database for PostgreSQL [lekérdezés-tárolási](concepts-query-store.md) funkciója hatékonyabb módszert biztosít a lekérdezési statisztikák nyomon követésére. Ezt a funkciót a *pg_stats_statements*használatának alternatívájaként ajánljuk. 

## <a name="next-steps"></a>Következő lépések
Vegye fontolóra a beállítást `pg_stat_statements.track = NONE` a [Azure Portal](howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md)használatával.

További információkért lásd: 
- [Lekérdezéstár – használati forgatókönyvek](concepts-query-store-scenarios.md) 
- [Ajánlott eljárások a lekérdezéstár használatához](concepts-query-store-best-practices.md) 
