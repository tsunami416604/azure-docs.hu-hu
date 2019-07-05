---
title: Adatok a replikáció konfigurálása az Azure-adatbázisban a MariaDB-hez |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan állítható be adatok a replikáció az Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 21e8a88cc6f03b4d54a6c5299b0b6be36cc32d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444805"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Adatok a replikáció konfigurálása az Azure-adatbázisban a MariaDB-hez

Ez a cikk ismerteti, hogyan állítható be adatok a replikáció az Azure Database for MariaDB a master és a replika kiszolgálók konfigurálásával. Ez a cikk feltételezi, hogy rendelkezik némi tapasztalattal a MariaDB-kiszolgálókra és adatbázisokra.

Az Azure Database for MariaDB-szolgáltatás a replika létrehozásához, adatok a replikáció szinkronizálja az adatokat egy fő MariaDB server helyszíni, virtuális gépek (VM), vagy a felhőbeli adatbázis-szolgáltatások.

> [!NOTE]
> Ha a fölérendelt kiszolgáló 10,2 vagy újabb verziója, azt javasoljuk, hogy az adatok a replikáció beállítása használatával [globális Tranzakcióazonosító](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>A replikaként használni MariaDB-kiszolgáló létrehozása

1. Hozzon létre egy új, Azure Database for MariaDB-kiszolgáló (például replica.mariadb.database.azure.com). A kiszolgáló pedig a replikakiszolgáló, az adatok a replikáció.

    Kiszolgáló létrehozása kapcsolatos további információkért lásd: [egy Azure Database for MariaDB-kiszolgáló létrehozása az Azure portal használatával](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Az általános célú és memóriahasználatra optimalizált tarifacsomagok kell létrehoznia az Azure Database for MariaDB-kiszolgáló.

2. Hozzon létre azonos felhasználói fiókok és a megfelelő jogosultságokat.
    
    Felhasználói fiókokat a rendszer nem replikálja a fő kiszolgálóról a replikakiszolgálóra. A felhasználói hozzáférést az adatbázisreplika-kiszolgálót, manuálisan kell létrehoznia fiókok és a megfelelő jogosultságokkal az újonnan létrehozott Azure Database for MariaDB-kiszolgálót a.

## <a name="configure-the-master-server"></a>A fölérendelt kiszolgáló konfigurálása

Az alábbi lépéseket előkészítése, és konfigurálja a MariaDB futó kiszolgálót a helyszínen, egy virtuális Gépet, vagy a felhőalapú adatbázis-szolgáltatás számára az adatok a replikáció. A MariaDB-kiszolgáló az adatok a replikáció a fő.

1. Kapcsolja be a bináris naplózás.
    
    Szeretné látni, ha a bináris naplózás engedélyezve van-e meg a főkiszolgáló, adja meg a következő parancsot:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Ha a változó [ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) értékét adja vissza `ON`, bináris naplózás engedélyezve van a kiszolgálón.

   Ha `log_bin` értékét adja vissza `OFF`, szerkesztheti a **my.cnf** fájlt úgy, hogy `log_bin=ON` bináris naplózás bekapcsolása. Indítsa újra a kiszolgálót, a módosítás érvénybe léptetéséhez.

2. Fő kiszolgáló beállításainak konfigurálása.

    Adatok a replikációhoz az szükséges, a paraméter `lower_case_table_names` összhangban a master és a replika között kell lennie. A `lower_case_table_names` paraméter értéke `1` MariaDB-hez készült Azure Database-ben alapértelmezés szerint.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Hozzon létre egy új replikációs szerepkört, és állítsa be az engedélyeket.

   A fő kiszolgálón konfigurált replikációs jogosultságokkal rendelkező felhasználói fiók létrehozása. Létrehozhat egy fiókot az SQL-parancsokat vagy a MySQL Workbench használatával. Ha azt tervezi, SSL-lel való replikálása, adjon meg ezt a felhasználói fiók létrehozásakor.
   
   Megtudhatja, hogyan adhat hozzá felhasználói fiókokat a fő kiszolgálón, tekintse meg a [MariaDB dokumentáció](https://mariadb.com/kb/en/library/create-user/).

   A következő parancsokkal, az új replikációs szerepkör hozzáférhet a fő bármely gépről, nem csak magát a fő futtató géphez. Adja meg a hozzáférést, **syncuser\@(%)** hozzon létre egy felhasználót a parancsban.
   
   MariaDB-dokumentációval kapcsolatos további tudnivalókért lásd: [nevének megadásával](https://mariadb.com/kb/en/library/create-user/#account-names).

   **SQL-parancs**

   - Replikációs SSL-lel

       Minden felhasználói kapcsolat SSL használatának megkövetelését, adja meg a felhasználó létrehozásához a következő parancsot:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - SSL nélküli replikációs

       Ha SSL nem szükséges az összes kapcsolat, adja meg a felhasználó létrehozásához a következő parancsot:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   A MySQL Workbench, a replikációs szerepkör létrehozása a a **felügyeleti** ablaktáblán válassza előbb **felhasználók és jogosultságok**. Válassza ki **fiók hozzáadása**.
 
   ![Felhasználók és jogosultságok](./media/howto-data-in-replication/users_privileges.png)

   Adjon meg egy felhasználónevet a **bejelentkezési név** mező.

   ![Felhasználó szinkronizálása](./media/howto-data-in-replication/syncuser.png)
 
   Válassza ki a **rendszergazdai szerepkörök** panelen, majd listáját az **globális jogosultságokkal**, jelölje be **replikációs alárendelt**. Válassza ki **alkalmaz** a replikációs szerepkör létrehozásához.

   ![Replikációs alárendelt](./media/howto-data-in-replication/replicationslave.png)


4. A fölérendelt kiszolgáló csak olvasható módba állítani.

   Mielőtt, dump egy adatbázist, a kiszolgáló csak olvasható módban kell elhelyezni. Csak olvasható módban, a fő írási tranzakciók nem tudja feldolgozni. Üzletmenetre gyakorolt hatás elkerülése érdekében a csak olvasható ablak ütemezése egy csúcsidőn kívüli időszakban.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Töltse le a jelenlegi bináris naplófájl nevét és eltolását.

   Az aktuális bináris naplófájl nevét és az eltolás megállapításához futtassa a parancsot [ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/).
    
   ```sql
   show master status;
   ```
   Az eredmények az alábbi táblázat hasonló lesz:
   
   ![Fő állapotinformáció](./media/howto-data-in-replication/masterstatus.png)

   Vegye figyelembe a bináris fájl nevét, mert a későbbi lépésekben fog használni.
   
6. (Nem kötelező, replikáció GTID a szükséges), első GTID pozíciója.

   A függvény futtatása [ `BINLOG_GTID_POS` ](https://mariadb.com/kb/en/library/binlog_gtid_pos/) GTID pozíciója lekérje a megfelelő binlog fájl neve és eltolását.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Memóriakép és visszaállítás a fölérendelt kiszolgáló

1. Memóriakép a fölérendelt kiszolgáló az összes adatbázishoz.

   Mysqldump használatával kiírása a fölérendelt kiszolgáló az összes adatbázishoz. Nem szükséges a MySQL-tár dump és a teszt-erőforrástár.

    További információkért lásd: [memóriakép és visszaállítás](howto-migrate-dump-restore.md).

2. Állítsa be a fölérendelt kiszolgáló az olvasási/írási módban.

   Után az adatbázis rendelkezik lett kiírt, módosíthatja a fő MariaDB kiszolgáló újra az olvasási/írási módban.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Állítsa vissza a memóriakép-fájl az új kiszolgálóra.

   Állítsa vissza a memóriakép-fájl az Azure Database for MariaDB-szolgáltatás a kiszolgálójához. Lásd: [memóriakép és visszaállítás](howto-migrate-dump-restore.md) a memóriakép-fájl visszaállítása a MariaDB-kiszolgálóhoz.

   Ha a memóriakép-fájl túl nagy, töltse fel az Azure-beli virtuális géphez belül és az adatbázisreplika-kiszolgáló ugyanabban a régióban. Állítsa vissza azt az Azure Database for MariaDB-kiszolgáló a virtuális gépről.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Adatok a replikáció a master és a replika kiszolgálók összekapcsolása

1. Állítsa be a főkiszolgáló.

   Minden replikációs adatokat a függvény a tárolt eljárásokat kell elvégeznie. Található összes eljárást annak [adatokat a replikálási tárolt eljárások](reference-data-in-stored-procedures.md). Tárolt eljárások a MySQL-rendszerhéj vagy a MySQL Workbench környezetben is futtatható.

   Két kiszolgálók összekapcsolása, és indítsa el a replikációt, jelentkezzen be a cél replikakiszolgáló MariaDB szolgáltatás az Azure-DB-ben. Ezután beállítani a külső példány a fölérendelt kiszolgáló segítségével a `mysql.az_replication_change_master` vagy `mysql.az_replication_change_master_with_gtid` tárolt eljárást az Azure DB for MariaDB-kiszolgáló.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   vagy
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: hostname of the master server
   - master_user: a főkiszolgáló felhasználónevét
   - master_password: a főkiszolgáló jelszavát
   - master_log_file: futását bináris naplófájl neve `show master status`
   - master_log_pos: futását bináris naplójának pozíciója `show master status`
   - master_gtid_pos: Futását GTID pozíciója `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: Hitelesítésszolgáltatói tanúsítvány környezet. Ha nem használ SSL, adja át egy üres string.*
    
    
    \* Azt javasoljuk, hogy a master_ssl_ca paraméter megadásának változóként. További információkért lásd az alábbi példákat.

   **Példák**

   - Replikációs SSL-lel

       Hozza létre a változót `@cert` a következő parancsok futtatásával:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       A tartomány companya.com lévő üzemeltetett főkiszolgálóvá, és a egy MariaDB-hez készült Azure Database-ben üzemeltetett adatbázisreplika-kiszolgáló közötti SSL-lel való replikáció van beállítva. Ez a tárolt eljárás futtatása a replikán.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - SSL nélküli replikációs

       A tartomány companya.com lévő üzemeltetett főkiszolgálóvá, és a egy MariaDB-hez készült Azure Database-ben üzemeltetett adatbázisreplika-kiszolgáló közötti SSL nélküli replikációs be van állítva. Ez a tárolt eljárás futtatása a replikán.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Indítsa el a replikációt.

   Hívja a `mysql.az_replication_start` tárolt eljárás replikáció.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Ellenőrizze a replikációs állapotát.

   Hívja a [ `show slave status` ](https://mariadb.com/kb/en/library/show-slave-status/) a replikakiszolgálón a replikációs állapot megtekintéséről parancsot.
    
   ```sql
   show slave status;
   ```

   Ha `Slave_IO_Running` és `Slave_SQL_Running` állapotban vannak `yes`, és az értéke `Seconds_Behind_Master` van `0`, a replikálás működik. `Seconds_Behind_Master` azt jelzi, hogyan késői a replikát. Ha az érték nem `0`, majd a replika frissítések feldolgozása folyamatban van.

4. Frissítse a megfelelő kiszolgálói változókat, hogy az adatok a replikáció biztonságosabb (csak a replikáció GTID nélkül szükséges).
    
    A MariaDB natív replikációs korlátozásai miatt be kell állítani [ `sync_master_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) és [ `sync_relay_log_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) változók a replikáció a GTID nélkül.

    Ellenőrizze az alárendelt kiszolgáló `sync_master_info` és `sync_relay_log_info` változókat, hogy az adatok a replikáció stabil, és állítsa a változókat `1`.
    
## <a name="other-stored-procedures"></a>Más tárolt eljárások

### <a name="stop-replication"></a>Replikáció leállítása

A fő és az adatbázisreplika-kiszolgáló közötti replikáció megszüntetéséhez használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>A replikációs kapcsolat eltávolítása

A fő és az adatbázisreplika-kiszolgáló közötti kapcsolat eltávolításához használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Hagyja ki a replikációs hiba

Egy replikációs hiba kihagyásához és a replikáció, használja a következő tárolt eljárást:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>További lépések
Tudjon meg többet [adatok a replikáció](concepts-data-in-replication.md) az Azure Database for MariaDB.
