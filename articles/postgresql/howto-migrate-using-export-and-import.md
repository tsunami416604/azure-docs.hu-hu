---
title: Telepítse át az importálási egy adatbázist és az Azure Database PostgreSQL exportálás
description: Ismerteti, hogyan bontsa ki a PostgreSQL-adatbázisból egy parancsfájl-fájlba, és az adatok importálása a céladatbázis, a fájl.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 5250b10c2d9645e25c5b6f6f0f34daa50608e51b
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736644"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Telepítse át az exportálási PostgreSQL-adatbázist, és importálása
Használható [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) kibontásához PostgreSQL-adatbázisból egy parancsfájl fájlba és [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) az adatok importálása a céladatbázis, a fájl számára.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- Egy [PostgreSQL-kiszolgáló Azure-adatbázis](quickstart-create-server-database-portal.md) a tűzfalszabályokat access és az adatbázis.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) telepített parancssori segédprogram
- [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) telepített parancssori segédprogram

A lépések segítségével exportálja és importálja a PostgreSQL-adatbázisból.

## <a name="create-a-script-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Az adatok betöltését tartalmazó pg_dump használatával parancsfájl létrehozása
A meglévő PostgreSQL adatbázis helyszíni exportálhatók, és egy virtuális Gépet egy sql-parancsfájlt, futtassa a következő parancsot a meglévő környezetben:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Például, ha a helyi kiszolgáló és adatbázis **testdb** benne:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>A cél Azure-adatbázis adatok importálása az PostgreSQL
Psql parancssor és a--dbname paramétert (-d) számára az adatok importálása az Azure-adatbázishoz tartozó PostgreSQL kiszolgáló és az adatok betöltése az sql-fájlból.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Ez a példa psql segédprogram és nevű parancsfájl **testdb.sql** az előző lépésben, hogy adatokat importáljon belőlük az adatbázis **mypgsqldb** a célkiszolgálón  **mydemoserver.postgres.Database.Azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>További lépések
- Egy PostgreSQL-adatbázist biztonsági másolat és helyreállítás áttelepítéséhez lásd: [telepítse át az PostgreSQL-adatbázist használ a biztonsági másolat és helyreállítás](howto-migrate-using-dump-and-restore.md).
- Áttelepítéssel kapcsolatos további információkért adatbázisokat az Azure Database-PostgreSQL, tekintse meg a [adatbázis áttelepítési útmutató](http://aka.ms/datamigration). 
