---
title: Adatbázis migrálása – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Leírja, hogyan bontsa ki a PostgreSQL-adatbázist egy parancsfájlba, és importálja azokat a fájlból a céladatbázisbe.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: f7cf5d245383b8a58f03e2e3610750866a2f4b5a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770203"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>A PostgreSQL-adatbázis migrálása exportálással és importálással
A [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) használatával kinyerheti a PostgreSQL-adatbázist egy parancsfájlba, és [psql](https://www.postgresql.org/docs/current/static/app-psql.html) importálhatja azokat a fájlból a céladatbázisbe.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md) , amely tűzfalszabályok használatával engedélyezi a hozzáférést és az adatbázist.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) telepített parancssori segédprogram
- telepített [psql](https://www.postgresql.org/docs/current/static/app-psql.html) parancssori segédprogram

A PostgreSQL-adatbázis exportálásához és importálásához kövesse az alábbi lépéseket.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Parancsfájl létrehozása pg_dump használatával, amely tartalmazza a betölteni kívánt adatfájlokat
Ha a meglévő PostgreSQL-adatbázist helyileg vagy egy virtuális gépen szeretné exportálni egy SQL-parancsfájlba, futtassa a következő parancsot a meglévő környezetében:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Ha például van egy helyi kiszolgálója és egy **testdb** nevű adatbázisa:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>A cél Azure Database for PostgreSQL vonatkozó adatimportálás
A psql parancssor és a--dbname paraméter (-d) használatával importálja az adatait a Azure Database for PostgreSQL-kiszolgálóra, és betöltheti az adatait az SQL-fájlból.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Ez a példa a psql segédprogramot és egy **testdb. SQL** nevű parancsfájlt használ az előző lépésben az adatok importálásához a célkiszolgáló **mydemoserver.postgres.database.Azure.com**adatbázis- **mypgsqldb** .
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Következő lépések
- A PostgreSQL-adatbázisok memóriakép és visszaállítás használatával történő áttelepítéséhez lásd: [a PostgreSQL-adatbázis migrálása a dump és a Restore paranccsal](howto-migrate-using-dump-and-restore.md).
- Az adatbázisok Azure Database for PostgreSQLre való áttelepítésével kapcsolatos további információkért tekintse meg az [adatbázis-áttelepítési útmutatót](https://aka.ms/datamigration). 
