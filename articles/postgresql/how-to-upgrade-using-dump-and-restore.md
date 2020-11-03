---
title: Frissítés a dump és a Restore-Azure Database for PostgreSQL-Single Server használatával
description: Néhány módszert ismertet az adatbázisok kiírásához és visszaállításához, hogy az áttelepíthető legyen egy magasabb verziójú Azure Database for PostgreSQL – egyetlen kiszolgálóra.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 26154f4501daba373f1f8b108f1ee7105b1b194f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93294285"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>A PostgreSQL-adatbázis frissítése a dump és a Restore használatával

Azure Database for PostgreSQL egyetlen kiszolgálón ajánlott a PostgreSQL-adatbázismotor frissítése egy magasabb szintű verzióra a következő módszerek egyikének használatával:
* Offline metódus a PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)használatával. Ebben a metódusban először a forrás-kiszolgálóról kell végrehajtani a memóriaképet, majd vissza kell állítania a memóriaképet a célkiszolgálón.
* Online metódus [**Database Migration Service**](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) (DMS) használatával. Ezzel a módszerrel a célként megadott adatbázis szinkronban van a forrással, és kiválaszthatja, hogy mikor kell kivágni. Vannak azonban bizonyos előfeltételek és korlátozások. 

Az alábbi javaslatot követve döntheti el, hogy az online és az offline metódusok között hogyan végezheti el a főbb verziófrissítést.

