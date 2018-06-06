---
title: Telepítse át a MySQL-adatbázis biztonsági másolat használatával, és MySQL az Azure-adatbázis visszaállítása
description: Ez a cikk ismerteti a biztonsági mentése és visszaállítása az Azure Database-adatbázisoknál a MySQL mysqldump, MySQL munkaterület és PHPMyAdmin két gyakori módjai.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/02/2018
ms.openlocfilehash: 9d9b9b5eda110ad7fa66bb99501936dda5d45e52
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737045"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>A MySQL-adatbázis áttelepítése az Azure Database a MySQL használata a biztonsági másolat és helyreállítás
Ez a cikk ismerteti a biztonsági mentése és adatbázisokat az Azure-adatbázis visszaállítása a MySQL két gyakori módjai
- Biztonsági másolat és helyreállítás parancsot a parancssorból (használatával az mysqldump) 
- Memóriakép és visszaállítás PHPMyAdmin segítségével 

## <a name="before-you-begin"></a>Előkészületek
Ez az útmutató Útmutató lépéseit, meg kell rendelkeznie:
- [Azure-adatbázis létrehozása a MySQL-kiszolgáló - Azure-portálon](quickstart-create-mysql-server-database-using-azure-portal.md)
- [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) parancssori segédprogram a gépre telepítve.
- MySQL-munkaterület [MySQL munkaterület letöltése](https://dev.mysql.com/downloads/workbench/), varangy, Navicat vagy más külső MySQL eszköz dump és visszaállításra vonatkozó parancsot kap.

## <a name="use-common-tools"></a>Általános eszközök használata
Közös segédprogramok és eszközöket, például a MySQL-munkaterületet, mysqldump, varangy vagy Navicat segítségével távolról csatlakozzon, és állítsa vissza adatokat a MySQL az Azure-adatbázisba. Az ilyen eszközök használatát az internet-kapcsolat ügyfélgépre MySQL az Azure-adatbázishoz való kapcsolódáshoz. Ajánlott biztonsági eljárások az SSL-titkosítású kapcsolat használata, lásd a [MySQL az Azure-adatbázis konfigurálása az SSL-kapcsolat](concepts-ssl-connection-security.md). Nem kell a memóriaképek áthelyezése semmilyen különleges felhő helyre, MySQL az Azure-adatbázis áttelepítésekor. 

## <a name="common-uses-for-dump-and-restore"></a>Gyakori használati mód a biztonsági másolat és helyreállítás
Néhány gyakori helyzetek MySQL segédprogramot mysqldump és mysqlpump és a memóriakép adatbázisokhoz például egy Azure-beli MySQL Database lehet használni. Más esetekben előfordulhat, hogy használja a [importálására és exportálására](concepts-migrate-import-export.md) közelítik meg helyette.

- Adatbázis használata a teljes adatbázis áttelepítésekor listázása. Ez a javaslat MySQL adatok nagy mennyiségű áthelyezésekor, vagy ha azt szeretné, minimálisra csökkentése érdekében a szolgáltatás megszakadásának élő webhelyek vagy alkalmazások rendelkezik. 
-  Győződjön meg arról, hogy az összes tábla az adatbázisban a InnoDB tárolási összetevőt használja, ha adatok betöltése az Azure-adatbázisba a MySQL. Azure MySQL-adatbázis csak InnoDB tárolóprogramot támogatja, és ezért nem támogatja a másodlagos tárolási motorok. Ha a táblák egyéb tárolási motorok vannak beállítva, a MySQL az Azure Database-áttelepítés előtt InnoDB motor formátumba alakítsa át azokat.
   Például ha egy WordPress vagy webalkalmazást a MyISAM táblák használata, először alakítsa át azokat a táblákat MySQL az Azure-adatbázis visszaállítása előtt InnoDB formátumra alakítja át. Használja a záradékot `ENGINE=InnoDB` , hogy az új tábla létrehozásakor használt motor, majd az adatok átvitelét a visszaállítás előtti kompatibilis táblába. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Kompatibilitási problémák elkerülése érdekében győződjön meg arról, MySQL ugyanazon verzióját szolgál a forrás és cél rendszereken való kiírása az adatbázisokat. Például ha a meglévő MySQL-kiszolgáló verziója 5.7-es verzióját, majd át kell telepítenie az Azure Database a MySQL verzió 5.7 futtatásra konfigurált. A `mysql_upgrade` parancs egy Azure-adatbázis MySQL-kiszolgáló nem működik, és nem támogatott. Ha MySQL verziói közötti frissítés van szüksége, először dump, vagy a saját környezetében az MySQL egy újabb verziójával a alacsonyabb verziójú adatbázis exportálása. Ezután futtassa `mysql_upgrade`, MySQL az Azure-adatbázisba történő áttelepítésének megkezdése előtt.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások
A teljesítmény optimalizálása érdekében elvégzendő nagy adatbázisok vonatkozó figyelmeztetés ezeket a szempontokat:
-   Használja a `exclude-triggers` mysqldump, ha az adatbázisok vonatkozó beállítást. Eseményindítók kizárása memóriaképek az eseményindító parancsokat, az adatok visszaállítás során kiváltó elkerülése érdekében. 
-   Használja a `single-transaction` a tranzakció elkülönítési mód beállításához REPEATABLE READ lehetőséget, majd indítsa el a TRANZAKCIÓ SQL-utasítás által a kiszolgálónak küldött adatok való kiírása előtt. Visszaállítás során fel néhány megnövelt tárhely való kiírása sok tábla egy tranzakción belül okoz. A `single-transaction` beállítás és a `lock-tables` beállítás, egymást kölcsönösen kizáró, mert ZÁROLÁSI táblák hajtható végre implicit tranzakciók függőben. Nagy táblák listázása, ötvözze a `single-transaction` a beállítást a `quick` lehetőséget. 
-   Használja a `extended-insert` több érték listák tartalmazó több soron kívüli szintaxist. Ez egy kisebb memóriakép eredményez, és felgyorsítja a beszúrások, ha a fájl be van töltve.
-  Használja a `order-by-primary` mysqldump, hogy az adatok parancsprogrammal létrehozva, elsődleges kulcs sorrendben való kiírása a adatbázisok, ha a beállítást.
-   Használja a `disable-keys` letiltásának lehetőségét az mysqldump vonatkozó adatokat, ha a külső kulcsra vonatkozó megkötések betöltése előtt. Idegen kulcs ellenőrzések letiltása biztosít teljesítménynövekedést érhet el. A megkötések engedélyezéséhez és a hivatkozási integritás biztosított betöltése után ellenőrizze az adatokat.
-   Használja a particionált táblákat, amikor szükséges.
-   Az adatok párhuzamos betöltése. Ne használjon túl sok párhuzamos okozza, hogy egy erőforrás korlátot elérte volna, és megfigyelje az erőforrásokat az Azure portálon elérhető metrikák használatával. 
-   Használja a `defer-table-indexes` mysqlpump való kiírása adatbázisok, úgy, hogy az index létrehozása táblák adatok betöltése után történik, ha a beállítást.
-   Egy Azure blob tárolóba másolja a biztonságimásolat-fájlt, és a visszaállításhoz onnan, amelyek sokkal gyorsabb, mint a visszaállítási folyamat végrehajtása az interneten keresztül kell lennie.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>A biztonsági másolat létrehozása a parancssorból mysqldump használatával
Meglévő MySQL-adatbázis mentésére, a helyi helyszíni kiszolgálón vagy egy virtuális gépen, a következő parancsot: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Adja meg a paraméterek a következők:
- [uname] A felhasználónevet 
- [fázis] A jelszót az adatbázis (Megjegyzés: a -p és a jelszó között nincs hely) 
- [dbname] Az adatbázis neve 
- [backupfile.sql] az adatbázis biztonsági másolatának fájlnév 
- [--opt] A mysqldump beállítás 

Például biztonsági mentése a MySQL-kiszolgáló a tesztfelhasználó "néven" felhasználónévvel és egy fájl testdb_backup.sql jelszó nélküli "testdb" nevű adatbázis, a következő paranccsal. A parancs biztonsági másolatot készít a `testdb` adatbázis nevű fájlba `testdb_backup.sql`, amely tartalmazza az adatbázis újbóli létrehozásához szükséges összes SQL-utasítások. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Jelölje be a megadott táblák az adatbázis biztonsági mentése, a táblanevek, szóközökkel elválasztott felsorolása Például a "testdb" biztonsági mentés csak table1 és table2 táblák, kövesse az ebben a példában: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Adatbázis biztonsági mentése több egyszerre, használja a--adatbázis váltson, és az adatbázis nevének szóközökkel elválasztott listában. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Minden az adatbázisok biztonsági mentése a kiszolgálón egy időben, ajánlott a--minden-adatbázisok lehetőséget.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>A cél Azure-adatbázis kiszolgáló MySQL-adatbázis létrehozása
Üres adatbázist létrehozni a cél Azure-adatbázis MySQL kiszolgáló, ahová az adatok áttelepítéséhez. Például a MySQL-munkaterületet, varangy vagy Navicat eszköz segítségével hozza létre az adatbázist. Az adatbázis lehet-e azonos nevük az adatbázis, amely a dömpingelt adatokat tartalmazott, vagy létrehozhat egy adatbázist egy eltérő nevű.

A csatlakozás, keresse meg a kapcsolati információit a **áttekintése** a MySQL az Azure-adatbázis.

![A kapcsolati adatok az Azure portálon található](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Adja hozzá az azokat a MySQL munkaterület-kapcsolódási információt.

![MySQL-munkaterület kapcsolati karakterlánc](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Állítsa vissza a MySQL-adatbázis használatával parancssori vagy MySQL munkaterület
Miután létrehozta a céladatbázis, a mysql-utasítás vagy -MySQL munkaterület használatával állítsa vissza az adatokat az egyes újonnan létrehozott adatbázis a biztonsági másolat fájlból történő.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Ebben a példában az adatok helyreállítását a cél Azure-adatbázis MySQL-kiszolgáló az újonnan létrehozott adatbázisba.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportálás PHPMyAdmin használatával
Exportálja, előfordulhat, hogy a már telepített helyileg a környezetben közös eszköz phpMyAdmin is használhat. A MySQL-adatbázis használatával PHPMyAdmin exportálása:
1. Nyissa meg a phpMyAdmin.
2. Válassza ki az adatbázist. Kattintson a bal oldali listában az adatbázis nevére. 
3. Kattintson a **exportálása** hivatkozásra. Egy új lap jelenik meg az adatbázis-memóriakép megtekintéséhez.
4. Az Exportálás területen kattintson a **kijelölése az összes** adja meg a táblák az adatbázisban mutató hivatkozást. 
5. Az SQL-beállítások területen kattintson a megfelelő beállításokat. 
6. Kattintson a **mentése fájlként** beállítás és a megfelelő tömörítés lehetőséget, majd kattintson a **nyissa meg** gombra. Egy párbeszédpanel jelenik meg, és mentse helyileg a fájlt felszólítja.

## <a name="import-using-phpmyadmin"></a>Importálás PHPMyAdmin
Az adatbázis importálása hasonlít exportálása. A következő műveleteket hajthatja végre:
1. Nyissa meg a phpMyAdmin. 
2. A phpMyAdmin beállítása lapon kattintson a **Hozzáadás** hozzáadása a MySQL-kiszolgálóhoz tartozó Azure-adatbázis. Adja meg a kapcsolati adatokat és a bejelentkezési adatok.
3. Hozzon létre egy megfelelő nevű, és válassza ki azt a képernyő bal oldalon található. Írja át a létező adatbázist, kattintson az adatbázis nevét, jelölje be a tábla neve melletti jelölőnégyzetet, és válassza **Drop** törli a meglévő táblákat. 
4. Kattintson a **SQL** hivatkozásra kattintva megjelenítheti a lap, ahol az SQL-parancsokat írhat, vagy az SQL-fájl feltöltése. 
5. Használja a **Tallózás** található az adatbázis gombra. 
6. Kattintson a **Ugrás** gombra a biztonsági mentés exportálása, az SQL-parancsok, és hozza létre újból az adatbázist.

## <a name="next-steps"></a>További lépések
- [Csatlakozás a MySQL az Azure Database-alkalmazások](./howto-connection-string.md).
- Áttelepítéssel kapcsolatos további információkért adatbázisokat az Azure Database-MySQL, tekintse meg a [adatbázis áttelepítési útmutató](http://aka.ms/datamigration).
