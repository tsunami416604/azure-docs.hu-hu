---
title: Migrálás a dump és a Restore használatával – Azure Database for MySQL
description: Ez a cikk két gyakori módszert ismertet a Azure Database for MySQL adatbázisainak biztonsági mentésére és visszaállítására, például a mysqldump, a MySQL Workbench és a PHPMyAdmin eszközzel.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: b15da2aa83231bfdc8732995888349b06ab56d15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78163777"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>MySQL-adatbázis migrálása a MySQL-hez készült Azure Database-be memóriakép és visszaállítás használatával
Ez a cikk két gyakori módszert ismertet a Azure Database for MySQL adatbázisainak biztonsági mentésére és visszaállítására
- Memóriakép és visszaállítás a parancssorból (mysqldump használatával) 
- Memóriakép és visszaállítás a PHPMyAdmin használatával 

## <a name="before-you-begin"></a>Előkészületek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Azure Database for MySQL kiszolgáló létrehozása – Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- a [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) parancssori segédprogram telepítve van a gépen.
- MySQL Workbench [MySQL Workbench Letöltés](https://dev.mysql.com/downloads/workbench/) vagy más, harmadik féltől származó MySQL-eszköz a dump és a Restore parancsok végrehajtásához.

## <a name="use-common-tools"></a>Gyakori eszközök használata
Használjon olyan gyakori segédprogramokat és eszközöket, mint például a MySQL Workbench vagy a mysqldump az adatAzure Database for MySQLba való távoli kapcsolódáshoz és az adathelyreállításhoz. Az ügyfélgépen lévő eszközök használatával internetkapcsolattal csatlakozhat a Azure Database for MySQLhoz. Használjon SSL-titkosítású titkosított kapcsolatot az ajánlott biztonsági eljárásokhoz: [az SSL-kapcsolat konfigurálása a Azure Database for MySQLban](concepts-ssl-connection-security.md). Az Azure Database for MySQLba való Migrálás során nem kell áthelyeznie a memóriakép fájljait a Felhőbeli helyre. 

## <a name="common-uses-for-dump-and-restore"></a>A dump és a Restore gyakori felhasználási módjai
A MySQL-segédprogramok, például a mysqldump és a mysqlpump használatával számos gyakori forgatókönyvben elvégezheti az adatbázisok kiírását és betöltését egy Azure MySQL-adatbázisba. Más helyzetekben az [importálási és exportálási](concepts-migrate-import-export.md) módszert is használhatja helyette.

- Az adatbázis-memóriaképek használata a teljes adatbázis áttelepítésekor. Ez a javaslat a nagy mennyiségű MySQL-adatbázis áthelyezésekor, illetve az élő webhelyeken vagy alkalmazásokban a szolgáltatás megszakadásának minimálisra csökkentése érdekében áll fenn. 
-  Győződjön meg arról, hogy az adatbázis összes táblája esetében az InnoDB tárolási motort használja, amikor betölti az adatokat az Azure Database for MySQL-be. A Azure Database for MySQL csak a InnoDB tároló motort támogatja, ezért nem támogatja az alternatív tárolóeszközöket. Ha a táblák más tárolási motorokkal vannak konfigurálva, a Azure Database for MySQLba való áttelepítés előtt alakítsa át őket a InnoDB-motor formátumba.
   Ha például egy WordPress vagy WebApp a MyISAM táblázatokat használja, először alakítsa át ezeket a táblákat úgy, hogy a Azure Database for MySQLra való visszaállítás előtt áttelepíti őket a InnoDB formátumba. Használja a záradékot `ENGINE=InnoDB` az új tábla létrehozásakor használt motor beállításához, majd a visszaállítás előtt vigye át az adatok a kompatibilis táblába. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- A kompatibilitási problémák elkerülése érdekében az adatbázisok biztonsági mentésekor győződjön meg arról, hogy a forrás- és a célrendszerek ugyanazt a MySQL-verziót használják. Ha például a meglévő MySQL-kiszolgáló a 5,7-es verzió, akkor át kell térnie az 5,7-es verzió futtatására konfigurált Azure Database for MySQLra. A `mysql_upgrade` parancs nem működik Azure Database for MySQL-kiszolgálón, és nem támogatott. Ha frissítenie kell a MySQL-verziókat, először az alacsonyabb verziójú adatbázist a MySQL egy magasabb verziójára exportálhatja a saját környezetében. Ezután futtassa `mysql_upgrade`a parancsot, mielőtt áttelepíti az áttelepítést egy Azure Database for MySQLba.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások
A teljesítmény optimalizálása érdekében vegye figyelembe a következő szempontokat a nagyméretű adatbázisok kiírásakor:
-   Az adatbázisok `exclude-triggers` kiírásakor használja a mysqldump kapcsolót. Kihagyhatja a memóriaképből származó eseményindítókat, hogy elkerülje az eseményindító parancsainak égetését az adatok visszaállítása során. 
-   A `single-transaction` beállítással beállíthatja a tranzakció-elkülönítési módot ismétlődő olvasásra, és elküldheti a Start Transaction SQL-utasítást a kiszolgálónak az adatok kiírása előtt. Egy tranzakción belül számos tábla kiírásakor a rendszer néhány további tárterületet használ a visszaállítás során. A `single-transaction` beállítás és a `lock-tables` beállítás kölcsönösen kizárható, mert a zárolási táblázatok implicit módon elvégeznek minden függőben lévő tranzakciót. A nagyméretű táblák kiírásához kombinálja a `single-transaction` kapcsolót a `quick` kapcsolóval. 
-   Használja a `extended-insert` több sorból álló szintaxist, amely több értéklista használatát is magában foglalja. Ez kisebb memóriaképet eredményez, és felgyorsítja a beszúrást a fájl újratöltése után.
-  A mysqldump `order-by-primary` használatakor használja az adatbázisokat, hogy az elsődleges kulcs sorrendjében legyenek megírtak.
-   A mysqldump `disable-keys` használatakor használja az adatmemóriaképet, hogy letiltsa a külső kulcsokra vonatkozó korlátozásokat a betöltés előtt. A külső kulcsok ellenőrzésének letiltása teljesítménybeli nyereséget biztosít. Engedélyezze a korlátozásokat, és ellenőrizze az adatok betöltését a hivatkozási integritás biztosítása érdekében.
-   Szükség esetén particionált táblákat használjon.
-   Párhuzamosan tölthetők be az adathalmazok. Kerülje a túl sok párhuzamosságot, amelynek hatására elérheti az erőforrás-korlátot, és figyelheti az erőforrásokat a Azure Portal elérhető metrikák használatával. 
-   A mysqlpump `defer-table-indexes` használatakor használja az adatbázisokat, így az index létrehozása a táblázatok adatainak betöltése után történik.
-   A mysqlpump `skip-definer` lehetőséggel kihagyhatja a definomabb és az SQL biztonsági záradékokat a Create utasításban a nézetek és a tárolt eljárások számára.  A memóriakép újratöltése után az az alapértelmezett és az SQL biztonsági értékeket használó objektumokat hoz létre.
-   Másolja a biztonságimásolat-fájlokat egy Azure-blobba/-tárolóba, és végezze el a visszaállítást onnan, ami sokkal gyorsabb lehet, mint az interneten keresztüli visszaállítás végrehajtása.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Biztonságimásolat-fájl létrehozása a parancssorból a mysqldump használatával
Ha egy meglévő MySQL-adatbázist szeretne biztonsági másolatot készíteni a helyi helyszíni kiszolgálón vagy egy virtuális gépen, futtassa a következő parancsot: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

A megadható paraméterek a következők:
- uname Az adatbázis felhasználóneve 
- pass Az adatbázis jelszava (Megjegyzés: a-p és a jelszó között nincs szóköz) 
- dbname Az adatbázis neve 
- [biztonságimentésfájlja. SQL] az adatbázis biztonsági másolatának fájlneve 
- [--opt] A mysqldump beállítás 

Ha például egy "testdb" nevű adatbázist szeretne biztonsági másolatot készíteni a MySQL-kiszolgálón a (z) "tesztfelhasználó" felhasználónévvel, és nincs jelszó testdb_backup. SQL fájlhoz, használja a következő parancsot. A parancs biztonsági másolatot készít az `testdb` adatbázisról egy nevű `testdb_backup.sql`fájlba, amely tartalmazza az adatbázis újbóli létrehozásához szükséges összes SQL-utasítást. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Ha a biztonsági mentéshez az adatbázisban megadott táblákat szeretne kiválasztani, sorolja fel a táblák nevét szóközzel elválasztva. Ha például csak a "testdb" tábla1 és table2-táblákról szeretne biztonsági másolatot készíteni, kövesse az alábbi példát: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Ha egynél több adatbázisról szeretne biztonsági másolatot készíteni, használja az--Database kapcsolót, és sorolja fel az adatbázis nevét szóközzel elválasztva. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Adatbázis létrehozása a cél Azure Database for MySQL-kiszolgálón
Hozzon létre egy üres adatbázist azon a célként Azure Database for MySQL kiszolgálón, amelyen át szeretné telepíteni az adatátvitelt. Hozzon létre egy eszközt, például a MySQL Workbench alkalmazást az adatbázis létrehozásához. Az adatbázis neve megegyezik a memóriaképet tartalmazó adatbázis nevével, vagy létrehozhat egy másik nevű adatbázist is.

A csatlakozáshoz keresse meg a kapcsolati adatokat a Azure Database for MySQL **áttekintésében** .

![A Azure Portal található kapcsolatok adatainak megkeresése](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Adja hozzá a kapcsolatok adatait a MySQL Workbench-hez.

![MySQL Workbench-beli kapcsolatok karakterlánca](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>MySQL-adatbázis visszaállítása a parancssori vagy a MySQL Workbench használatával
Miután létrehozta a céladatbázis-adatbázist, a MySQL-paranccsal vagy a MySQL Workbench használatával visszaállíthatja az adatait az adott újonnan létrehozott adatbázisba a memóriakép fájlból.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Ebben a példában a cél Azure Database for MySQL-kiszolgálón az újonnan létrehozott adatbázisba állítja vissza az adathalmazt.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportálás a PHPMyAdmin használatával
Az exportáláshoz használhatja az általános eszközt (phpMyAdmin), amelyet esetleg már helyileg telepített a környezetében. MySQL-adatbázis exportálása a PHPMyAdmin használatával:
1. A phpMyAdmin megnyitása.
2. Válassza ki az adatbázist. Kattintson az adatbázis nevére a bal oldali listában. 
3. Kattintson az **Exportálás** hivatkozásra. Megjelenik egy új lap az adatbázis memóriaképének megtekintéséhez.
4. Az Exportálás területen kattintson az **összes kijelölése** hivatkozásra az adatbázis tábláinak kiválasztásához. 
5. Az SQL-beállítások területen kattintson a megfelelő beállításokra. 
6. Kattintson a **Mentés fájlként** lehetőségre és a megfelelő tömörítési lehetőségre, majd kattintson a **Go (ugrás** ) gombra. Ekkor megjelenik egy párbeszédpanel, amely felszólítja a fájl helyi mentésére.

## <a name="import-using-phpmyadmin"></a>Importálás a PHPMyAdmin használatával
Az adatbázis importálása hasonló az exportáláshoz. Hajtsa végre a következő műveleteket:
1. A phpMyAdmin megnyitása. 
2. A phpMyAdmin beállítása lapon kattintson a **Hozzáadás** gombra a Azure Database for MySQL-kiszolgáló hozzáadásához. Adja meg a kapcsolat adatait és a bejelentkezési adatokat.
3. Hozzon létre egy megfelelő névvel ellátott adatbázist, és válassza ki a képernyő bal oldalán. A meglévő adatbázis újraírásához kattintson az adatbázis nevére, jelölje be az összes jelölőnégyzetet a táblázat neve mellett, majd válassza a **drop (eldobás** ) lehetőséget a meglévő táblák törléséhez. 
4. Az SQL **-hivatkozásra kattintva** megjelenítheti az SQL-parancsokat beírható oldalt, vagy feltöltheti az SQL-fájlt. 
5. Az adatbázisfájl megkereséséhez használja a **Tallózás** gombot. 
6. Kattintson a **Go (ugrás** ) gombra a biztonsági mentés exportálásához, hajtsa végre az SQL-parancsokat, majd hozza létre újra az adatbázist.

## <a name="next-steps"></a>További lépések
- [Alkalmazások Összekötése Azure Database for MySQLhoz](./howto-connection-string.md).
- Az adatbázisok Azure Database for MySQLre való áttelepítésével kapcsolatos további információkért tekintse meg az [adatbázis-áttelepítési útmutatót](https://aka.ms/datamigration).
