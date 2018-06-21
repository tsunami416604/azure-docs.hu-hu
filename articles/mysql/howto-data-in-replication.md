---
title: Adatok a replikáció replikálják az adatokat az Azure-adatbázisba a MySQL konfigurálása.
description: A cikkből megtudhatja, hogyan állíthat be adatok a replikáció az Azure Database MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/20/2018
ms.openlocfilehash: e099597eae419653a2a40c7f01ee7abbbc4657f0
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294421"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>A MySQL-adatok a replikáció Azure-adatbázis konfigurálása

Ebben a cikkben, megtudhatja, hogyan állíthatja be az Azure-adatbázishoz a MySQL-szolgáltatás az adatok a replikáció az elsődleges és a replika kiszolgálók konfigurálásával. Adatok a replikáció szinkronizálja az adatokat a helyszíni futó virtuális gépek vagy adatbázis-szolgáltatások üzemeltetője más szolgáltatók, az Azure-adatbázishoz a MySQL-szolgáltatás egy replika az elsődleges MySQL kiszolgálóról teszi lehetővé. 

Ez a cikk feltételezi, hogy rendelkezik-e legalább némi tapasztalattal a MySQL-kiszolgálók és adatbázisok.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>A MySQL-kiszolgáló replikaként használni létrehozása

1. MySQL-kiszolgáló új Azure-adatbázis létrehozása

   Hozzon létre egy új MySQL-kiszolgálót (például) "replica.mysql.database.azure.com"). Tekintse meg [egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis létrehozása az Azure-portál használatával](quickstart-create-mysql-server-database-using-azure-portal.md) a kiszolgáló létrehozásához. Ez a kiszolgáló az a "replika" kiszolgáló adatok a replikáció.

   > [!IMPORTANT]
   > A MySQL-kiszolgálóhoz tartozó Azure-adatbázis az általános célú vagy Memóriaoptimalizált tarifacsomagok kell létrehozni.
   > 

2. Azonos felhasználói fiókok és a megfelelő jogosultságok létrehozása

   Felhasználói fiókok nem replikálódnak az elsődleges kiszolgálóról a replikakiszolgálóra. Ha tervezi az adatbázisreplika-kiszolgálón való hozzáférés biztosítása a felhasználóknak, kell manuálisan létrehoznia fiókok és a megfelelő jogosultságokkal az újonnan létrehozott MySQL-kiszolgáló Azure-adatbázis.

## <a name="configure-the-primary-server"></a>Az elsődleges kiszolgáló konfigurálása
Az alábbi lépéseket előkészítése és a MySQL üzemeltetett server helyszíni, konfigurálja a virtuális gépet, vagy egyéb adatok a replikáció szolgáltatók által üzemeltetett adatbázis-szolgáltatás. A kiszolgáló nem az "elsődleges" adatok a replikáció. 

1. Bináris naplózás bekapcsolása

   Ellenőrizze, hogy ha bináris naplózás engedélyezve van az elsődleges a következő parancs futtatásával: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Ha a változó [ `log_bin` ](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) lett visszaadva az érték "ON", bináris naplózás engedélyezve van a kiszolgálón. 

   Ha `log_bin` van az "OFF" érték lett visszaadva, kapcsolja be a bináris, ezért a my.cnf fájl szerkesztésével naplózás, amely `log_bin=ON` és indítsa újra a kiszolgálót a változtatás érvénybe léptetéséhez.

