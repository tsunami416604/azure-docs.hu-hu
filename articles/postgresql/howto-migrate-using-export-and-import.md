---
title: Adatbázis áttelepítése - Azure Database for PostgreSQL - Single Server
description: Ez a témakör azt ismerteti, hogy a PostgreSQL-adatbázis hogyan bontható ki parancsfájlba, és hogyan importálja az adatokat a céladatbázisba abból a fájlból.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: f7cf5d245383b8a58f03e2e3610750866a2f4b5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770203"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>A PostgreSQL adatbázis áttelepítése exportálás és importálás használatával
A [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) segítségével kibonthatja a PostgreSQL adatbázist egy parancsfájlba, és [psql](https://www.postgresql.org/docs/current/static/app-psql.html) importálhatja az adatokat a céladatbázisba abból a fájlból.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md) tűzfalszabályoklehetővé teszi a hozzáférést és az adatbázis alatta.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) parancssori segédprogram telepítve
- [psql](https://www.postgresql.org/docs/current/static/app-psql.html) parancssori segédprogram telepítve

A PostgreSQL-adatbázis exportálásához és importálásához kövesse az alábbi lépéseket.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Parancsfájl létrehozása a betöltendő adatokat tartalmazó pg_dump használatával
Ha a meglévő PostgreSQL-adatbázist a helyszínen vagy egy virtuális gépen szeretné exportálni egy SQL parancsfájlba, futtassa a következő parancsot a meglévő környezetben:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Ha például van benne egy helyi kiszolgáló és egy **testdb** nevű adatbázis:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Adatok importálása a PostgreSQL célAzure-adatbázisában
A psql parancssorés a --dbname paraméter (-d) segítségével importálhatja az adatokat az Azure Database for PostgreSQL kiszolgálóra, és adatokat tölthet be az sql fájlból.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Ez a példa a psql segédprogramot és a **testdb.sql** nevű parancsfájlt használja az előző lépésből az adatok importálásához a **célkiszolgálón lévő mypgsqldb** adatbázisba **mydemoserver.postgres.database.azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>További lépések
- Ha egy PostgreSQL adatbázist memóriakép és visszaállítás segítségével szeretne áttelepíteni, olvassa el [A PostgreSQL-adatbázis áttelepítése dump és visszaállítás használatával című témakört.](howto-migrate-using-dump-and-restore.md)
- Az adatbázisok Azure Database for PostgreSQL szolgáltatásba való áttelepítéséről az [Adatbázis-áttelepítési útmutatóban](https://aka.ms/datamigration)talál további információt. 
