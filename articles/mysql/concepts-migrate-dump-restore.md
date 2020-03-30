---
title: Áttelepítés dump és visszaállítás használatával - Azure Database for MySQL
description: Ez a cikk két gyakori módon biztonsági másolatot és visszaállításhoz adatbázisok az Azure Database for MySQL, eszközök használatával, mint a mysqldump, MySQL Workbench és PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: b15da2aa83231bfdc8732995888349b06ab56d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163777"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>MySQL-adatbázis migrálása a MySQL-hez készült Azure Database-be memóriakép és visszaállítás használatával
Ez a cikk két gyakori módot ismerteti az adatbázisok biztonsági mentésére és visszaállítására a MySQL Azure-adatbázisában
- Dump és visszaállítás a parancssorból (mysqldump használatával) 
- Dump és visszaállítás phpMyAdmin használatával 

## <a name="before-you-begin"></a>Előkészületek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- [Azure-adatbázis létrehozása a MySQL-kiszolgálóhoz – Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- a machine-re telepített [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) parancssori segédprogram.
- MySQL Workbench [MySQL Workbench Letöltés](https://dev.mysql.com/downloads/workbench/) vagy más harmadik fél MySQL eszköz csinálni dump és visszaállítási parancsokat.

## <a name="use-common-tools"></a>Általános eszközök használata
A közös segédprogramok és eszközök, például a MySQL Workbench vagy a mysqldump használatával távolról csatlakozhat és állíthat vissza adatokat az Azure Database for MySQL-hez. Használja ezeket az eszközöket az ügyfélgépen egy internet-kapcsolaton, hogy csatlakozzon az Azure Database for MySQL. A legjobb biztonsági gyakorlatérdekében használjon SSL-titkosítású kapcsolatot, lásd [még: SSL-kapcsolat konfigurálása az Azure Database for MySQL alkalmazásban.](concepts-ssl-connection-security.md) Nem kell áthelyeznia a memóriakép fájlokat bármely speciális felhőhelyre, amikor az Azure Database for MySQL áttelepítése. 

## <a name="common-uses-for-dump-and-restore"></a>A memóriakép és a visszaállítás gyakori felhasználási ideje
A MySQL segédprogramok, például a mysqldump és a mysqlpump segítségével adatbázisokat defektésre és betöltésre használhat egy Azure MySQL-adatbázisba számos gyakori forgatókönyv esetén. Más esetekben [használhatja](concepts-migrate-import-export.md) az Importálás és exportálás megközelítést.

- A teljes adatbázis áttelepítésekor használjon adatbázis-memóriaképeket. Ez a javaslat nagy mennyiségű MySQL-adat áthelyezésekor érvényes, vagy ha minimalizálni szeretné a szolgáltatás megszakítását az élő webhelyek vagy alkalmazások esetében. 
-  Győződjön meg arról, hogy az adatbázis összes táblája esetében az InnoDB tárolási motort használja, amikor betölti az adatokat az Azure Database for MySQL-be. Az Azure Database for MySQL csak az InnoDB Storage motort támogatja, ezért nem támogatja az alternatív tárolómotorokat. Ha a táblák más tárolómotorokkal vannak konfigurálva, konvertálja őket InnoDB motorformátumra, mielőtt az Azure Database for MySQL-re való áttérésre váltana.
   Ha például egy WordPress vagy WebApp a MyISAM táblákat használja, először konvertálja ezeket a táblákat innoDB formátumba való áttelepítéssel, mielőtt visszaállítana a MySQL Azure Database for MySQL-re. A záradék `ENGINE=InnoDB` segítségével állítsa be az új tábla létrehozásakor használt motort, majd a visszaállítás előtt vigye át az adatokat a kompatibilis táblába. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- A kompatibilitási problémák elkerülése érdekében az adatbázisok biztonsági mentésekor győződjön meg arról, hogy a forrás- és a célrendszerek ugyanazt a MySQL-verziót használják. Ha például a meglévő MySQL-kiszolgáló 5.7-es verziójú, akkor az 5.7-es verzió futtatásához konfigurált Azure Database for MySQL-re kell áttérnie. A `mysql_upgrade` parancs nem működik az Azure Database for MySQL-kiszolgáló, és nem támogatott. Ha frissítenie kell a MySQL verziók között, először dump vagy exportálni az alacsonyabb verziós adatbázis egy magasabb verzió a MySQL a saját környezetben. Ezután `mysql_upgrade`futtassa a , mielőtt megpróbálna migrálódik egy Azure Database for MySQL.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások
A teljesítmény optimalizálása érdekében vegye figyelembe az alábbi szempontokat nagy adatbázisok lerakásakor:
-   Használja `exclude-triggers` a lehetőséget mysqldump, ha dömping adatbázisok. Zárja ki az eseményindítókat a memóriaképfájlokból, hogy elkerülje az eseményindítóparancsok indítását az adat-visszaállítás során. 
-   Ezzel `single-transaction` a beállítással a tranzakcióelkülönítési módot megismételhető OLVASÁSra állíthatja, és az adatok kidobása előtt elküldi a START TRANSACTION SQL utasítást a kiszolgálónak. Ha egy tranzakción belül sok táblát kidob, akkor a visszaállítás során további tárhely et kell felhasználni. Az `single-transaction` opció `lock-tables` és a beállítás kölcsönösen kizárják egymást, mivel a LOCK TABLES hatására a függőben lévő tranzakciók implicit módon lesznek véglegesítve. Nagy táblák kiírásához `single-transaction` kombinálja `quick` a lehetőséget a beállítással. 
-   Használja `extended-insert` a többsoros szintaxist, amely több ÉRTÉKET tartalmaz. Ez kisebb memóriaképfájlt eredményez, és felgyorsítja a beszúrásokat a fájl újratöltésekor.
-  Használja `order-by-primary` a mysqldump opciót adatbázisok kirakásakor, hogy az adatok elsődleges kulcssorrendben íródjanak.
-   Használja `disable-keys` a mysqldump opciót az adatok kirakásakor, hogy letiltsa az idegen kulcs megkötéseit a betöltés előtt. Az idegen kulcsellenőrzések letiltása teljesítménynövekedést biztosít. Engedélyezze a korlátozásokat, és ellenőrizze az adatokat a terhelés után a hivatkozási integritás biztosítása érdekében.
-   Szükség esetén használjon particionált táblákat.
-   Adatok betöltése párhuzamosan. Kerülje a túl sok párhuzamosság, amely azt eredményezné, hogy elérje az erőforrás-korlátot, és az Azure Portalon elérhető metrikák használatával figyelheti az erőforrásokat. 
-   Használja `defer-table-indexes` a mysqlpump opciót adatbázisok kirakásakor, így az index létrehozása a táblák adatainak betöltése után történik.
-   A `skip-definer` mysqlpump beállításával kihagyhatja a definer és az SQL SECURITY záradékokat a nézetek és a tárolt eljárások létrehozási utasításokból.  A memóriaképfájl újratöltésekor olyan objektumokat hoz létre, amelyek az alapértelmezett DEFINER és SQL SECURITY értékeket használják.
-   Másolja a biztonsági mentési fájlokat egy Azure blob/store, és hajtsa végre a visszaállítást onnan, amely sokkal gyorsabb, mint a visszaállítás végrehajtása az interneten keresztül.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Biztonsági másolat létrehozása a parancssorból a mysqldump használatával
Ha egy meglévő MySQL-adatbázisról szeretne biztonsági másolatot tenni a helyi kiszolgálón vagy egy virtuális gépen, futtassa a következő parancsot: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

A paraméterek et a következőknek kell megadniuk:
- -1, nem, nem, nem, nem, nem, nem Az adatbázis felhasználóneve 
- Nem, nem, nem, nem, nem, nem Az adatbázis jelszava (vegye figyelembe, hogy nincs szóköz a -p és a jelszó között) 
- [dbname] Az adatbázis neve 
- [backupfile.sql] Az adatbázis biztonsági másolatának fájlneve 
- Nem, nem, nem, nem, nem, nem, nem, nem, nem A mysqldump opció 

Ha például biztonsági másolatot szeretne tenni egy "testdb" nevű adatbázisról a MySQL-kiszolgálón a "testuser" felhasználónévvel és a testdb_backup.sql fájl jelszavának nélkül, használja a következő parancsot. A parancs biztonsági `testdb` másolatot készít `testdb_backup.sql`az adatbázisról egy fájlba, amely tartalmazza az adatbázis újbóli létrehozásához szükséges összes SQL utasítást. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Ha az adatbázisban meghatározott táblákat szeretne kijelölni, sorolja fel a szóközökkel elválasztott táblaneveket. Ha például csak a table1 és table2 táblákat szeretné biztonsági másolatot tenni a "testdb" tábláról, kövesse az alábbi példát: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Ha egyszerre több adatbázisról szeretne biztonsági másolatot tartani, használja a --database kapcsolót, és sorolja fel az adatbázisneveket szóközökkel elválasztva. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Adatbázis létrehozása a mySQL-kiszolgálócél Azure-adatbázisában
Hozzon létre egy üres adatbázist a cél Azure Database for MySQL-kiszolgáló, ahol szeretné áttelepíteni az adatokat. Az adatbázis létrehozásához használjon egy eszközt, például a MySQL Workbench-et. Az adatbázis neve megegyezhet a kimászott adatokat tartalmazó adatbázisnevével, vagy létrehozhat egy másik nevű adatbázist is.

A csatlakozáshoz keresse meg a kapcsolatadatait az Azure Database for MySQL **áttekintése** című témakörben.

![A kapcsolatadatainak megkeresése az Azure Portalon](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Adja hozzá a kapcsolat adatait a MySQL Workbench-hez.

![MySQL workbench kapcsolati karakterlánc](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>A MySQL-adatbázis visszaállítása parancssorból vagy MySQL Workbench használatával
Miután létrehozta a céladatbázist, használhatja a mysql parancsot vagy a MySQL Workbench-et az adatok visszaállításához az adott újonnan létrehozott adatbázisba a memóriaképfájlból.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Ebben a példában állítsa vissza az adatokat az újonnan létrehozott adatbázisba a cél Azure Database for MySQL-kiszolgáló.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportálás a PHPMyAdmin használatával
Az export, akkor a közös eszköz phpMyAdmin, amit már telepített helyileg a környezetben. MySQL adatbázis exportálása phpMyAdmin használatával:
1. Nyissa meg a phpMyAdmin.Open phpMyAdmin.
2. Válassza ki az adatbázist. Kattintson az adatbázis nevére a bal oldali listában. 
3. Kattintson az **Exportálás** hivatkozásra. Megjelenik egy új lap az adatbázis kiírásának megtekintéséhez.
4. Az Exportálás területen kattintson az **Összes kijelölése** hivatkozásra az adatbázis tábláinak kiválasztásához. 
5. Az SQL beállítások területen kattintson a megfelelő beállításokra. 
6. Kattintson a **Fájlmentése** és a megfelelő tömörítési beállításra, majd az **Ugrás** gombra. Megjelenik egy párbeszédpanel, amely a fájl helyi mentését kéri.

## <a name="import-using-phpmyadmin"></a>Importálás a PHPMyAdmin használatával
Az adatbázis importálása hasonló az exportáláshoz. Tegye a következő műveleteket:
1. Nyissa meg a phpMyAdmin.Open phpMyAdmin. 
2. A phpMyAdmin telepítőlapján kattintson a **Hozzáadás** gombra az Azure Database for MySQL-kiszolgáló hozzáadásához. Adja meg a kapcsolat részleteit és a bejelentkezési adatokat.
3. Hozzon létre egy megfelelően elnevezett adatbázist, és jelölje ki a képernyő bal oldalán. A meglévő adatbázis újraírásához kattintson az adatbázis nevére, jelölje be a táblanevek melletti összes jelölőnégyzetet, és a Meglévő táblák törléséhez válassza a **Legördülő** menüt. 
4. Kattintson az **SQL** hivatkozásra a lap megjelenítéséhez, ahol beírhatja az SQL-parancsokat, vagy feltöltheti az SQL-fájlt. 
5. A **tallózás** gombbal megkeresheti az adatbázisfájlt. 
6. Kattintson az **Ugrás** gombra a biztonsági másolat exportálásához, az SQL-parancsok végrehajtásához és az adatbázis újbóli létrehozásához.

## <a name="next-steps"></a>További lépések
- [Alkalmazások csatlakoztatása a MySQL Azure Database szolgáltatáshoz.](./howto-connection-string.md)
- Az adatbázisok Azure Database for MySQL-be való áttelepítéséről az [Adatbázis-áttelepítési útmutatóban](https://aka.ms/datamigration)talál további információt.
