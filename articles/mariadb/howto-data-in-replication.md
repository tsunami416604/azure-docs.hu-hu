---
title: Az adatreplikálás konfigurálása – Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan állíthatja be a felhőbe irányuló replikálást a Azure Database for MariaDBban.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 0dbbc9b09d5d4770296223db9dc909c17f574fe8
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767024"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>felhőbe irányuló replikálás konfigurálása Azure Database for MariaDB

Ez a cikk azt ismerteti, hogyan állíthatja be a felhőbe irányuló replikálás a Azure Database for MariaDBban a fő-és a replika-kiszolgálók konfigurálásával. Ez a cikk azt feltételezi, hogy a MariaDB-kiszolgálókkal és-adatbázisokkal kapcsolatban némi korábbi tapasztalat van.

Ahhoz, hogy replikát hozzon létre a Azure Database for MariaDB szolgáltatásban, felhőbe irányuló replikálás szinkronizálja a helyszíni, a virtuális gépeken vagy a Cloud Database Servicesben lévő fő MariaDB-kiszolgáló adatait.

> [!NOTE]
> Ha a főkiszolgáló 10,2-es vagy újabb verziójú, javasoljuk, hogy a [globális tranzakció-azonosítóval](https://mariadb.com/kb/en/library/gtid/)állítsa be a felhőbe irányuló replikálás.


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Replikaként használandó MariaDB-kiszolgáló létrehozása

1. Hozzon létre egy új Azure Database for MariaDB-kiszolgálót (például replica.mariadb.database.azure.com). A kiszolgáló a felhőbe irányuló replikálás található replika-kiszolgáló.

    A kiszolgálók létrehozásával kapcsolatos további tudnivalókért lásd: [Azure Database for MariaDB kiszolgáló létrehozása a Azure Portal használatával](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > A Azure Database for MariaDB-kiszolgálót a általános célú vagy a memória optimalizált díjszabási szintjein kell létrehoznia.

2. Hozzon létre azonos felhasználói fiókokat és megfelelő jogosultságokat.
    
    A felhasználói fiókok nem replikálódnak a főkiszolgálóról a másodpéldány-kiszolgálóra. A replika-kiszolgálóhoz való felhasználói hozzáférés biztosításához manuálisan kell létrehoznia az összes fiókot és a megfelelő jogosultságokat az újonnan létrehozott Azure Database for MariaDB-kiszolgálón.

## <a name="configure-the-master-server"></a>A főkiszolgáló konfigurálása

A következő lépések előkészítik és konfigurálja a helyszínen üzemeltetett MariaDB-kiszolgálót egy virtuális gépen vagy egy felhőalapú adatbázis-szolgáltatásban felhőbe irányuló replikálás számára. A MariaDB-kiszolgáló a felhőbe irányuló replikálás főkiszolgálója.

1. A bináris naplózás bekapcsolása.
    
    Ha szeretné megtekinteni, hogy engedélyezve van-e a bináris naplózás a főkiszolgálón, írja be a következő parancsot:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Ha az [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) változó a `ON`értéket adja vissza, akkor a bináris naplózás engedélyezve van a kiszolgálón.

   Ha `log_bin` a `OFF`értéket adja vissza, szerkessze a **My. cnf** fájlt, hogy a `log_bin=ON` bekapcsolja a bináris naplózást. Indítsa újra a kiszolgálót a módosítás érvénybe léptetéséhez.

2. A főkiszolgáló beállításainak konfigurálása.

    Felhőbe irányuló replikálás megköveteli, hogy a paraméter `lower_case_table_names` konzisztens legyen a fő-és a replika-kiszolgálók között. A `lower_case_table_names` paraméter alapértelmezés szerint `1`re van állítva Azure Database for MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Hozzon létre egy új replikációs szerepkört, és állítson be engedélyeket.

   Hozzon létre egy felhasználói fiókot a fő kiszolgálón, amely replikációs jogosultságokkal van konfigurálva. Az SQL-parancsok vagy a MySQL Workbench használatával hozhat létre fiókot. Ha az SSL-sel való replikálást tervezi, ezt a felhasználói fiók létrehozásakor kell megadnia.
   
   Ha szeretné megtudni, hogyan adhat hozzá felhasználói fiókokat a főkiszolgálón, tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/library/create-user/).

   Az alábbi parancsok használatával az új replikációs szerepkör bármely gépről elérheti a főkiszolgálót, nem csak a főkiszolgálót üzemeltető gépet. Ehhez a hozzáféréshez a **(z) "%" syncuser\@** a parancsban a felhasználó létrehozásához.
   
   A MariaDB-dokumentációval kapcsolatos további tudnivalókért tekintse [meg a fiókok nevének megadásával](https://mariadb.com/kb/en/library/create-user/#account-names)foglalkozó témakört.

   **SQL-parancs**

   - Replikáció SSL használatával

       Ha az SSL használatát szeretné megkövetelni az összes felhasználói kapcsolathoz, adja meg a következő parancsot egy felhasználó létrehozásához:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replikáció SSL nélkül

       Ha az SSL nem szükséges minden kapcsolathoz, adja meg a következő parancsot egy felhasználó létrehozásához:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   A MySQL Workbench replikációs szerepkörének létrehozásához a **felügyelet** ablaktáblán válassza a **felhasználók és jogosultságok**lehetőséget. Ezután válassza a **fiók hozzáadása**lehetőséget.
 
   ![Felhasználók és jogosultságok](./media/howto-data-in-replication/users_privileges.png)

   Adjon meg egy felhasználónevet a **bejelentkezési név** mezőben.

   ![Felhasználó szinkronizálása](./media/howto-data-in-replication/syncuser.png)
 
   Válassza ki a **felügyeleti szerepkörök** panelt, majd a **globális jogosultságok**listájában válassza ki a **replikálási Slave**elemet. A replikációs szerepkör létrehozásához kattintson az **alkalmaz** gombra.

   ![Replikálási Slave](./media/howto-data-in-replication/replicationslave.png)


4. Állítsa a főkiszolgálót csak olvasható módra.

   Az adatbázisok kiírása előtt a kiszolgálót csak olvasható módban kell elhelyezni. Amíg csak olvasható módban van, a főkiszolgáló nem dolgozhat fel írási tranzakciókat. Az üzleti hatás elkerülése érdekében ütemezze a csak olvasási időszakot egy off-Peak idő alatt.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Az aktuális bináris naplófájl nevének és eltolásának beolvasása.

   Az aktuális bináris naplófájl nevének és eltolásának meghatározásához futtassa a következő parancsot: [`show master status`](https://mariadb.com/kb/en/library/show-master-status/).
    
   ```sql
   show master status;
   ```
   Az eredmények az alábbi táblázathoz hasonlóak:
   
   ![Fő állapot eredményei](./media/howto-data-in-replication/masterstatus.png)

   Jegyezze fel a bináris fájl nevét, mert a későbbi lépésekben lesz használva.
   
6. A GTID pozíciójának beolvasása (nem kötelező, a GTID való replikáláshoz szükséges).

   Futtassa a [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) függvényt a megfelelő BinLog-fájlnév és eltolás GTID pozíciójának lekéréséhez.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>A főkiszolgáló kiírása és visszaállítása

1. Az összes adatbázis kiírása a főkiszolgálóról.

   A mysqldump használatával az összes adatbázist kihasználhatja a főkiszolgálóról. Nem szükséges a MySQL-függvénytár és a tesztelési könyvtár kiírása.

    További információ: [memóriakép és visszaállítás](howto-migrate-dump-restore.md).

2. A főkiszolgáló beállítása írási/olvasási módra.

   Az adatbázis kiírása után állítsa vissza a fő MariaDB-kiszolgálót olvasási/írási módra.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Állítsa vissza a memóriakép fájlját az új kiszolgálóra.

   Állítsa vissza a memóriakép-fájlt a Azure Database for MariaDB szolgáltatásban létrehozott kiszolgálóra. A dump-fájlok MariaDB-kiszolgálóra való visszaállításával kapcsolatban lásd: [dump & Restore](howto-migrate-dump-restore.md) .

   Ha a memóriakép nagy méretű, töltse fel azt egy Azure-beli virtuális gépre, amely a replika-kiszolgálóval azonos régióban található. Állítsa vissza a Azure Database for MariaDB-kiszolgálóra a virtuális gépről.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>A fő-és a replika-kiszolgálók összekapcsolása a felhőbe irányuló replikálás indításához

1. Állítsa be a főkiszolgálót.

   Az összes felhőbe irányuló replikálás függvényt tárolt eljárások hajtják végre. Az összes eljárást [felhőbe irányuló replikálás tárolt eljárásokban](reference-data-in-stored-procedures.md)találja. A tárolt eljárások a MySQL-rendszerhéjban vagy a MySQL Workbenchben is futtathatók.

   Két kiszolgáló összekapcsolásához és a replikáció megkezdéséhez jelentkezzen be a cél másodpéldány-kiszolgálóra az Azure DB for MariaDB szolgáltatásban. Ezután állítsa a külső példányt főkiszolgálóként a `mysql.az_replication_change_master` vagy `mysql.az_replication_change_master_with_gtid` tárolt eljárással a MariaDB-kiszolgálóhoz készült Azure-ADATBÁZISon.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   vagy
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: a fő kiszolgáló állomásneve
   - master_user: a fő kiszolgáló felhasználóneve
   - master_password: a főkiszolgáló jelszava
   - master_log_file: bináris naplófájl neve a futtatási `show master status`
   - master_log_pos: bináris naplózási pozíció futtatása `show master status`
   - master_gtid_pos: a GTID pozíciója fut `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány környezete. Ha nem használ SSL-t, adjon meg egy üres karakterláncot. *
    
    
    \* Javasoljuk, hogy a master_ssl_ca paramétert változóként adja át. További információkért tekintse meg az alábbi példákat.

   **Példák**

   - Replikáció SSL használatával

       Hozza létre a `@cert` változót a következő parancsok futtatásával:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Az SSL-sel történő replikáció beállítása a tartományi companya.com üzemeltetett főkiszolgálók és a Azure Database for MariaDBban üzemeltetett replika-kiszolgáló között történik. Ez a tárolt eljárás fut a replikán.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replikáció SSL nélkül

       Az SSL nélküli replikáció beállítása a tartomány companya.com üzemeltetett főkiszolgálók és a Azure Database for MariaDBban üzemeltetett replika-kiszolgáló között történik. Ez a tárolt eljárás fut a replikán.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Replikáció elindítása.

   A replikáció indításához hívja meg a `mysql.az_replication_start` tárolt eljárást.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Replikáció állapotának bejelölése.

   A replikálási állapot megtekintéséhez hívja meg a [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) parancsot a replika kiszolgálóján.
    
   ```sql
   show slave status;
   ```

   Ha `Slave_IO_Running` és `Slave_SQL_Running` állapota `yes`, és a `Seconds_Behind_Master` értéke `0`, a replikálás működik. `Seconds_Behind_Master` jelzi, hogy a replika milyen későn van. Ha az érték nem `0`, akkor a replika frissíti a frissítéseket.

4. Frissítse a megfelelő kiszolgálói változókat az adatreplikálás biztonságosabbá tételéhez (csak a GTID nélküli replikáláshoz szükséges).
    
    A MariaDB natív replikációjának korlátozása miatt a GTID-forgatókönyv nélkül be kell állítania [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) és [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) változókat a replikálásban.

    Ellenőrizze a Slave-kiszolgáló `sync_master_info` és `sync_relay_log_info` változóit, hogy az adatreplikáció stabil legyen, és állítsa be a változókat `1`re.
    
## <a name="other-stored-procedures"></a>Egyéb tárolt eljárások

### <a name="stop-replication"></a>Replikáció leállítása

A fő-és a replika-kiszolgáló közötti replikáció leállításához használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Replikációs kapcsolat eltávolítása

A fő-és a replika-kiszolgáló közötti kapcsolat eltávolításához használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>A replikálási hiba kihagyása

A replikálási hibák kihagyásához és a replikáció engedélyezéséhez használja a következő tárolt eljárást:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Következő lépések
További információ a Azure Database for MariaDB [felhőbe irányuló replikálásról](concepts-data-in-replication.md) .
