---
title: Memóriakép és visszaállítása az Azure Database for postgresql-hez
description: Bontsa ki a PostgreSQL-adatbázis egy memóriakép fájlba, és a egy PostgreSQL-hez készült Azure Database-ben pg_dump által létrehozott fájl visszaállítása ismerteti.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/19/2018
ms.openlocfilehash: 94d196ceecc0b63b9f0b0fe94f71363dc2086c30
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213650"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Memóriakép és visszaállítás használatával a PostgreSQL-adatbázis migrálása
Használhatja [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) be memóriakép-fájl egy PostgreSQL-adatbázis kibontásához és [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) pg_dump által létrehozott archív fájl visszaállítása a PostgreSQL-adatbázishoz.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- Egy [, Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md) , hogy a hozzáférés és az alatta database tűzfalszabályokkal.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) parancssori segédprogramok telepítése

Kövesse az alábbi lépéseket a dump és a PostgreSQL-adatbázis visszaállítása:

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Hozzon létre egy memóriakép pg_dump kell betölteni az adatokat tartalmazó fájlt
Készítsen biztonsági másolatot egy meglévő PostgreSQL adatbázis helyi vagy egy virtuális Gépre, futtassa a következő parancsot:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Ha például van egy helyi kiszolgálón és a egy nevű adatbázist **testdb** benne
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```

> [!IMPORTANT]
> Másolja a biztonságimásolat-fájlokat egy Azure blob/tároló és a visszaállítás végrehajtása itt, ami sokkal gyorsabb, mint a visszaállítás végrehajtása az interneten keresztül kell lennie.
> 

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Állítsa vissza az adatokat a cél Azure Database-be a PostrgeSQL pg_restore használatával
Miután létrehozta a céladatbázis, használhatja a pg_restore parancsot és a -d-,--dbname paramétert állítsa vissza az adatokat a memóriakép-fájl a cél-adatbázisba.
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Beleértve a--no-owner paraméter okok tulajdonosa a felhasználó megadja a--username és a visszaállítás során létrehozott összes objektumot. További információkért tekintse meg a hivatalos PostgreSQL dokumentációja [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Ha a PostgreSQL-kiszolgáló SSL-kapcsolatot igényel (az Azure Database for PostgreSQL-kiszolgálók alapértelmezés szerint), egy környezeti változót `PGSSLMODE=require` úgy, hogy SSL használatával kapcsolódik a pg_restore eszköz. SSL-t, anélkül a hiba lehetséges, hogy olvasása  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> A Windows-parancssorban futtassa a parancsot `SET PGSSLMODE=require` a pg_restore parancs futtatása előtt. Linux vagy a Bash futtassa a parancsot `export PGSSLMODE=require` a pg_restore parancs futtatása előtt.
>

Ebben a példában visszaállíthatja az adatokat a memóriakép-fájl **testdb.dump** az adatbázisba **mypgsqldb** célkiszolgálón **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>További lépések
- Az exportálás és importálás segítségével a PostgreSQL-adatbázis áttelepítéséhez lásd: [exportálási PostgreSQL adatbázis Migrálása és importálása](howto-migrate-using-export-and-import.md).
- További információ az adatbázisok az Azure Database for postgresql-hez, lásd: a [adatbázis-Migrálási útmutató](http://aka.ms/datamigration).
