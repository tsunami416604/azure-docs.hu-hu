---
title: Memóriakép és visszaállítás - Azure Database for PostgreSQL - Single Server
description: A postgreSQL-adatbázis kibontása egy memóriaképfájlba, és a pg_dump által az Azure Database for PostgreSQL – Single Server rendszerben létrehozott fájlból történő visszaállítása.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 4365338efa56593e80edcc19cba5944b213d2b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770237"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>PostgreSQL-adatbázis migrálása memóriakép és visszaállítás használatával
[A pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) segítségével kibonthatja a PostgreSQL adatbázist egy memóriaképfájlba, és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) a PostgreSQL adatbázis visszaállításához a pg_dump által létrehozott archív fájlból.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md) tűzfalszabályoklehetővé teszi a hozzáférést és az adatbázis alatta.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) parancssori segédprogramok telepítve

A PostgreSQL adatbázis kiírásához és visszaállításához kövesse az alábbi lépéseket:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Memóriaképfájl létrehozása a betöltendő adatokat tartalmazó pg_dump használatával
Ha egy meglévő PostgreSQL-adatbázisról szeretne biztonsági másolatot küldeni a helyszínen vagy egy virtuális gépen, futtassa a következő parancsot:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Például, ha van egy helyi szerver és egy adatbázis nevű **testdb** benne
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>Állítsa vissza az adatokat a postgreSQL célAzure-adatbázisába pg_restore
A céladatbázis létrehozása után a pg_restore paranccsal és a -d, --dbname paraméterrel visszaállíthatja az adatokat a céladatbázisba a memóriaképfájlból.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
A --no-owner paraméter rel együtt a visszaállítás során létrehozott összes objektum tulajdonosa a --username paraméterrel megadott felhasználó. További információt a PostgreSQL hivatalos dokumentációjában [talál pg_restore.](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html)

> [!NOTE]
> Ha a PostgreSQL-kiszolgáló ssl-kapcsolatokat igényel (alapértelmezés szerint az Azure `PGSSLMODE=require` Database for PostgreSQL-kiszolgálókban), állítson be egy környezeti változót úgy, hogy a pg_restore eszköz csatlakozzon az SSL-hez. SSL nélkül a hiba`FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> A Windows parancssorában futtassa a parancsot `SET PGSSLMODE=require` a pg_restore parancs futtatása előtt. Linux vagy Bash futtassa a parancsot, `export PGSSLMODE=require` mielőtt a pg_restore parancsot.
>

Ebben a példában állítsa vissza az adatokat a dump **fájlból testdb.dump** az adatbázisba **mypgsqldb** a célszerveren **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Az áttelepítési folyamat optimalizálása

A meglévő PostgreSQL-adatbázis átáttelepítésének egyik módja az Azure Database for PostgreSQL szolgáltatásba, ha biztonsági másolatot ad az adatbázisról a forráson, és visszaállítja azt az Azure-ban. Az áttelepítés befejezéséhez szükséges idő minimalizálása érdekében érdemes a következő paramétereket használni a biztonsági mentési és visszaállítási parancsokkal.

> [!NOTE]
> A szintaktikai részletes információkat a [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) és pg_restore című [cikkben talál.](https://www.postgresql.org/docs/current/static/app-pgrestore.html)
>

### <a name="for-the-backup"></a>A biztonsági mentéshez
- Vegye ki a biztonsági másolatot a -Fc kapcsolóval, hogy a visszaállítást párhuzamosan hajthassa végre, hogy felgyorsítsa. Példa:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName -f Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>A visszaállításhoz
- Azt javasoljuk, hogy helyezze át a biztonsági másolat fájlt egy Azure virtuális gép ugyanabban a régióban, mint az Azure Database for PostgreSQL-kiszolgáló, amelyhez áttelepíti, és a pg_restore a virtuális gépről a hálózati késés csökkentése érdekében. Azt is javasoljuk, hogy a virtuális gép gyorsított [hálózati engedélyezve](../virtual-network/create-vm-accelerated-networking-powershell.md) van.

- Alapértelmezés szerint már el kell végezni, de nyissa meg a memóriaképfájlt, és ellenőrizze, hogy a create indexutasítások az adatok beszúrása után vannak-e. Ha nem ez a helyzet, helyezze át a create index utasítások az adatok beszúrása után.

- Visszaállítás a -Fc és -j *#* kapcsolókkal a visszaállítás párhuzamosításához. *#* a célkiszolgálón lévő magok száma. Megpróbálhatja a *#* célkiszolgáló magjainak kétszeresére való beállítással is a hatás megtekintéséhez. Példa:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- A kiírási fájlt úgy is szerkesztheti, hogy hozzáadja a *synchronous_commit = ki,* az elején és a *parancskészletet synchronous_commit = be;* a végén. Ha nem kapcsolja be a végén, mielőtt az alkalmazások megváltoztatnák az adatokat, az adatvesztést eredményezhet.

- A postgreSQL-kiszolgálóhoz készült cél Azure-adatbázisban a visszaállítás előtt tegye meg a következőket:
    - Kapcsolja ki a lekérdezés teljesítményének nyomon követését, mivel ezekre a statisztikákra nincs szükség az áttelepítés során. Ezt úgy teheti meg, hogy pg_stat_statements.track, pg_qs.query_capture_mode és pgms_wait_sampling.query_capture_mode.query_capture_mode at NONE.

    - Az áttelepítés felgyorsítása érdekében használjon nagy számítási és nagy memóriával használható termékváltozatokat, például a 32 virtuálismagos memóriaoptimalizált at. A visszaállítás befejezése után könnyedén leskálázhatja a kívánt termékváltozatra. Minél magasabb a sku, annál több párhuzamosság `-j` érhető el a megfelelő paraméter növelésével a pg_restore parancsban. 

    - A célkiszolgálón további IOPS-adatok javíthatják a visszaállítási teljesítményt. A kiszolgáló tárolási méretének növelésével további IOPS-t hozhat ki. Ez a beállítás nem visszafordítható, de fontolja meg, hogy egy magasabb IOPS előnyös lenne a tényleges számítási feladatok a jövőben.

Ne felejtse el tesztelni és érvényesíteni ezeket a parancsokat egy tesztkörnyezetben, mielőtt éles környezetben használná őket.

## <a name="next-steps"></a>További lépések
- Ha export és import használatával szeretne áttelepíteni egy PostgreSQL-adatbázist, olvassa el [a PostgreSQL-adatbázis áttelepítése exportálás sal és importálással című témakört.](howto-migrate-using-export-and-import.md)
- Az adatbázisok Azure Database for PostgreSQL szolgáltatásba való áttelepítéséről az [Adatbázis-áttelepítési útmutatóban](https://aka.ms/datamigration)talál további információt.
