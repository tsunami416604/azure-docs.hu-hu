---
title: Áttelepítés memóriaképsel és visszaállítással – Azure Database for MariaDB
description: Ez a cikk két gyakori módon biztonsági másolatot és visszaállításhoz adatbázisok az Azure Database for MariaDB, eszközök használatával, mint a mysqldump, MySQL Workbench és PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 72735e83af97fde8377e27daa45501704ef5a3c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164542"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>A MariaDB-adatbázis áttelepítése a MariaDB Azure-adatbázisába a memóriakép és visszaállítás használatával
Ez a cikk két gyakori módszer az adatbázisok biztonsági mentésére és visszaállítására a MariaDB Azure-adatbázisában
- Dump és visszaállítás a parancssorból (mysqldump használatával) 
- Dump és visszaállítás phpMyAdmin használatával

## <a name="before-you-begin"></a>Előkészületek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- [Azure-adatbázis létrehozása a MariaDB-kiszolgálóhoz – Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- a machine-re telepített [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) parancssori segédprogram.
- MySQL Workbench [MySQL Workbench Letöltés](https://dev.mysql.com/downloads/workbench/) vagy más harmadik fél MySQL eszköz csinálni dump és visszaállítási parancsokat.

## <a name="use-common-tools"></a>Általános eszközök használata
A közös segédprogramok és eszközök, például a MySQL Workbench vagy a mysqldump segítségével távolról csatlakozhat és állíthat vissza adatokat a MariaDB Azure Database szolgáltatásába. Használja ezeket az eszközöket az ügyfélgépen egy internet-kapcsolat, hogy csatlakozzon az Azure Database for MariaDB. A legjobb biztonsági gyakorlatérdekében használjon SSL-titkosítású kapcsolatot, lásd [még: SSL-kapcsolat konfigurálása az Azure Database for MariaDB-ben című témakört.](concepts-ssl-connection-security.md) A MariaDB Azure Database szolgáltatásba való áttelepítéskor nem kell áthelyeznie a memóriaképfájlokat semmilyen speciális felhőhelyre. 

## <a name="common-uses-for-dump-and-restore"></a>A memóriakép és a visszaállítás gyakori felhasználási ideje
A MySQL segédprogramok, például a mysqldump és a mysqlpump segítségével adatbázisokat defektésre és betöltésre használhat egy Azure Database for MariaDB kiszolgálóra számos gyakori forgatókönyv esetén. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- A teljes adatbázis áttelepítésekor használjon adatbázis-memóriaképeket. Ez a javaslat nagy mennyiségű adat áthelyezésekor érvényes, vagy ha minimálisra szeretné csökkenteni az élő webhelyek vagy alkalmazások szolgáltatásának megszakítását. 
-  Az adatbázis összes táblája esetében az InnoDB tárolási motort használja, amikor betölti az adatokat az Azure Database for MariaDB-be. A MariaDB Azure Database csak az InnoDB Storage motort támogatja, ezért nem támogatja az alternatív tárolómotorokat. Ha a táblák más tárolómotorokkal vannak konfigurálva, konvertálja őket InnoDB motorformátumra, mielőtt a MariaDB-alapú Azure Database-be való áttelepítésre váltana.
   Ha például egy WordPress vagy WebApp a MyISAM táblákat használja, először konvertálja ezeket a táblákat innoDB formátumba való áttelepítéssel, mielőtt visszaállítana a MariaDB Azure Database for MariaDB-re. A záradék `ENGINE=InnoDB` segítségével állítsa be az új tábla létrehozásakor használt motort, majd a visszaállítás előtt vigye át az adatokat a kompatibilis táblába. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- A kompatibilitási problémák elkerülése érdekében az adatbázisok biztonsági mentésekor győződjön meg arról, hogy a forrás- és a célrendszerek ugyanazt a MariaDB-verziót használják. Ha például a meglévő MariaDB-kiszolgáló 10.2-es verziója, akkor át kell telepítenie az Azure Database for MariaDB 10.2-es verziójának futtatására konfigurált. A `mysql_upgrade` parancs nem működik a MariaDB-kiszolgálóazure-adatbázisában, és nem támogatott. Ha frissítenie kell a MariaDB verziók között, először ürítse ki vagy exportálja az alacsonyabb verziójú adatbázist a MariaDB egy magasabb verziójába a saját környezetében. Ezután `mysql_upgrade`futtassa a , mielőtt megpróbálna migrálódik egy Azure-adatbázisba a MariaDB számára.

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
-   Másolja a biztonsági mentési fájlokat egy Azure blob/store, és hajtsa végre a visszaállítást onnan, amely sokkal gyorsabb, mint a visszaállítás végrehajtása az interneten keresztül.

## <a name="create-a-backup-file"></a>Biztonságimásolat-fájl létrehozása
Ha egy meglévő MariaDB-adatbázisról szeretne biztonsági másolatot tenni a helyi kiszolgálón vagy egy virtuális gépen, futtassa a következő parancsot a mysqldump használatával: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

A paraméterek et a következőknek kell megadniuk:
- -1, nem, nem, nem, nem, nem, nem Az adatbázis felhasználóneve 
- Nem, nem, nem, nem, nem, nem Az adatbázis jelszava (vegye figyelembe, hogy nincs szóköz a -p és a jelszó között) 
- [dbname] Az adatbázis neve 
- [backupfile.sql] Az adatbázis biztonsági másolatának fájlneve 
- Nem, nem, nem, nem, nem, nem, nem, nem, nem A mysqldump opció 

Ha például a MariaDB-kiszolgálón egy "testdb" nevű adatbázisról szeretne biztonsági másolatot, amelynek felhasználóneve "testuser", és nem jelszó valamerre a testdb_backup.sql fájlhoz, használja a következő parancsot. A parancs biztonsági `testdb` másolatot készít `testdb_backup.sql`az adatbázisról egy fájlba, amely tartalmazza az adatbázis újbóli létrehozásához szükséges összes SQL utasítást. 

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

## <a name="create-a-database-on-the-target-server"></a>Adatbázis létrehozása a célkiszolgálón
Hozzon létre egy üres adatbázist a cél Azure Database mariaDB-kiszolgáló, ahol át szeretné telepíteni az adatokat. Az adatbázis létrehozásához használjon egy eszközt, például a MySQL Workbench-et. Az adatbázis neve megegyezhet a kimászott adatokat tartalmazó adatbázisnevével, vagy létrehozhat egy másik nevű adatbázist is.

A csatlakozáshoz keresse meg a kapcsolatadatait az Azure-adatbázis **áttekintése** a MariaDB-hez című témakörben.

![A kapcsolatadatainak megkeresése az Azure Portalon](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Adja hozzá a kapcsolat adatait a MySQL Workbench-hez.

![MySQL workbench kapcsolati karakterlánc](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>A MariaDB-adatbázis visszaállítása
Miután létrehozta a céladatbázist, használhatja a mysql parancsot vagy a MySQL Workbench-et az adatok visszaállításához az adott újonnan létrehozott adatbázisba a memóriaképfájlból.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Ebben a példában állítsa vissza az adatokat az újonnan létrehozott adatbázisba a cél Azure Database for MariaDB-kiszolgálón.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportálás a PHPMyAdmin használatával
Az export, akkor a közös eszköz phpMyAdmin, amit már telepített helyileg a környezetben. A MariaDB adatbázis exportálása a PHPMyAdmin használatával:
1. Nyissa meg a phpMyAdmin.Open phpMyAdmin.
2. Válassza ki az adatbázist. Kattintson az adatbázis nevére a bal oldali listában. 
3. Kattintson az **Exportálás** hivatkozásra. Megjelenik egy új lap az adatbázis kiírásának megtekintéséhez.
4. Az Exportálás területen kattintson az **Összes kijelölése** hivatkozásra az adatbázis tábláinak kiválasztásához. 
5. Az SQL beállítások területen kattintson a megfelelő beállításokra. 
6. Kattintson a **Fájlmentése** és a megfelelő tömörítési beállításra, majd az **Ugrás** gombra. Megjelenik egy párbeszédpanel, amely a fájl helyi mentését kéri.

## <a name="import-using-phpmyadmin"></a>Importálás a PHPMyAdmin használatával
Az adatbázis importálása hasonló az exportáláshoz. Tegye a következő műveleteket:
1. Nyissa meg a phpMyAdmin.Open phpMyAdmin. 
2. A phpMyAdmin telepítőlapján kattintson a **Hozzáadás** gombra az Azure Database for MariaDB-kiszolgáló hozzáadásához. Adja meg a kapcsolat részleteit és a bejelentkezési adatokat.
3. Hozzon létre egy megfelelően elnevezett adatbázist, és jelölje ki a képernyő bal oldalán. A meglévő adatbázis újraírásához kattintson az adatbázis nevére, jelölje be a táblanevek melletti összes jelölőnégyzetet, és a Meglévő táblák törléséhez válassza a **Legördülő** menüt. 
4. Kattintson az **SQL** hivatkozásra a lap megjelenítéséhez, ahol beírhatja az SQL-parancsokat, vagy feltöltheti az SQL-fájlt. 
5. A **tallózás** gombbal megkeresheti az adatbázisfájlt. 
6. Kattintson az **Ugrás** gombra a biztonsági másolat exportálásához, az SQL-parancsok végrehajtásához és az adatbázis újbóli létrehozásához.

## <a name="next-steps"></a>További lépések
- [Alkalmazások csatlakoztatása a MariaDB Azure Database szolgáltatáshoz.](./howto-connection-string.md)
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
