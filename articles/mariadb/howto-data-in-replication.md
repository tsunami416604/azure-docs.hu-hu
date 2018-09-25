---
title: MariaDB-hez készült Azure Database-be replikálják az adatokat az adatok a replikáció konfigurálása.
description: Ez a cikk ismerteti, hogyan állítható be adatokat a replikáció az Azure Database for MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 13d1353b0f8b3320972973d428f4aedb0fb68ac8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990688"
---
# <a name="how-to-configure-azure-database-for-mariadb-data-in-replication"></a>Azure Database MariaDB-adatok a replikáció konfigurálása

Ebben a cikkben megtudhatja, hogyan állítható be adatok a replikáció az Azure Database for MariaDB-szolgáltatás által a master és a replika kiszolgálók konfigurálása az. Adatok a replikáció lehetővé teszi, hogy a fő MariaDB-kiszolgáló, a helyszínen futó virtuális gépeket vagy adatbázis-szolgáltatások az Azure Database for MariaDB szolgáltatás egy replika az egyéb felhőszolgáltatók által üzemeltetett adatai szinkronizálva. 

Ez a cikk azt feltételezi, hogy legalább némi tapasztalattal a MariaDB-kiszolgálókra és adatbázisokra.

## <a name="create-a-mariadb-server-to-be-used-as-replica"></a>MariaDB-kiszolgáló replikaként használni létrehozása

1. Hozzon létre egy új, Azure Database for MariaDB-kiszolgáló

   Hozzon létre egy új MariaDB-kiszolgálót (például. "replica.mariadb.database.azure.com"). Tekintse meg [egy Azure Database for MariaDB-kiszolgáló létrehozása az Azure portal használatával](quickstart-create-mariadb-server-database-using-azure-portal.md) a kiszolgáló létrehozásához. Ez a kiszolgáló pedig a "" replikakiszolgáló adatok a replikáció.

   > [!IMPORTANT]
   > Az általános célú és memóriahasználatra optimalizált tarifacsomagok az Azure Database for MariaDB-kiszolgálón kell létrehozni.
   > 

2. Azonos felhasználói fiókok és a megfelelő jogosultságok létrehozása

   Felhasználói fiókok nem lesznek replikálva a fő kiszolgálóról a replikakiszolgálóra. Ha azt tervezi, hogy az adatbázisreplika-kiszolgálón való hozzáférést biztosítanak a felhasználók számára, manuálisan hozzon létre az újonnan létrehozott Azure Database for MariaDB-kiszolgáló a fiókok és a megfelelő jogosultságokat szeretne.

## <a name="configure-the-master-server"></a>A fölérendelt kiszolgáló konfigurálása
Az alábbi lépéseket előkészítése, és konfigurálja a MariaDB futó kiszolgálót a helyszínen, a virtuális gépet, vagy az adatok a replikáció egyéb felhőszolgáltatók által üzemeltetett adatbázis-szolgáltatás. A kiszolgáló nem a replikációs adatok a "master". 

1. Bináris naplózás bekapcsolása

   Ellenőrizze, hogy ha a bináris naplózás engedélyezve van a a fő a következő parancs futtatásával: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Ha a változó [ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) adja vissza "Be" értéket, a bináris naplózás engedélyezve van a kiszolgálón. 

   Ha `log_bin` van hibát adott vissza az érték "OFF", kapcsolja be a bináris naplózás, ezért a my.cnf fájl szerkesztésével, amely `log_bin=ON` , és indítsa újra a kiszolgálót, a módosítás érvénybe léptetéséhez.

