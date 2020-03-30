---
title: Az adatreplikáció konfigurálása – Azure-adatbázis a MariaDB-hez
description: Ez a cikk bemutatja, hogyan állíthatja be az adatok replikációját a MariaDB Azure Database-ben.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 51b800dde140affd222f2bdb341c0fbf3a57d8cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530155"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Az adatok replikációjának konfigurálása a MariaDB Azure-adatbázisában

Ez a cikk bemutatja, hogyan állíthatja be az adatok replikációját az Azure Database for MariaDB-ben a fő- és replikakiszolgálók konfigurálásával. Ez a cikk feltételezi, hogy van néhány korábbi tapasztalata a MariaDB-kiszolgálókkal és -adatbázisokkal.

Az Azure Database for MariaDB szolgáltatás ban egy replika létrehozásához a Data-in Replication szinkronizálja az adatokat a fő MariaDB-kiszolgálóról a helyszínen, a virtuális gépeken (VMs) vagy a felhőalapú adatbázis-szolgáltatásokban.

> [!NOTE]
> Ha a főkiszolgáló 10.2-es vagy újabb verziójú, azt javasoljuk, hogy globális [tranzakcióazonosítóval](https://mariadb.com/kb/en/library/gtid/)állítsa be az Adatreplikációt.


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Kópiaként használandó MariaDB-kiszolgáló létrehozása

1. Hozzon létre egy új Azure-adatbázist a MariaDB-kiszolgálóhoz (például replica.mariadb.database.azure.com). A kiszolgáló a replikálási kiszolgáló az adatreplikációban.

    A kiszolgálók létrehozásáról az [Azure Database for MariaDB-kiszolgáló létrehozása az Azure Portal használatával című témakörben](quickstart-create-mariadb-server-database-using-azure-portal.md)olvashat.

   > [!IMPORTANT]
   > Létre kell hoznia az Azure Database for MariaDB-kiszolgálót az általános cél vagy a memória optimalizált tarifacsomagokban.

2. Hozzon létre azonos felhasználói fiókokat és a megfelelő jogosultságokat.
    
    A felhasználói fiókok nem replikálódnak a főkiszolgálóról a replikakiszolgálóra. A replikakiszolgáló felhasználói hozzáférésének biztosításához manuálisan kell létrehoznia az összes fiókot és a megfelelő jogosultságokat az újonnan létrehozott Azure Database for MariaDB-kiszolgálón.

## <a name="configure-the-master-server"></a>A főkiszolgáló konfigurálása

A következő lépések előkészítik és konfigurálják a mariadb-kiszolgálót, amely a helyszínen, a virtuális gépben vagy egy felhőalapú adatbázis-szolgáltatásban üzemelteti az adatreplikációt. A MariaDB-kiszolgáló az adatreplikáció főkiszolgálója.

1. Kapcsolja be a bináris naplózást.
    
    Ha meg szeretné tudni, hogy engedélyezve van-e a bináris naplózás a főoldalon, írja be a következő parancsot:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Ha a [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) változó `ON`az értéket adja vissza, a bináris naplózás engedélyezve van a kiszolgálón.

   Ha `log_bin` az `OFF`értéket adja vissza, akkor szerkesztheti `log_bin=ON` a **my.cnf** fájlt úgy, hogy bekapcsolja a bináris naplózást. A módosítás életbe léptetéséhez indítsa újra a kiszolgálót.

2. A főkiszolgáló beállításainak konfigurálása.

    Az adatreplikáció megköveteli, `lower_case_table_names` hogy a paraméter konzisztens legyen a fő- és a replikakiszolgálók között. A `lower_case_table_names` paraméter `1` alapértelmezés szerint a MariaDB Azure Database-ben van beállítva.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Hozzon létre egy új replikációs szerepkört, és állítsa be az engedélyeket.

   Hozzon létre egy felhasználói fiókot a replikációs jogosultságokkal konfigurált főkiszolgálón. Létrehozhat egy fiókot SQL-parancsokkal vagy a MySQL Workbench használatával. Ha SSL-lel szeretne replikálni, ezt a felhasználói fiók létrehozásakor meg kell adnia.
   
   Ha tudni szeretné, hogyan vehet fel felhasználói fiókokat a főkiszolgálóra, olvassa el a [MariaDB dokumentációját.](https://mariadb.com/kb/en/library/create-user/)

   A következő parancsok használatával az új replikációs szerepkör bármely gépről elérheti a főkiszolgálót, nem csak a főkiszolgálót tartalmazó gépről. Ehhez a hozzáféréshez adja meg a **(%'\@szinkronizálási felhasználót** a parancsban a felhasználó létrehozásához).
   
   A MariaDB dokumentációjáról a [Fióknevek megadása](https://mariadb.com/kb/en/library/create-user/#account-names)..

   **SQL parancs**

   - Replikáció SSL-lel

       Ha az összes felhasználói kapcsolathoz SSL-t szeretne megkövetelni, írja be a következő parancsot a felhasználó létrehozásához:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - SSL nélküli replikáció

       Ha az SSL nem szükséges minden kapcsolathoz, írja be a következő parancsot a felhasználó létrehozásához:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   A replikációs szerepkör létrehozásához a MySQL Workbench alkalmazásban a **Kezelés** ablaktáblán válassza a **Felhasználók és jogosultságok**lehetőséget. Ezután válassza **a Fiók hozzáadása**lehetőséget.
 
   ![Felhasználók és jogosultságok](./media/howto-data-in-replication/users_privileges.png)

   Írjon be egy felhasználónevet a **Bejelentkezési név** mezőbe.

   ![Felhasználó szinkronizálása](./media/howto-data-in-replication/syncuser.png)
 
   Jelölje ki a **Felügyeleti szerepkörök** panelt, majd a **globális jogosultságok**listájában válassza a **Replikációs szolga**lehetőséget. A replikációs szerepkör létrehozásához válassza az **Alkalmaz** lehetőséget.

   ![Replikációs szolga](./media/howto-data-in-replication/replicationslave.png)


4. Állítsa a főkiszolgálót írásvédett módba.

   Az adatbázis kiírása előtt a kiszolgálót írásvédett módban kell elhelyezni. Írásvédett módban a főkiszolgáló nem tudja feldolgozni az írási tranzakciókat. Az üzleti hatások elkerülése érdekében ütemezze az írásvédett ablakot csúcsidőn kívül.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Az aktuális bináris naplófájl nevének és eltolásának beolvasása.

   Az aktuális bináris naplófájl nevének és [`show master status`](https://mariadb.com/kb/en/library/show-master-status/)eltolásának meghatározásához futtassa a parancsot.
    
   ```sql
   show master status;
   ```
   Az eredményeknek hasonlónak kell lenniük a következő táblázathoz:
   
   ![Főkiszolgáló állapotának eredményei](./media/howto-data-in-replication/masterstatus.png)

   Jegyezze fel a bináris fájl nevét, mert a későbbi lépésekben fogja használni.
   
6. Szerezd meg a GTID pozíciót (nem kötelező, a GTID-vel történő replikációhoz szükséges).

   Futtassa [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) a függvényt a megfelelő binlog fájlnév és eltolás GTID pozíciójának lehívásához.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>A főkiszolgáló kiírása és visszaállítása

1. Az összes adatbázis kiírása a főkiszolgálóról.

   A mysqldump segítségével a főkiszolgálóról kikérdezésre az összes adatbázist kiszeretné önteni. Nem szükséges a MySQL könyvtár és a tesztkönyvtár kiírása.

    További információt a [Kiírás és visszaállítás](howto-migrate-dump-restore.md)című témakörben talál.

2. Állítsa a főkiszolgálót olvasási/írási módra.

   Az adatbázis kiírása után módosítsa a fő MariaDB-kiszolgálót olvasási/írási módra.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Állítsa vissza a memóriaképfájlt az új kiszolgálóra.

   Állítsa vissza a memóriaképfájlt az Azure Database for MariaDB szolgáltatásban létrehozott kiszolgálóra. A [Kiírás & visszaállítása](howto-migrate-dump-restore.md) a Kiírásfájl MariaDB-kiszolgálóra való visszaállításáról.

   Ha a memóriaképfájl nagy, töltse fel egy virtuális gépre az Azure-ban, ugyanabban a régióban, mint a replika-kiszolgáló. Állítsa vissza az Azure Database for MariaDB-kiszolgálóa virtuális gépről.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>A fő- és a replikakiszolgálók csatolása az adatreplikáció elindításához

1. Állítsa be a főkiszolgálót.

   A replikációs adatok összes funkciója tárolt eljárásokkal történik. Az összes eljárást megtalálja a [Tárolt adatreplikációs eljárások oldalon.](reference-data-in-stored-procedures.md) A tárolt eljárások futtathatók a MySQL rendszerhéjban vagy a MySQL Workbench-ben.

   Két kiszolgáló összekapcsolására és a replikáció indítására jelentkezzen be a cél replikakiszolgálóra az Azure DB for MariaDB szolgáltatásban. Ezután állítsa be a külső példányt `mysql.az_replication_change_master` `mysql.az_replication_change_master_with_gtid` főkiszolgálóként az Azure DB mariaDB-kiszolgálón az Azure DB-kiszolgálón tárolt eljárással.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   vagy
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: a főkiszolgáló állomásneve
   - master_user: a főkiszolgáló felhasználóneve
   - master_password: a főkiszolgáló jelszava
   - master_log_file: a bináris naplófájl neve futásból`show master status`
   - master_log_pos: a bináris napló pozíciója a futástól`show master status`
   - master_gtid_pos: GTID pozíció futása`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: A hitelesítésszolgáltatói tanúsítvány környezete. Ha nem SSL-t használ, adja át üres karakterláncot.*
    
    
    *Azt javasoljuk, hogy a master_ssl_ca paramétert változóként adja át. További információt az alábbi példákban talál.

   **Példák**

   - Replikáció SSL-lel

       Hozza létre `@cert` a változót a következő parancsok futtatásával:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Az SSL-lel való replikáció a tartományban üzemeltetett companya.com és a MariaDB Azure Database-ben üzemeltetett replikakiszolgáló között van beállítva. Ez a tárolt eljárás a replikán fut.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - SSL nélküli replikáció

       Az SSL nélküli replikáció a tartományban üzemeltetett companya.com és a MariaDB Azure Database-ben üzemeltetett replikakiszolgáló között van beállítva. Ez a tárolt eljárás a replikán fut.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Indítsa el a replikációt.

   A `mysql.az_replication_start` replikáció elindításához hívja meg a tárolt eljárást.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Ellenőrizze a replikáció állapotát.

   Hívja [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) meg a parancsot a replikakiszolgálón a replikáció állapotának megtekintéséhez.
    
   ```sql
   show slave status;
   ```

   Ha `Slave_IO_Running` `Slave_SQL_Running` és az `yes`állapot , és `Seconds_Behind_Master` `0`az értéke, replikáció működik. `Seconds_Behind_Master`azt jelzi, hogy a replika milyen későn van. Ha az érték `0`nem, akkor a replika a frissítések feldolgozása.

4. Frissítse a megfelelő kiszolgálóváltozókat, hogy biztonságosabbá tegye az adatreplikációt (csak a GTID nélküli replikációhoz szükséges).
    
    A MariaDB natív replikációs korlátozása [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) miatt a GTID-forgatókönyv nélkül kell beállítania a replikáción és a változókat.

    Ellenőrizze a slave `sync_master_info` kiszolgáló `sync_relay_log_info` és a változók, hogy az adat-a replikáció `1`stabil, és állítsa a változók .
    
## <a name="other-stored-procedures"></a>Egyéb tárolt eljárások

### <a name="stop-replication"></a>Replikáció leállítása

A főkiszolgáló és a replikakiszolgáló közötti replikáció leállításához kövesse a következő tárolt eljárást:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>A replikációs kapcsolat eltávolítása

A főkiszolgáló és a replikakiszolgáló közötti kapcsolat eltávolításához kövesse a következő tárolt eljárást:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>A replikációs hiba kihagyása

A replikációs hiba kihagyásához és a replikáció engedélyezéséhez kövesse a következő tárolt eljárást:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>További lépések
További információ az Azure Database for MariaDB [adatreplikációjáról.](concepts-data-in-replication.md)
