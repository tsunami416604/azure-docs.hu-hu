---
title: Az adatreplikáció konfigurálása - Azure Database for MySQL
description: Ez a cikk ismerteti, hogyan állíthatja be a Data-in Replication for Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 18c1d8b42dc73951901ec4ae9b79715ddbd47617
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474040"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Az Azure Database beállítása a MySQL data-in replikációhoz

Ez a cikk ismerteti, hogyan állíthatja be a Data-in Replication az Azure Database for MySQL a fő- és replikakiszolgálók konfigurálásával. Ez a cikk feltételezi, hogy van néhány korábbi tapasztalata a MySQL-kiszolgálókkal és adatbázisokkal.

Az Azure Database for MySQL szolgáltatás ban egy replika létrehozásához a Data-in Replication szinkronizálja az adatokat a helyszíni master MySQL-kiszolgálóról, a virtuális gépekről (VM- ek) vagy a felhőalapú adatbázis-szolgáltatásokból.

A jelen cikkben ismertetett lépések végrehajtása előtt tekintse át az adatok replikációjának [korlátait és követelményeit.](concepts-data-in-replication.md#limitations-and-considerations)

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Kópiaként használandó MySQL-kiszolgáló létrehozása

1. Új Azure-adatbázis létrehozása a MySQL-kiszolgálóhoz

   Hozzon létre egy új MySQL szerver (pl. "replica.mysql.database.azure.com"). Tekintse meg [az Azure Database létrehozása a MySQL-kiszolgálóhoz az Azure Portal használatával a](quickstart-create-mysql-server-database-using-azure-portal.md) kiszolgáló létrehozása. Ez a kiszolgáló a "replika" kiszolgáló az adatreplikációban.

   > [!IMPORTANT]
   > Az Azure Database for MySQL-kiszolgáló kell létrehozni az általános célú vagy memória optimalizált tarifacsomagok.
   > 

2. Azonos felhasználói fiókok és a megfelelő jogosultságok létrehozása

   A felhasználói fiókok nem replikálódnak a főkiszolgálóról a replikakiszolgálóra. Ha azt tervezi, hogy hozzáférést biztosít a felhasználók számára a replikakiszolgálóhoz, manuálisan kell létrehoznia az összes fiókot és a megfelelő jogosultságokat ezen az újonnan létrehozott Azure Database for MySQL-kiszolgálón.

3. Adja hozzá a főkiszolgáló IP-címét a replika tűzfalszabályaihoz. 

   A tűzfalszabályokat az [Azure Portal](howto-manage-firewall-using-portal.md) vagy az [Azure CLI](howto-manage-firewall-using-cli.md) használatával frissítheti.

## <a name="configure-the-master-server"></a>A főkiszolgáló konfigurálása
A következő lépések előkészítik és konfigurálják a helyszíni, virtuális gépen vagy más felhőszolgáltatók által az adatreplikációhoz üzemeltetett adatbázis-szolgáltatást üzemeltető MySQL-kiszolgálót. Ez a kiszolgáló a "fő" az adatközpontos replikációban.


1. A folytatás előtt tekintse át a [főkiszolgáló követelményeit.](concepts-data-in-replication.md#requirements) 

   Például győződjön meg arról, hogy a főkiszolgáló engedélyezi a bejövő és a kimenő forgalmat a 3306-os porton, és hogy a főkiszolgáló **nyilvános IP-címmel**rendelkezik, a DNS nyilvánosan elérhető, vagy teljesen minősített tartománynévvel (FQDN) rendelkezik. 
   
   Tesztelje a főkiszolgálóhoz való kapcsolódást egy másik gépen vagy az Azure Portalon elérhető [Azure Cloud Shellben](https://docs.microsoft.com/azure/cloud-shell/overview) üzemeltetett, egy eszközről, például a MySQL parancssorból való csatlakozással.

2. Bináris naplózás bekapcsolása

   Ellenőrizze, hogy engedélyezve van-e a bináris naplózás a főkiszolgálón a következő parancs futtatásával: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Ha a [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) változót "ON" értékkel adja vissza, a bináris naplózás engedélyezve van a kiszolgálón. 

   Ha `log_bin` az "OFF" értékkel adja vissza, kapcsolja be a bináris naplózást a my.cnf fájl szerkesztésével, hogy `log_bin=ON` indítsa újra a kiszolgálót, hogy a módosítás érvénybe lépjen.

3. Főkiszolgáló beállításai

   Az adatreplikációmegköveteli, `lower_case_table_names` hogy a paraméter konzisztens legyen a fő- és a replikakiszolgálók között. Ez a paraméter alapértelmezés szerint 1 az Azure Database for MySQL-ben. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Új replikációs szerepkör létrehozása és engedély beállítása

   Hozzon létre egy replikációs jogosultságokkal konfigurált felhasználói fiókot a főkiszolgálón. Ez sql parancsokkal vagy egy olyan eszközzel, mint a MySQL Workbench. Fontolja meg, hogy tervezi-e az SSL-lel történő replikálását, mivel ezt meg kell adni a felhasználó létrehozásakor. A főkiszolgálón a felhasználói [fiókok hozzáadásának](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) módjáról a MySQL dokumentációjában olvashat. 

   Az alábbi parancsokban a létrehozott új replikációs szerepkör bármely gépről hozzáférhet a főkiszolgálóhoz, nem csak a főkiszolgálót tartalmazó géphez. Ez úgy történik, hogy a "syncuser@'%'" értéket adja meg a Felhasználói létrehozás parancsban. A [fióknevek megadásáról](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)a MySQL dokumentációjában olvashat bővebben.

   **SQL parancs**

   *Replikáció SSL-lel*

   Ha az összes felhasználói kapcsolathoz SSL-t szeretne megkövetelni, a következő paranccsal hozzon létre egy felhasználót: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *SSL nélküli replikáció*

   Ha az SSL nem szükséges minden kapcsolathoz, a következő paranccsal hozzon létre egy felhasználót:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   A replikációs szerepkör létrehozásához a MySQL Workbench,nyissa meg a **Felhasználók és jogosultságok** panel a **Felügyeleti** panelen. Ezután kattintson a **Fiók hozzáadása gombra.** 
 
   ![Felhasználók és jogosultságok](./media/howto-data-in-replication/users_privileges.png)

   Írja be a felhasználónevet a **Bejelentkezési név** mezőbe. 

   ![Felhasználó szinkronizálása](./media/howto-data-in-replication/syncuser.png)
 
   Kattintson a **Felügyeleti szerepkörök** panelre, majd a **globális jogosultságok**listájában válassza a **Replikációs szolga** elemet. Ezután kattintson az **Alkalmazás gombra** a replikációs szerepkör létrehozásához.

   ![Replikációs szolga](./media/howto-data-in-replication/replicationslave.png)


5. A főkiszolgáló írásvédett üzemmódra állítása

   Az adatbázis kiírásának megkezdése előtt a kiszolgálót írásvédett módban kell elhelyezni. Írásvédett módban a főkiszolgáló nem tudja feldolgozni az írási tranzakciókat. Értékelje ki a vállalkozásra gyakorolt hatást, és szükség esetén csúcsidőn kívül ütemezze az írásvédett ablakot.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Bináris naplófájl nevének és eltolásának beolvasása

   Futtassa a [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) parancsot az aktuális bináris naplófájl nevének és eltolásának meghatározásához.
    
   ```sql
   show master status;
   ```
   Az eredményeknek olyannak kell lenniük, mint a következő. Ügyeljen arra, hogy vegye figyelembe a bináris fájl nevét, mivel a későbbi lépésekben használni fogja.

   ![Főkiszolgáló állapotának eredményei](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Főkiszolgáló kiírása és visszaállítása

1. Az összes adatbázis kiírása a főkiszolgálóról

   Használhatja mysqldump dump adatbázisok at a mester. A részleteket a [Dump & Visszaállítás .](concepts-migrate-dump-restore.md) Szükségtelen a MySQL könyvtár és a tesztkönyvtár kiírása.

2. A főkiszolgáló beállítása olvasási/írási módra

   Miután az adatbázis dömpingelt, változtassa meg a master MySQL szerver vissza olvasási / írási módban.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Memóriaképfájl visszaállítása új kiszolgálóra

   Állítsa vissza a memóriaképfájlt az Azure Database for MySQL szolgáltatásban létrehozott kiszolgálóra. A [Dump & Visszaállítás című](concepts-migrate-dump-restore.md) dokumentumban megtudhatja, hogy miként állíthatja vissza a memóriaképfájlt a MySQL-kiszolgálóra. Ha a memóriaképfájl nagy, töltse fel egy virtuális gépre az Azure-ban ugyanabban a régióban, mint a replika-kiszolgáló. Állítsa vissza az Azure Database for MySQL-kiszolgáló a virtuális gépről.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>A főkiszolgáló- és replikakiszolgálók csatolása az adatreplikáció elindításához

1. Főkiszolgáló beállítása

   A replikációs adatok összes funkciója tárolt eljárásokkal történik. Az összes eljárást megtalálja a [Tárolt adatreplikációs eljárások oldalon.](reference-data-in-stored-procedures.md) A tárolt eljárások futtathatók a MySQL rendszerhéjban vagy a MySQL Workbench-ben. 

   Két kiszolgáló összekapcsolására és a replikáció indításához jelentkezzen be a célreplika-kiszolgálóra az Azure DB for MySQL szolgáltatásban, és állítsa be a külső példányt főkiszolgálóként. Ez úgy történik, `mysql.az_replication_change_master` hogy a tárolt eljárást az Azure DB a MySQL-kiszolgáló.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: a főkiszolgáló állomásneve
   - master_user: a főkiszolgáló felhasználóneve
   - master_password: a főkiszolgáló jelszava
   - master_log_file: a bináris naplófájl neve futásból`show master status`
   - master_log_pos: a bináris napló pozíciója a futástól`show master status`
   - master_ssl_ca: A hitelesítésszolgáltatói tanúsítvány környezete. Ha nem használja az SSL-t, adja át üres karakterláncban.
       - Javasoljuk, hogy adja át ezt a paramétert, mint egy változó. További információt az alábbi példákban talál.

> [!NOTE]
> Ha a fő kiszolgáló egy Azure-beli virtuális gépen található, állítsa az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" (BE) beállítását, hogy a fő- és replikakiszolgálók kommunikálhassanak egymással. Ez a beállítás a **Kapcsolat biztonsági** beállításaiközött módosítható. További információt a [tűzfalszabályok portál használatával történő kezelése](howto-manage-firewall-using-portal.md) című dokumentumban talál.

   **Példák**

   *Replikáció SSL-lel*

   A `@cert` változó a következő MySQL parancsok futtatásával jön létre: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Az SSL-lel való replikáció a "companya.com" tartományban üzemeltetett főkiszolgáló és az Azure Database for MySQL-ben üzemeltetett replikakiszolgáló között van beállítva. Ez a tárolt eljárás a replikán fut. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *SSL nélküli replikáció*

   Az SSL nélküli replikáció a "companya.com" tartományban üzemeltetett főkiszolgáló és az Azure Database for MySQL-ben üzemeltetett replikakiszolgáló között van beállítva. Ez a tárolt eljárás a replikán fut.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Replikáció indítása

   A `mysql.az_replication_start` replikáció kezdeményezéséhez hívja meg a tárolt eljárást.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Replikáció állapotának ellenőrzése

   Hívja [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) meg a parancsot a replikakiszolgálón a replikáció állapotának megtekintéséhez.
    
   ```sql
   show slave status;
   ```

   Ha az `Slave_IO_Running` állapot `Slave_SQL_Running` és az "igen", és az értéke `Seconds_Behind_Master` "0", replikáció jól működik. `Seconds_Behind_Master`azt jelzi, hogy a replika milyen későn van. Ha az érték nem "0", az azt jelenti, hogy a replika frissítéseket dolgoz fel. 

## <a name="other-stored-procedures"></a>Egyéb tárolt eljárások

### <a name="stop-replication"></a>Replikáció leállítása

A főkiszolgáló és a replikakiszolgáló közötti replikáció leállításához kövesse a következő tárolt eljárást:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Replikációs kapcsolat eltávolítása

A főkiszolgáló és a replikakiszolgáló közötti kapcsolat eltávolításához kövesse a következő tárolt eljárást:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Replikációs hiba kihagyása

A replikációs hiba kihagyása és a replikáció folytatásának engedélyezése a következő tárolt eljárással:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>További lépések
- További információ az Azure Database for MySQL [adatreplikációjáról.](concepts-data-in-replication.md) 
