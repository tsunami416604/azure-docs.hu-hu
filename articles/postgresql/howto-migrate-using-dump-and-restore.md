---
title: Kiírás és visszaállítás Azure Database for PostgreSQL – egyetlen kiszolgálón
description: Ismerteti, hogyan lehet kibontani a PostgreSQL-adatbázist egy memóriaképfájl-fájlba, és hogyan kell visszaállítani a pg_dump által létrehozott fájlból a Azure Database for PostgreSQL-Single Serverben.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 55e802aa1f7bdf0d67d1a9c3f020d255afdc8130
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261909"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>A PostgreSQL-adatbázis migrálása a dump és a Restore használatával
A [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) használatával kinyerheti a PostgreSQL-adatbázist egy memóriaképfájl-fájlba, és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) visszaállíthatja a PostgreSQL-adatbázist egy pg_dump által létrehozott archív fájlból.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md) , amely tűzfalszabályok használatával engedélyezi a hozzáférést és az adatbázist.
- telepített [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) parancssori segédprogramok

A PostgreSQL-adatbázis kiírásához és visszaállításához kövesse az alábbi lépéseket:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Memóriaképfájl létrehozása a betölteni kívánt pg_dump tartalmazó memóriakép használatával
Ha egy meglévő PostgreSQL-adatbázist szeretne biztonsági másolatot készíteni a helyszínen vagy egy virtuális gépen, futtassa a következő parancsot:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Ha például van egy helyi kiszolgálója és egy **testdb** nevű adatbázis
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pg_restore"></a>Az adatvisszaállítás a cél Azure-adatbázisba PostrgeSQL a pg_restore használatával
A céladatbázis létrehozása után a pg_restore parancs és a-d,--dbname paraméter használatával visszaállíthatja az adatait a célként megadott adatbázisba a memóriakép fájlból.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
A--No-Owner paraméterrel együtt a visszaállítás során létrehozott összes objektum tulajdonosa lesz a--username értékkel megadott felhasználó tulajdonában. További információkért tekintse meg a [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html)hivatalos PostgreSQL-dokumentációját.

> [!NOTE]
> Ha a PostgreSQL-kiszolgáló SSL-kapcsolatokat igényel (alapértelmezés szerint Azure Database for PostgreSQL-kiszolgálókon), állítson `PGSSLMODE=require` be egy környezeti változót, hogy a pg_restore eszköz csatlakozzon az SSL-hez. SSL nélkül előfordulhat, hogy a hiba olvasható`FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> A Windows-parancssorban futtassa a parancsot `SET PGSSLMODE=require` a pg_restore parancs futtatása előtt. A Linux vagy a bash futtatása előtt `export PGSSLMODE=require` futtassa a parancsot a pg_restore parancs futtatása előtt.
>

Ebben a példában a **testdb. dump** fájlból származó adatok visszaállítását a célkiszolgáló **mydemoserver.postgres.database.Azure.com**adatbázis- **mypgsqldb** . 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Az áttelepítési folyamat optimalizálása

A meglévő PostgreSQL-adatbázis Azure Database for PostgreSQL szolgáltatásba való áttelepítése az egyik módszer, ha biztonsági másolatot készít az adatbázisról a forrásról, és visszaállítja azt az Azure-ban. Az áttelepítés befejezéséhez szükséges idő minimalizálásához érdemes a következő paramétereket használni a biztonsági mentési és visszaállítási parancsokkal.

> [!NOTE]
> A szintaxis részletes ismertetését lásd: [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>A biztonsági mentéshez
- A biztonsági mentést a-FC kapcsolóval hajtsa végre, így a visszaállítás párhuzamosan is elvégezhető. Példa:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName -f Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>A visszaállításhoz
- Javasoljuk, hogy helyezze át a biztonságimásolat-fájlt egy olyan Azure-beli virtuális gépre, amelyben az a Azure Database for PostgreSQL-kiszolgáló, amelyre az áttelepítést végzi, és az adott virtuális gép pg_restore a hálózati késés csökkentése érdekében. Javasoljuk továbbá, hogy a virtuális gép a [gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-powershell.md) engedélyezésével legyen létrehozva.

- Alapértelmezés szerint már el kell végeznie, de meg kell nyitnia a memóriaképet annak ellenőrzéséhez, hogy a Create index utasítások az adatokat szúrják-e be. Ha nem ez a helyzet, helyezze át a Create index utasítást az adatbeszúrás után.

- Restore with the switchs-FC és *#* -j kapcsolóval integrálással a visszaállítást. *#* a célkiszolgálón lévő magok száma. Azt is megteheti *#* , hogy a beállítás megadásával kétszer is megpróbálkozik a célkiszolgáló magok számával, hogy a hatás megjelenjen. Példa:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- A memóriaképet úgy is szerkesztheti, ha hozzáadja a *synchronous_commit = off;* parancsot az elején, és a *synchronous_commit = on; paranccsal beállítja* a végpontot. Ha nem kapcsolja be a végére, az alkalmazások megváltoznak az adatvesztés következtében.

- A cél Azure Database for PostgreSQL kiszolgálón a visszaállítás előtt vegye figyelembe a következőket:
    - A lekérdezési teljesítmény nyomon követésének kikapcsolása, mivel ezek a statisztikák nem szükségesek az áttelepítés során. Ezt úgy teheti meg, hogy a pg_stat_statements. Track, a pg_qs. query_capture_mode és a pgms_wait_sampling. query_capture_mode beállítást a NONE értékre állítja.

    - A Migrálás felgyorsításához használjon nagy számítási és magas memória-SKU-t, például 32 virtuális mag memóriát. A visszaállítás befejezése után egyszerűen méretezheti vissza az előnyben részesített SKU-ra. Minél nagyobb a SKU, annál több párhuzamosságot érhet el a pg_restore parancs megfelelő `-j` paraméterének növelésével. 

    - A célkiszolgáló további IOPS javíthatják a visszaállítási teljesítményt. A kiszolgáló tárolási méretének növelésével több IOPS is kiépítheti. Ez a beállítás nem vonható vissza, azonban érdemes megfontolni, hogy egy magasabb IOPS a jövőben is hasznát veheti-e a tényleges munkaterhelésnek.

Az éles környezetben való használat előtt ne felejtse el tesztelni és érvényesíteni ezeket a parancsokat tesztkörnyezetben.

## <a name="next-steps"></a>További lépések
- A PostgreSQL-adatbázis exportálással és importálással történő áttelepítésével kapcsolatban lásd: [a PostgreSQL-adatbázis migrálása az Exportálás és az importálás használatával](howto-migrate-using-export-and-import.md).
- Az adatbázisok Azure Database for PostgreSQLre való áttelepítésével kapcsolatos további információkért tekintse meg az [adatbázis-áttelepítési útmutatót](https://aka.ms/datamigration).
