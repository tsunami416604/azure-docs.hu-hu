---
title: "Memóriakép, és a PostgreSQL az Azure-adatbázis visszaállítása"
description: "Bontsa ki a PostgreSQL-adatbázisból egy biztonsági másolat fájlba, és az Azure Database pg_dump hozta létre a PostgreSQL-fájlból való visszaállítása ismerteti."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: f74c60cb99ee5bae1af8e000ebbd21b41600638d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Telepítse át az PostgreSQL-adatbázist használ a biztonsági másolat és helyreállítás
Használható [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) PostgreSQL-adatbázisból egy biztonsági másolat fájlba kibontásához és [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) fájlból történő visszaállításához a PostgreSQL-adatbázisból az archív pg_dump hozta létre.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- Egy [PostgreSQL-kiszolgáló Azure-adatbázis](quickstart-create-server-database-portal.md) a tűzfalszabályokat access és az adatbázis.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) telepített parancssori segédprogramok

Kövesse az alábbi lépéseket dump, és visszaállíthatja a PostgreSQL-adatbázisban:

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Hozzon létre egy biztonsági másolat fájlt, amely tartalmazza az adatok betöltését pg_dump használatával
Készítsen biztonsági másolatot egy meglévő PostgreSQL adatbázis helyszíni vagy egy virtuális Gépre, a következő parancsot:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Például, ha a helyi kiszolgáló és adatbázis **testdb** benne
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Állítsa vissza az adatokat a cél Azure Database-be a PostrgeSQL pg_restore használatával
Miután létrehozta a céladatbázis, használhatja a pg_restore parancs és a -d, az adatok helyreállítását a kiírt fájlok a cél adatbázisba--dbname paraméter.
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
--Nem-owner paraméter okairól minden objektumot létrehozni a felhasználó által megadott felhasználónévvel--tartozó a visszaállítás során is beleértve. További információkért lásd a PostgreSQL dokumentációs [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

Ebben a példában, visszaállíthatja az adatokat a memóriakép **testdb.dump** az adatbázisba **mypgsqldb** célkiszolgálón **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>További lépések
- Egy PostgreSQL-adatbázist az exportálás és importálás áttelepítéséhez lásd: [telepítse át az exportálási PostgreSQL-adatbázist, és importálása](howto-migrate-using-export-and-import.md)