| **Adatbázis** | **Memóriakép/visszaállítás (offline)** | **DMS (online)** |
| ------ | :------: | :-----: |
| Van egy kis adatbázisa, amely leállást biztosít a frissítéshez  | X | |
| Kisméretű adatbázisok (< 10 GB)  | X | X | 
| Kis adathordozós adatbázisok (10 GB – 100 GB) | X | X |
| Nagyméretű adatbázisok (> 100 GB) |  | X |
| Leállást biztosíthat a verziófrissítéshez (az adatbázis méretétől függetlenül) | X |  |
| Megadhatja a DMS [előfeltételeit](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal#prerequisites) , beleértve az újraindítást is? |  | X |
| Elkerülheti a frissítési folyamat során a DDLs és a nem naplózott táblákat? | |  X |

Ez a útmutató két példát mutat be az adatbázisok PostgreSQL-pg_dump és pg_restore parancsok használatával történő frissítésére. A jelen dokumentumban szereplő folyamat **frissítésnek** minősül, bár a rendszer  **áttelepíti** az adatbázist a forráskiszolgálóról a célkiszolgálóra. 

> [!NOTE]
> A PostgreSQL-memóriakép és a visszaállítás többféleképpen is elvégezhető. Dönthet úgy, hogy a jelen dokumentumban leírt módon különböző módszereket használ. Ha például egy olyan memóriaképet szeretne elvégezni, amelyet a PostgreSQL-ügyfélről szeretne visszaállítani, tekintse meg a részletes eljárás részletes ismertetését és az ajánlott eljárásokat ismertető [dokumentumot](./howto-migrate-using-dump-and-restore.md) . További paraméterekkel rendelkező részletes memóriakép-és visszaállítási szintaxist a következő cikkekben talál: [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-postgresql"></a>A dump és a Restore Azure PostgreSQL-sel való használatának előfeltételei
 
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- 9,5, 9,6 vagy 10 rendszerű forrásadatbázis (Azure Database for PostgreSQL – egyetlen kiszolgáló)
- Célként megadott adatbázis-kiszolgáló a kívánt PostgreSQL főverzió [Azure Database for PostgreSQL-kiszolgálóval](quickstart-create-server-database-portal.md). 
- A PostgreSQL-mel telepített, [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) parancssori segédprogramokkal rendelkező ügyfélrendszer (Linux) telepítve van. 
- Azt is megteheti, hogy [Azure Cloud Shell](https://shell.azure.com) vagy a [Azure Portal](https://portal.azure.com)jobb felső sarkában lévő menüsorban található Azure Cloud Shellra kattint. `az login`A memóriakép és a visszaállítási parancsok futtatása előtt be kell jelentkeznie a fiókjába.
- A PostgreSQL-ügyfél helye, például egy virtuális gép, amely a forrás-és a célkiszolgáló azonos régiójában fut. 

## <a name="additional-details-and-considerations"></a>További részletek és szempontok
- A kapcsolódási karakterláncot a forrás-és a célként megadott adatbázisokhoz a portálon a "kapcsolódási karakterláncok" gombra kattintva érheti el. 
- Előfordulhat, hogy a kiszolgálón egynél több adatbázist futtat. Az adatbázisok listáját a forráskiszolgáló csatlakoztatásával és a futtatásával érheti el `\l` .
- Hozza létre a megfelelő adatbázisokat a cél adatbázis-kiszolgálón.
- Kihagyhatja a frissítés `azure_maintenance` vagy a sablon adatbázisait.
- A fenti táblázatokból megállapíthatja, hogy az adatbázis alkalmas-e az ilyen áttelepítési módra.
- Ha Azure Cloud Shellt szeretne használni, a munkamenet 20 perc elteltével időtúllépést tapasztal. Ha az adatbázis mérete < 10 GB, előfordulhat, hogy az időtúllépés nélkül befejezi a frissítést. Ellenkező esetben előfordulhat, hogy a munkamenetet más módon kell megnyitnia, például a kulcs megnyomása <Enter> 10-15 percen belül. 

> [!TIP] 
> - Ha a forráshoz és a céladatbázishez ugyanazt a jelszót használja, beállíthatja a `PGPASSWORD=yourPassword` környezeti változót.  Ezután nem kell megadnia a jelszót minden olyan parancs futtatásakor, mint például a psql, a pg_dump és a pg_restore.  Ehhez hasonlóan további változókat is beállíthat `PGUSER` , például `PGSSLMODE` a következőt: [PostgreSQL környezeti változók](https://www.postgresql.org/docs/11/libpq-envars.html).
>  
> - Ha a PostgreSQL-kiszolgáló TLS/SSL-kapcsolatokat igényel (alapértelmezés szerint Azure Database for PostgreSQL-kiszolgálókon), állítson be egy környezeti változót, `PGSSLMODE=require` hogy a pg_restore eszköz csatlakozzon a TLS-hez. A TLS nélkül a hiba is olvasható  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> - A Windows-parancssorban futtassa a parancsot a `SET PGSSLMODE=require` pg_restore parancs futtatása előtt. A Linux vagy a bash futtatása előtt futtassa a parancsot a `export PGSSLMODE=require` pg_restore parancs futtatása előtt.

## <a name="example-database-used-in-this-guide"></a>Példa az útmutatóban használt adatbázisra

 | **Leírás** | **Érték** |
 | ------- | ------- |
 | Forráskiszolgáló (v 9.5) | pg-95.postgres.database.azure.com |
 | Forrásadatbázis | bench5gb |
 | Forrásadatbázis mérete | 5 GB |
 | Forrás felhasználóneve | pg@pg-95 |
 | Célkiszolgáló (v11) | pg-11.postgres.database.azure.com |
 | Céladatbázis | bench5gb |
 | Cél felhasználóneve | pg@pg-11 |

## <a name="method-1-upgrade-with-streaming-backups-to-the-target"></a>1. módszer: frissítés a célhelyre irányuló folyamatos biztonsági mentéssel 

Ebben a metódusban a teljes adatbázis-memóriakép továbbítása közvetlenül a célként megadott adatbázis-kiszolgálóra történik, és nem tárolja a memóriaképet az ügyfélen. Ezért ezt a korlátozott tárterülettel rendelkező ügyféllel lehet használni, és a Azure Cloud Shell is futtathatók. 

1. Győződjön meg arról, hogy az adatbázis létezik a célkiszolgálón a `\l` parancs használatával. Ha az adatbázis nem létezik, hozza létre az adatbázist.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```bash
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Futtassa a memóriaképet, és állítsa vissza egyetlen parancssorból egy pipe használatával. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Például:

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Miután a verziófrissítési (áttelepítési) folyamat befejeződik, tesztelheti az alkalmazást a célkiszolgálóra. 
4. Ismételje meg a folyamatot a kiszolgálón található összes adatbázisra vonatkozóan.

 Az alábbi táblázat az ezzel a módszerrel történő verziófrissítés idejét mutatja be. Az adatok a [pgbench](https://www.postgresql.org/docs/10/pgbench.html)használatával lesznek feltöltve. Mivel az adatbázis különböző méretű objektumokkal rendelkezhet, amelyek mérete eltérő, mint a pgbench létrehozott táblák és indexek, erősen ajánlott tesztelni a memóriaképet és visszaállítani az adatbázist, hogy megértse az adatbázis frissítéséhez szükséges időt. 

| **Adatbázis mérete** | **Kb. szükséges idő** | 
| ----- | ------ |
| 1 GB  | 1-2 perc |
| 5 GB | 8-10 perc |
| 10 GB | 15-20 perc |
| 50 GB | 1 – 1,5 óra |
| 100 GB | 2,5 – 3 óra|
   
## <a name="method-2-upgrade-with-parallel-dump-and-restore"></a>2. módszer: verziófrissítés párhuzamos memóriaképtel és visszaállítással 

Ez a módszer akkor hasznos, ha kevés nagyobb táblázat van az adatbázisban, és szeretné integrálással az adott adatbázis memóriaképét és visszaállítási folyamatát. Elegendő helyi lemezes tárterületre van szüksége az adatbázisok biztonsági mentési memóriaképének befogadásához. Ez a párhuzamos memóriakép és visszaállítási folyamat csökkenti az idő felhasználását a teljes áttelepítés/frissítés befejezéséhez. Például a 50 GB-os pgbench-adatbázis, amely 1 – 1,5 órányi átállást vett igénybe, 30 percnél rövidebb idő alatt fejeződött be.

1. A forráskiszolgálón található minden adatbázishoz hozzon létre egy megfelelő adatbázist a célkiszolgálón.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Például:
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Futtassa az pg_dump parancsot egy olyan könyvtár-formátumban, amelyben a feladatok száma = 4 (a táblák száma az adatbázisban). Nagyobb számítási szinten és több táblázattal nagyobb számra növelheti. A pg_dump létre fog hozni egy könyvtárat, amely az egyes feladatokhoz tartozó tömörített fájlokat tárolja.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Például:
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Ezután állítsa vissza a biztonsági mentést a célkiszolgálón.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Például:
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> Az ebben a dokumentumban említett folyamat az előzetes verzióban elérhető Azure Database for PostgreSQL rugalmas kiszolgáló frissítésére is használható. A fő különbség a rugalmas kiszolgáló céljának a () nélküli kapcsolatok karakterlánca `@dbName` .  Ha például a Felhasználónév `pg` , akkor az egyetlen kiszolgáló felhasználónevét a kapcsolódási karakterláncban `pg@pg-95` , míg a rugalmas kiszolgálóval, egyszerűen használhatja `pg` .

## <a name="next-steps"></a>Következő lépések

- Ha elégedett a célként megadott adatbázis-függvénnyel, elhúzhatja a régi adatbázis-kiszolgálót. 
- Ha ugyanazt az adatbázis-végpontot szeretné használni, mint a forráskiszolgáló, akkor a régi forrás-adatbázis-kiszolgáló törlése után létrehozhat egy olvasási replikát a régi adatbázis-kiszolgáló nevével. Ha az állandó állapot létrejött, leállíthatja a replikát, amely a replika-kiszolgálót egy független kiszolgálónak fogja előléptetni. További részletekért lásd: [replikáció](./concepts-read-replicas.md) .
- Az éles környezetben való használat előtt ne felejtse el tesztelni és érvényesíteni ezeket a parancsokat tesztkörnyezetben.