2. Elsődleges kiszolgáló beállításai

   Adatok a replikáció paraméter szükséges `lower_case_table_names` összhangban az elsődleges és a replika között kell lennie. Ez a paraméter értéke 1 alapértelmezés szerint a MySQL az Azure-adatbázis. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Hozzon létre egy új replikációs szerepkört, és állítsa be az engedélyt

   Az elsődleges kiszolgálón konfigurált replikációs jogosultságokkal rendelkező felhasználói fiók létrehozása. Ez végezhető el az SQL-parancsok vagy egy eszköz, például a MySQL-munkaterületet. Vegye figyelembe, hogy tervezi, ez lesz meg kell adni a felhasználó létrehozásakor SSL végez replikációt. Tekintse meg a MySQL dokumentációját tudni, hogyan [felhasználói fiókok hozzáadása](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) az elsődleges kiszolgálón. 

   Az alábbi parancsokat a létrehozott új replikációs a szerepe, fér hozzá az elsődleges a gépi, nem csak a üzemeltető magát az elsődleges gép. Ezt úgy teheti meg "syncuser@'%" "a Létrehozás felhasználói parancsban. A MySQL dokumentációjában olvashat [számítógépfiók-nevét megadó](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **SQL-parancs**

   *SSL-replikációt*

   SSL megkövetelése minden felhasználói kapcsolat, a következő paranccsal egy felhasználó létrehozásához: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *SSL nélküli replikációs*

   Ha SSL nincs szükség az összes kapcsolat, a következő paranccsal egy felhasználó létrehozásához:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL-munkaterület**

   A replikációs szerepkör MySQL munkaterület létrehozásához nyissa meg a **felhasználók és jogosultságok** a panel a **felügyeleti** panel. Kattintson a **fiók hozzáadása**. 
 
   ![Felhasználók és jogosultságok](./media/howto-data-in-replication/users_privileges.png)

   Írja be a felhasználónevet a **bejelentkezési név** mező. 

   ![Szinkronizálási felhasználó](./media/howto-data-in-replication/syncuser.png)
 
   Kattintson a a **rendszergazdai szerepkörei** panelen, majd válassza ki **replikációs alárendelt** közül **globális jogosultságokkal**. Kattintson a **alkalmaz** létrehozni a replikációs szerepkört.

   ![Replikációs alárendelt](./media/howto-data-in-replication/replicationslave.png)