2. Fő kiszolgáló beállításai

   Adatok a replikációhoz az szükséges, a paraméter `lower_case_table_names` összhangban a master és a replika között kell lennie. Ez a paraméter értéke 1, Azure Database for MariaDB alapértelmezés szerint. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Hozzon létre egy új replikációs szerepkör és az engedélyek beállítása

   A fő kiszolgálón konfigurált replikációs jogosultságokkal rendelkező felhasználói fiók létrehozása. Ezt az SQL-parancsokat vagy egy eszköz, például a MySQL Workbench segítségével teheti meg. Vegye figyelembe, hogy azt tervezi, hogy SSL-lel való replikálásához, ezt is meg kell adni, ha a felhasználó létrehozása. A MariaDB dokumentációjában tudni, hogyan [felhasználói fiókokat felvenni](https://mariadb.com/kb/en/library/create-user/) a fölérendelt kiszolgálón. 

   Az alábbi parancsokat a létrehozott új replikációs szerepkör minden olyan gép, nem csak magát a fő futtató géphez, a fő elérhetik. Ez történik, megadásával "syncuser@'%" "a Létrehozás felhasználói parancsban. A MariaDB dokumentációjában talál további információkat talál [nevének megadásával](https://mariadb.com/kb/en/library/create-user/#account-names).

   **SQL-parancs**

   *Replikációs SSL-lel*

   SSL megkövetelése minden felhasználói kapcsolat, hozzon létre egy felhasználót a következő parancs segítségével: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *SSL nélküli replikációs*

   Ha az SSL nem szükséges az összes kapcsolat, a következő parancs segítségével hozzon létre egy felhasználót:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **A MySQL Workbench**

   A MySQL Workbench a replikációs szerepkör létrehozásához nyissa meg a **felhasználók és jogosultságok** a panelen a **felügyeleti** panel. Kattintson a **fiók hozzáadása**. 
 
   ![Felhasználók és jogosultságok](./media/howto-data-in-replication/users_privileges.png)

   Írja be a felhasználónevet, a **bejelentkezési név** mező. 

   ![Felhasználó szinkronizálása](./media/howto-data-in-replication/syncuser.png)
 
   Kattintson a a **rendszergazdai szerepkörök** panelen, majd **replikációs alárendelt** listájából **globális jogosultságokkal**. Kattintson a **alkalmaz** a replikációs szerepkör létrehozásához.

   ![Replikációs alárendelt](./media/howto-data-in-replication/replicationslave.png)


4. A fölérendelt kiszolgáló csak olvasható módra beállítva

   Mielőtt hozzákezdene kiírása ki az adatbázist, a kiszolgálónak van szüksége, csak olvasható módban kell helyezni. Csak olvasható módban, a fő lesz írási tranzakciók feldolgozása nem sikerült. Elemezheti a hatását, az üzleti, és a csak olvasható ablak ütemezheti a csúcsidőszakon, ha szükséges.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Bináris naplófájl nevét és eltolása

   Futtassa a [ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/) parancsot annak meghatározásához, a jelenlegi bináris naplófájl nevét és eltolását.
    
   ```sql
   show master status;
   ```
   Az eredmények hasonló kell lennie. Mindenképpen jegyezze fel a bináris fájl neve, a későbbi lépésekben fog használni.

   ![Fő állapotinformáció](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Memóriakép és a fölérendelt kiszolgáló visszaállítása

1. Minden adatbázis főkiszolgálóról memóriakép

   Memóriakép adatbázisok mysqldump segítségével a főágból. Részletekért tekintse meg a [memóriakép és visszaállítás](howto-migrate-dump-restore.md). Nem szükséges MySQL könyvtár dump és a teszt-erőforrástár.

2. Írási/olvasási mód fő kiszolgáló beállítása

   Miután az adatbázis rendelkezik lett kiírt, módosítsa a fő MariaDB kiszolgáló újra az olvasási/írási módban.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Visszaállítás új kiszolgálóra memóriakép-fájl

   Állítsa vissza a memóriakép-fájl az Azure Database for MariaDB-szolgáltatás a kiszolgálójához. Tekintse meg [memóriakép és visszaállítás](howto-migrate-dump-restore.md) a memóriakép-fájl visszaállítása a MariaDB-kiszolgálóhoz. Ha a memóriakép-fájl túl nagy, töltse fel az Azure-ban és az adatbázisreplika-kiszolgáló ugyanabban a régióban lévő virtuális géphez. Állítsa vissza azt az Azure Database for MariaDB-kiszolgáló a virtuális gépről.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Adatok a replikáció a master és a replika kiszolgálók összekapcsolása

1. Fő kiszolgáló beállítása

   Minden replikációs adatokat a függvény a tárolt eljárásokat kell elvégeznie. Található összes eljárást annak [adatokat a replikálási tárolt eljárások](reference-data-in-stored-procedures.md). A tárolt eljárások a MySQL-rendszerhéj vagy a MySQL Workbench környezetben is futtatható.

   Két kiszolgálók összekapcsolása, és indítsa el a replikációt, jelentkezzen be a cél replikakiszolgáló MariaDB szolgáltatás az Azure-DB-ben, és a külső példány állítja be a főkiszolgáló. Ennek segítségével történik a `mysql.az_replication_change_master` tárolt eljárást az Azure DB for MariaDB-kiszolgáló.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: a fölérendelt kiszolgáló állomásnevét
   - master_user: a főkiszolgáló felhasználónevét
   - master_password: a főkiszolgáló jelszavát
   - master_log_file: futását bináris naplófájl neve `show master status`
   - master_log_pos: futását bináris naplójának pozíciója `show master status`
   - master_ssl_ca: Hitelesítésszolgáltatói tanúsítvány környezetben. Ha nem használ SSL, üres karakterláncot adja át.
       - Javasoljuk, hogy ezt a paramétert a átadhatja egy változóként. Az alábbiakban talál további információt.

   **Példák**

   *Replikációs SSL-lel*

   A változó `@cert` jön létre a következő parancsok futtatásával: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   SSL-lel való replikáció be van állítva a főkiszolgálóval a "companya.com" tartományban lévő üzemeltetett és a egy MariaDB-hez készült Azure Database-ben üzemeltetett adatbázisreplika-kiszolgáló között. Ez a tárolt eljárás futtatása a replikán. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
   ```
   *SSL nélküli replikációs*

   A "companya.com" tartományban lévő üzemeltetett főkiszolgálóvá és a egy MariaDB-hez készült Azure Database-ben üzemeltetett adatbázisreplika-kiszolgáló közötti SSL nélküli replikációs be van állítva. Ez a tárolt eljárás futtatása a replikán.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
   ```

2. Indítsa el a replikációt

   Hívja a `mysql.az_replication_start` tárolt eljárás replikáció kezdeményezéséhez.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Replikációs állapot ellenőrzése

   Hívja a [ `show slave status` ](https://mariadb.com/kb/en/library/show-slave-status/) a replikakiszolgálón a replikációs állapot megtekintéséről parancsot.
    
   ```sql
   show slave status;
   ```

   Ha állapotát `Slave_IO_Running` és `Slave_SQL_Running` "yes" és az értékét `Seconds_Behind_Master` "0", a replikálás működik jól. `Seconds_Behind_Master` azt jelzi, hogyan késői a replikát. Ha az értéke nem "0", az azt jelenti, hogy a replika frissítések feldolgozása. 

## <a name="other-stored-procedures"></a>Más tárolt eljárások

### <a name="stop-replication"></a>Replikáció leállítása

A fő és az adatbázisreplika-kiszolgáló közötti replikáció megszüntetéséhez használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>A replikálási kapcsolat eltávolítása

A master és a replika-kiszolgáló közötti kapcsolat eltávolításához használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Hagyja ki a replikációs hiba

Egy replikációs hiba kihagyásához és a replikáció folytatja, használja a következő tárolt eljárást:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [adatok a replikáció](concepts-data-in-replication.md) az Azure Database for MariaDB.