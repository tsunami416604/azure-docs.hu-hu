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
ms.date: 09/22/2018
ms.openlocfilehash: b8d5208992e8f12fae3c010748b2c494e0d50ee8
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465657"
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
Miután létrehozta a céladatbázis, a pg_restore parancsot és a -d,--dbname paraméter használatával állítsa vissza az adatokat a memóriakép-fájl a cél-adatbázisba.
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

## <a name="optimizing-the-migration-process"></a>Az áttelepítési folyamat optimalizálása

A meglévő PostgreSQL-adatbázis migrálása az Azure Database for PostgreSQL szolgáltatás egyik módja, hogy az adatbázis biztonsági mentése a forráson, és állítsa vissza az Azure-ban. Az áttelepítés végrehajtásához szükséges idő minimalizálása érdekében fontolja meg az alábbi paramétereket a biztonsági mentést, és állítsa vissza a parancsokat.

> [!NOTE]
> Szintaxis részletes információkért tekintse meg a cikkeket [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>A biztonsági mentés
- A biztonsági mentés a szálcsatorna - kapcsoló igénybe, így a visszaállítást hajthat végre párhuzamosan, felgyorsítása érdekében. Példa:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName > Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>A visszaállításhoz
- Másolja a biztonságimásolat-fájlt az Azure blob/tárolóban, és hajtsa végre a visszaállítást onnan. Gyorsabb, mint a visszaállítás végrehajtása az interneten keresztül kell lennie. 
- Alapértelmezés szerint már végezhető, de a memóriakép-fájl, és ellenőrizze, hogy a create index utasításokat az adatok a Beszúrás után nyissa meg. Ha nem, akkor a helyzet, a create index utasításokat áthelyezése után az adatok.
- Állítsa vissza a kapcsolók a -Fc és -j *#* való párhuzamosíthatja a visszaállítást. *#* van a célkiszolgálón magok számát. Az is megpróbálhatja *#* kétszer a célkiszolgáló magok számát értékre van állítva,-azonosítókra gyakorolt hatást. Példa:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- A parancs hozzáadásával a memóriakép-fájl is szerkesztheti *: synchronous_commit állítsa le; =* elején és a parancs *beállítása: synchronous_commit = on;* végén. Nem bekapcsolását, a végén az alkalmazásokat módosítani az adatokat, mielőtt az adatok későbbi adatvesztést eredményezhet.

Ne felejtse el tesztelés és ellenőrzés ezeket a parancsokat egy tesztkörnyezetben, mielőtt éles környezetben használni.

## <a name="next-steps"></a>További lépések
- Az exportálás és importálás segítségével a PostgreSQL-adatbázis áttelepítéséhez lásd: [exportálási PostgreSQL adatbázis Migrálása és importálása](howto-migrate-using-export-and-import.md).
- További információ az adatbázisok az Azure Database for postgresql-hez, lásd: a [adatbázis-Migrálási útmutató](http://aka.ms/datamigration).
