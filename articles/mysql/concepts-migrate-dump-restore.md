---
title: Memóriakép használata a MySQL-adatbázis áttelepítése, és állítsa vissza az Azure Database for MySQL-hez
description: Ez a cikk leírja a két leggyakoribb biztonsági mentése és visszaállítása-adatbázisok az Azure Database for MySQL, eszközök, például a mysqldump, a MySQL Workbench és a PHPMyAdmin használatával.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/02/2018
ms.openlocfilehash: f3e38bb3e7e4f2c58f1ae955878747ebc7d386f1
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984486"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>A MySQL-adatbázis migrálása az Azure Database for MySQL-hez memóriakép és visszaállítás használatával
Ez a cikk azt ismerteti, biztonsági mentése és visszaállítása-adatbázisok az Azure Database for MySQL-hez két gyakori módjai
- Memóriakép és visszaállítás a parancssorból (használatával az mysqldump) 
- Memóriakép és visszaállítás PHPMyAdmin használatával 

## <a name="before-you-begin"></a>Előkészületek
Ez az útmutató elvégezhető, szüksége lesz:
- [Az Azure-adatbázis létrehozása MySQL-kiszolgálóhoz - Azure-portálon](quickstart-create-mysql-server-database-using-azure-portal.md)
- [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) parancssori segédprogram olyan gépre telepíteni.
- A MySQL Workbench [MySQL Workbench letöltési](https://dev.mysql.com/downloads/workbench/), varangy, Navicat vagy más külső MySQL eszköz memóriakép és visszaállítás parancsokat.

## <a name="use-common-tools"></a>Általános eszközökkel
Közös segédprogramok és eszközök, például a MySQL Workbench, mysqldump, varangy vagy Navicat használatával távolról csatlakozhat, és állítsa vissza adatokat MySQL-hez készült Azure Database-be. Az ilyen eszközök használata az ügyfélgépen internetkapcsolattal rendelkező csatlakozni az Azure Database for MySQL-hez. Egy SSL-titkosítású kapcsolat használata ajánlott biztonsági eljárásokat, lásd a [SSL-összekapcsolhatóság konfigurálása az Azure Database for MySQL-hez](concepts-ssl-connection-security.md). Nem kell mozgatni a memóriaképeket különleges cloud bárhova áttelepítése az Azure Database for MySQL-hez. 

## <a name="common-uses-for-dump-and-restore"></a>Gyakori használati memóriakép és visszaállítás
A számos gyakori forgatókönyv például a mysqldump és a dump és a load adatbázisokhoz mysqlpump Azure MySQL-adatbázis MySQL segédprogramot használhatja. Más esetekben használhatja a [importálása és exportálása](concepts-migrate-import-export.md) megközelítést helyette.

- Adatbázis használata a teljes adatbázis áttelepítésekor listázása. Ez a javaslat MySQL adatok nagy mennyiségű áthelyezésekor, vagy ha élő webhelyek vagy alkalmazások számára a szolgáltatás megszakadásának minimalizálni szeretné tárolja. 
-  Győződjön meg arról, hogy az adatbázis minden táblájához InnoDB tárolási motort használják, amikor az adatok betöltését az Azure Database for MySQL-hez. Azure Database for MySQL csak tárolási InnoDB motor támogatja, és ezért nem támogatja a másodlagos tárolási motorokkal. Ha a táblák más tárolási motorral vannak konfigurálva, MySQL-hez készült Azure Database-ba való migrálás előtt InnoDB motor formátumba alakítsa át őket.
   Például a WordPress vagy a MyISAM-táblák használata WebApp rendelkezik, alakítani a táblák InnoDB-formátumba a MySQL-hez készült Azure-adatbázis visszaállítása előtt áttelepítése révén. Használja a záradékot `ENGINE=InnoDB` , hogy az új tábla létrehozásakor használt motor, majd az adatok átvitelét a visszaállítás előtti kompatibilis táblába. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Kompatibilitási problémák elkerülése érdekében győződjön meg arról, MySQL ugyanazt a verzióját használja a forrás és cél rendszereken, adatbázisok kiírásakor. Például ha a meglévő MySQL-kiszolgáló 5.7-es verzióra, majd meg kell migrálása az Azure Database for MySQL 5.7-es verzió futtatására konfigurált. A `mysql_upgrade` parancsot egy Azure Database for MySQL-kiszolgáló nem működik, és nem támogatott. MySQL verziója között frissíteni szeretne, ha először dump, vagy exportálása az alacsonyabb verziójú adatbázis MySQL újabb verziójával a saját környezetében. Ezután futtassa `mysql_upgrade`, mielőtt megpróbálná áttelepítését az Azure-adatbázis MySQL-hez.

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

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>A biztonságimásolat-fájl létrehozása a parancssorból mysqldump használatával
Egy meglévő MySQL-adatbázis biztonsági mentése a helyi kiszolgálón vagy egy virtuális gépen, futtassa a következő parancsot: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Adja meg a paraméterek a következők:
- [uname] Az adatbázis felhasználónevének 
- [pass] A database jelszavát (Megjegyzés: nincs szabad hely között – p és a jelszó) 
- [adatbázisnév] Az adatbázis neve 
- [backupfile.sql] a fájlnevét, az adatbázis biztonsági mentése 
- [--opt] A mysqldump lehetőséget 

Például egy adatbázis biztonsági mentésekor a MySQL-kiszolgáló és a egy fájl testdb_backup.sql jelszó nélküli "tesztfelhasználó néven", amelynek "testdb" nevű, használja a következő parancsot. A parancs biztonsági másolatot készít a `testdb` adatbázis nevű fájlba `testdb_backup.sql`, amely tartalmazza az adatbázis újbóli létrehozásához szükséges összes SQL-utasításokkal. 

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

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Hozzon létre egy adatbázist a cél Azure Database for MySQL-kiszolgáló
Hozzon létre egy üres adatbázist a cél Azure Database for MySQL-kiszolgáló, amelyre az adatok áttelepítéséhez. Az adatbázis létrehozásához használja a megfelelő eszköz, például a MySQL Workbench, varangy vagy Navicat. Az adatbázis rendelkezhet ugyanazzal a névvel, az adatbázis, amely a változásképek adatokat tartalmazott, vagy létrehozhat egy adatbázist egy másik névvel.

A csatlakozás, keresse meg a kapcsolati információkat a **áttekintése** , az Azure Database for MySQL-hez.

![Keresse meg a kapcsolati adatokat az Azure Portalon](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Adja hozzá a kapcsolati adatokat, a MySQL Workbench segítségével.

![A MySQL Workbench kapcsolati karakterlánc](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>A MySQL-adatbázishoz parancssori vagy a MySQL Workbench visszaállítása
Miután létrehozta a céladatbázis, a mysql parancsot vagy a MySQL Workbench használatával állítsa vissza az adatokat az újonnan létrehozott adatbázisra a memóriakép-fájl az adott be.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Ebben a példában a cél Azure Database for MySQL-kiszolgáló az újonnan létrehozott adatbázisba állítsa vissza az adatokat.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportálás PHPMyAdmin használatával
Szeretné exportálni, előfordulhat, hogy a már telepített helyi környezete általános eszköz phpMyAdmin használhatja. A MySQL-adatbázis PHPMyAdmin használatával exportálása:
1. Nyissa meg a phpMyAdmin.
2. Válassza ki az adatbázist. Kattintson a bal oldali listában található az adatbázis nevét. 
3. Kattintson a **exportálása** hivatkozásra. Az adatbázis-memóriakép megtekintéséhez megjelenik egy új lap.
4. Az exportálási területen kattintson a **válassza ki az összes** hivatkozás kiválasztása a táblák az adatbázisban. 
5. Az SQL-beállítások területen kattintson a megfelelő beállításokat. 
6. Kattintson a **mentése fájlként** lehetőséget, és a megfelelő tömörítés lehetőséget, majd kattintson a **lépjen** gombra. Mentse helyileg a fájlt, és felszólítja kell megjelenik egy párbeszédpanel.

## <a name="import-using-phpmyadmin"></a>Importálás PHPMyAdmin használatával
Az adatbázis importálása a hasonló exportálása. A következő műveleteket hajthatja végre:
1. Nyissa meg a phpMyAdmin. 
2. A phpMyAdmin telepítőjében kattintson **Hozzáadás** hozzáadása az Azure Database for MySQL-kiszolgálóhoz. Adja meg a kapcsolat adatait, és a bejelentkezési adatait.
3. Hozzon létre egy megfelelő nevű lapfüleken adatbázist, és jelölje ki a képernyő a bal oldalon. A meglévő adatbázis újraírási, kattintson az adatbázis nevét, jelölje be a tábla neve melletti jelölőnégyzeteket, majd válassza **Drop** törli a meglévő táblákat. 
4. Kattintson a **SQL** hivatkozásra kattintva megjelenítheti az oldal, ahol az SQL-parancsokat beírhatja, vagy az SQL-fájl feltöltése. 
5. Használja a **Tallózás** gombra kattintva keresse meg a következő adatbázisfájl. 
6. Kattintson a **Go** gombra a biztonsági másolat exportálása, hajtsa végre az SQL-parancsokat, és hozza létre újból az adatbázist.

## <a name="next-steps"></a>További lépések
- [Alkalmazások az Azure Database for MySQL-hez csatlakozzon](./howto-connection-string.md).
- További információ az adatbázisok az Azure Database for MySQL-hez, lásd: a [adatbázis-Migrálási útmutató](https://aka.ms/datamigration).