4. Csak olvasható módra az elsődleges kiszolgáló beállítása

   Ki az adatbázis kiírása megkezdése előtt el kell helyezni, csak olvasható módban kell a kiszolgálót. Csak olvasható módban, az elsődleges nem képes feldolgozni írási tranzakciók. Az üzlet értékeli, és a csak olvasható ablak ütemezési csúcsidőn kívüli időpontot szükség esetén.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Bináris naplófájl nevét és az eltolás

   Futtassa a [ `show master status` ](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) parancsot annak meghatározásához, a jelenlegi bináris naplófájl nevét és az eltolás.
    
   ```sql
   show master status;
   ```
   Az eredmények hasonló kell lennie. Ügyeljen arra, hogy a későbbi lépésekben fog használni, vegye figyelembe a bináris fájl nevét.

   ![Fő állapotinformáció](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-primary-server"></a>Memóriakép és az elsődleges kiszolgáló helyreállítása

1. Összes adatbázis elsődleges kiszolgálóról dump

   Memóriakép adatbázisok mysqldump segítségével az elsődleges adatbázisból. További információkért tekintse meg [Dump & visszaállítása](concepts-migrate-dump-restore.md). Nem szükséges dump MySQL könyvtár és a teszt-erőforrástár.

2. Olvasási/írási módban elsődleges kiszolgáló beállítása

   Az adatbázis tartalmaz lett kiírt, módosíthatja az elsődleges kiszolgáló ismét MySQL olvasási/írási módban.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Új server kiírt fájlok visszaállítása

   A memóriakép visszaállítása a kiszolgálóra, hozza létre az Azure-adatbázisban a MySQL-szolgáltatás. Tekintse meg [Dump & visszaállítása](concepts-migrate-dump-restore.md) memóriaképfájl visszaállítása a MySQL-kiszolgáló számára. Ha a biztonsági másolat fájl túl nagy, töltse fel a egy virtuális gép az ugyanabban a régióban, mint a replikakiszolgáló az Azure-ban. Visszaállítja a MySQL-kiszolgálóhoz tartozó Azure-adatbázis a virtuális gépről.

## <a name="link-primary-and-replica-servers-to-start-data-in-replication"></a>A kapcsolat elsődleges és a replikakiszolgáló adatainak a replikálását indító

1. Elsődleges kiszolgáló beállítása

   Minden replikációs adatokat a funkciók által tárolt eljárások történik. Található összes eljárást, [adatok a replikálási tárolt eljárások](reference-data-in-stored-procedures.md). A tárolt eljárások a MySQL rendszerhéj vagy a MySQL munkaterület is futtatható. 

   Segítségével kapcsolja össze a két kiszolgáló, és indítsa el a replikációt, jelentkezzen be a cél replikakiszolgáló, az az Azure-Adatbázisba, a MySQL-szolgáltatás, és a külső példány állítja be az elsődleges kiszolgáló. Ehhez használja a `mysql.az_replication_change_primary` tárolt eljárást az az Azure-adatbázis MySQL-kiszolgáló.

   ```sql
   CALL mysql.az_replication_change_primary('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: az elsődleges kiszolgáló állomásnevét
   - master_user: felhasználónév, az elsődleges kiszolgáló
   - master_password: elsődleges kiszolgáló jelszava
   - master_log_file: futtatását bináris naplófájl neve `show master status`
   - master_log_pos: futtatását bináris napló pozíciója `show master status`
   - master_ssl_ca: Hitelesítésszolgáltatói tanúsítványt a környezetben. Ha nem használ SSL, adjon át üres karakterlánc.
       - Javasoljuk, hogy ez a paraméter átadására változóként. A következő példák további információt.

   **Példák**

   *SSL-replikációt*

   A változó `@cert` jön létre a következő MySQL-parancsok futtatásával: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Egy elsődleges kiszolgáló, a tartomány "companya.com" üzemeltetett és MySQL az Azure-adatbázisban tárolt replikakiszolgáló közötti replikáció SSL be van állítva. A tárolt eljárás futása a replikán. 

   ```sql
   CALL mysql.az_replication_change_primary('primary.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *SSL nélküli replikációs*

   Egy elsődleges kiszolgáló, a tartomány "companya.com" üzemeltetett és MySQL az Azure-adatbázisban tárolt replikakiszolgáló közötti replikáció SSL nélküli be van állítva. A tárolt eljárás futása a replikán.

   ```sql
   CALL mysql.az_replication_change_primary('primary.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

2. Indítsa el a replikációt

   Hívja a `mysql.az_replication_start` tárolt eljárás replikáció elindítására.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Replikációs állapotának ellenőrzése

   Hívja a [ `show slave status` ](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) a replikakiszolgálón a replikációs állapot parancsot.
    
   ```sql
   show slave status;
   ```

   Ha a állapotának `Slave_IO_Running` és `Slave_SQL_Running` "yes" és az értékének `Seconds_Behind_Master` "0", a replikálás működik jól. `Seconds_Behind_Master` azt jelzi, hogyan késői a replikát. Ha az érték nem "0", az azt jelenti, hogy a replika frissítések feldolgozása. 

## <a name="other-stored-procedures"></a>Más tárolt eljárások

### <a name="stop-replication"></a>Replikáció leállítása

Állítsa le a replikációt az elsődleges és a replika-kiszolgáló között, használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Távolítsa el a replikációs kapcsolat

Az elsődleges és replika kiszolgáló közötti kapcsolat eltávolításához használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_remove_primary;
```

### <a name="skip-replication-error"></a>Hagyja ki a replikációs hiba

Egy replikációs hiba kihagyásához és a replikáció folytatja, a következő tárolt eljárás használata:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>További lépések
- További információ [adatok a replikáció](concepts-data-in-replication.md) MySQL az Azure-adatbázishoz. 