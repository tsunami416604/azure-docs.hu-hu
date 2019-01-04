---
title: Memóriakép MariaDB adatbázis migrálása, és állítsa vissza az Azure Database for MariaDB
description: Ez a cikk leírja a két leggyakoribb biztonsági mentése és visszaállítása az adatbázisok az Azure Database for MariaDB, eszközök, például a mysqldump, a MySQL Workbench és a PHPMyAdmin használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: bcb76fcbba02bf53b48cc462e3dad8f264db02ed
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540443"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>A MariaDB-adatbázis migrálása az Azure Database for MariaDB memóriakép és visszaállítás használatával
Ez a cikk azt ismerteti, biztonsági mentése és visszaállítása az Azure Database-adatbázisok a MariaDB-hez két gyakori módjai
- Memóriakép és visszaállítás a parancssorból (használatával az mysqldump) 
- Memóriakép és visszaállítás PHPMyAdmin használatával

## <a name="before-you-begin"></a>Előkészületek
Ez az útmutató elvégezhető, szüksége lesz:
- [Hozzon létre az Azure Database for MariaDB-kiszolgáló – az Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) parancssori segédprogram olyan gépre telepíteni.
- A MySQL Workbench [MySQL Workbench letöltési](https://dev.mysql.com/downloads/workbench/), varangy, Navicat vagy más külső MySQL eszköz memóriakép és visszaállítás parancsokat.

## <a name="use-common-tools"></a>Általános eszközökkel
Közös segédprogramok és eszközök, például a MySQL Workbench, mysqldump, varangy vagy Navicat használatával távolról csatlakozhat, és állítsa vissza adatokat MariaDB-hez készült Azure Database-be. Az ilyen eszközök használata az ügyfélgépen internet-kapcsolattal csatlakozni az Azure Database for MariaDB. Egy SSL-titkosítású kapcsolat használata ajánlott biztonsági eljárásokat, lásd a [SSL-összekapcsolhatóság konfigurálása az Azure Database for MariaDB](concepts-ssl-connection-security.md). Nem kell semmilyen különleges cloud hely helyezze át a memóriaképeket MariaDB-hez készült Azure Database-migráció során. 

## <a name="common-uses-for-dump-and-restore"></a>Gyakori használati memóriakép és visszaállítás
Számos gyakori forgatókönyv MariaDB Server egy Azure Database-be például a mysqldump és a dump és a load adatbázisokhoz mysqlpump MySQL segédprogramokat használhatja. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Adatbázis használata a teljes adatbázis áttelepítésekor listázása. Ez a javaslat tárolja, ha nagy mennyiségű adat áthelyezését, illetve ha élő webhelyek vagy alkalmazások számára a szolgáltatás megszakadásának minimalizálni szeretné. 
-  Győződjön meg arról, hogy az adatbázis minden táblájához InnoDB tárolási motort használják, amikor az adatok betöltését az Azure Database for MariaDB. Azure Database for MariaDB csak tárolási InnoDB motor támogatja, és ezért nem támogatja a másodlagos tárolási motorokkal. Ha a táblák megtörténik az egyéb tároló-motorral, konvertálja a InnoDB motor formátumra MariaDB-hez készült Azure Database-ba való migrálás előtt.
   Például ha egy WordPress vagy a MyISAM-táblák használata WebApp, alakítani a táblák áttelepítésével InnoDB formátumra MariaDB-hez készült Azure-adatbázis visszaállítása előtt. Használja a záradékot `ENGINE=InnoDB` , hogy az új tábla létrehozásakor használt motor, majd az adatok átvitelét a visszaállítás előtti kompatibilis táblába. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Kompatibilitási problémák elkerülése érdekében győződjön meg arról, a forrás és cél rendszereken használt MariaDB ugyanazt a verzióját, adatbázisok kiírásakor. Például ha a meglévő MariaDB-kiszolgáló verziója 10.2, majd meg kell migrálása az Azure Database verzió 10.2 futtatásra konfigurált MariaDB-hez készült. A `mysql_upgrade` parancsot egy Azure Database for MariaDB-kiszolgáló nem működik, és nem támogatott. MariaDB verziói közötti frissítés van szüksége, ha először dump, vagy az alacsonyabb verziójú adatbázis exportálása MariaDB újabb verziójával a saját környezetében. Ezután futtassa `mysql_upgrade`, MariaDB-hez készült Azure-adatbázishoz való migrálás megkísérlése előtt.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások
Teljesítmény optimalizálása érdekében figyelje a felsorolt szempontok nagy méretű adatbázisokhoz kiírásakor:
-   Használja a `exclude-triggers` lehetőség a mysqldump adatbázisok kiírásakor. Zárja ki az eseményindítók a memóriaképeket elkerülése érdekében a trigger parancsokat, ez az adat-visszaállítás során. 
-   Használja a `single-transaction` , a tranzakció elkülönítési mód beállítása legyen REPEATABLE READ lehetőséget, majd indítsa el a TRANZAKCIÓ SQL-utasítás küld a kiszolgáló előtt vonatkozó adatok. Sok tábla egy tranzakción belül való kiírása hatására a visszaállítás során felhasznált néhány extra tárterület. A `single-transaction` lehetőséget, és a `lock-tables` lehetőség, egymást kölcsönösen kizáró, mert ZÁROLÁSI táblák okoz minden függőben lévő tranzakciók hajtható végre implicit módon. Nagy táblák kiírása, kombinálhatja a `single-transaction` a beállítást a `quick` lehetőséget. 
-   Használja a `extended-insert` több értéklisták tartalmazó több sorból szintaxist. Ez egy kisebb memóriakép-fájl eredményez, és felgyorsítja a beszúrások, ha a fájl újbóli.
-  Használja a `order-by-primary` mysqldump adatbázisok kiírásakor, így az adatok a parancsfájlalapú elsődleges kulcs ahhoz szereplő beállítást.
-   Használja a `disable-keys` mysqldump vonatkozó adatokat, ha a beállítás letiltja a betöltés előtt külső kulcsra vonatkozó megkötések. Idegen kulcs ellenőrzések letiltása biztosít teljesítménynövekedést. A korlátozások engedélyezése és a terhelés annak biztosítása érdekében a hivatkozási integritás után ellenőrizze az adatokat.
-   Használja a particionált táblákat, ha szükséges.
-   Párhuzamos adatokat betölteni. Ne használjon túl sok párhuzamos végrehajtás, amely miatt a eléri a erőforrás korlátot, és figyelheti erőforrásait az Azure Portalon elérhető mérőszámok az. 
-   Használja a `defer-table-indexes` lehetőség mysqlpump adatbázisok kiírásakor, úgy, hogy az index létrehozása táblák adatok betöltése után történik.
-   Másolja a biztonságimásolat-fájlokat egy Azure blob/tároló és a visszaállítás végrehajtása itt, ami sokkal gyorsabb, mint a visszaállítás végrehajtása az interneten keresztül kell lennie.

## <a name="create-a-backup-file"></a>Hozzon létre egy biztonsági mentési fájlt
Egy meglévő MariaDB-adatbázis biztonsági mentése a helyi kiszolgálón vagy egy virtuális gépen, futtassa a következő parancsot a mysqldump használatával: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Adja meg a paraméterek a következők:
- [uname] Az adatbázis felhasználónevének 
- [pass] A database jelszavát (Megjegyzés: nincs szabad hely között – p és a jelszó) 
- [adatbázisnév] Az adatbázis neve 
- [backupfile.sql] a fájlnevét, az adatbázis biztonsági mentése 
- [--opt] A mysqldump lehetőséget 

Például egy adatbázis biztonsági mentésekor a MariaDB kiszolgálón "testuser" felhasználónévvel, és a egy fájl testdb_backup.sql jelszó nélküli "testdb" nevű, használja a következő parancsot. A parancs biztonsági másolatot készít a `testdb` adatbázis nevű fájlba `testdb_backup.sql`, amely tartalmazza az adatbázis újbóli létrehozásához szükséges összes SQL-utasításokkal. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Válassza ki az adott táblák az adatbázis biztonsági mentése, listázza a táblanevek, szóközzel elválasztva. Például biztonsági mentése csak table1 és table2 táblák "testdb", kövesse az ebben a példában: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Adatbázis biztonsági mentése több egyszerre, használja a--adatbázis váltson, és az adatbázis neve szóközzel elválasztva sorolja. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Minden az adatbázisok biztonsági mentése a kiszolgálón egy időben, célszerű használni az--all-adatbázisok beállítást.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Adatbázis létrehozása a célkiszolgálón
Hozzon létre egy üres adatbázist a cél Azure Database for MariaDB-kiszolgáló, amelyre az adatok áttelepítéséhez. Az adatbázis létrehozásához használja a megfelelő eszköz, például a MySQL Workbench, varangy vagy Navicat. Az adatbázis rendelkezhet ugyanazzal a névvel, az adatbázis, amely a változásképek adatokat tartalmazott, vagy létrehozhat egy adatbázist egy másik névvel.

A csatlakozás, keresse meg a kapcsolati információkat a **áttekintése** , az Azure Database for MariaDB.

![Keresse meg a kapcsolati adatokat az Azure Portalon](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Adja hozzá a kapcsolati adatokat, a MySQL Workbench segítségével.

![A MySQL Workbench kapcsolati karakterlánc](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>A MariaDB-adatbázis visszaállítása
Miután létrehozta a céladatbázis, a mysql parancsot vagy a MySQL Workbench használatával állítsa vissza az adatokat az újonnan létrehozott adatbázisra a memóriakép-fájl az adott be.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Ebben a példában a cél Azure Database for MariaDB-kiszolgáló az újonnan létrehozott adatbázisba állítsa vissza az adatokat.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportálás PHPMyAdmin használatával
Szeretné exportálni, előfordulhat, hogy a már telepített helyi környezete általános eszköz phpMyAdmin használhatja. A PHPMyAdmin használatával MariaDB-adatbázis exportálása:
1. Nyissa meg a phpMyAdmin.
2. Válassza ki az adatbázist. Kattintson a bal oldali listában található az adatbázis nevét. 
3. Kattintson a **exportálása** hivatkozásra. Az adatbázis-memóriakép megtekintéséhez megjelenik egy új lap.
4. Az exportálási területen kattintson a **válassza ki az összes** hivatkozás kiválasztása a táblák az adatbázisban. 
5. Az SQL-beállítások területen kattintson a megfelelő beállításokat. 
6. Kattintson a **mentése fájlként** lehetőséget, és a megfelelő tömörítés lehetőséget, majd kattintson a **lépjen** gombra. Mentse helyileg a fájlt, és felszólítja kell megjelenik egy párbeszédpanel.

## <a name="import-using-phpmyadmin"></a>Importálás PHPMyAdmin használatával
Az adatbázis importálása a hasonló exportálása. A következő műveleteket hajthatja végre:
1. Nyissa meg a phpMyAdmin. 
2. A phpMyAdmin telepítőjében kattintson **Hozzáadás** hozzáadása az Azure Database for MariaDB-kiszolgáló. Adja meg a kapcsolat adatait, és a bejelentkezési adatait.
3. Hozzon létre egy megfelelő nevű lapfüleken adatbázist, és jelölje ki a képernyő a bal oldalon. A meglévő adatbázis újraírási, kattintson az adatbázis nevét, jelölje be a tábla neve melletti jelölőnégyzeteket, majd válassza **Drop** törli a meglévő táblákat. 
4. Kattintson a **SQL** hivatkozásra kattintva megjelenítheti az oldal, ahol az SQL-parancsokat beírhatja, vagy az SQL-fájl feltöltése. 
5. Használja a **Tallózás** gombra kattintva keresse meg a következő adatbázisfájl. 
6. Kattintson a **Go** gombra a biztonsági másolat exportálása, hajtsa végre az SQL-parancsokat, és hozza létre újból az adatbázist.

## <a name="next-steps"></a>További lépések
- [Alkalmazások az Azure Database for MariaDB csatlakoztatása](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
