---
title: Az adatreplikálás konfigurálása – Azure Database for MySQL
description: Ez a cikk a Azure Database for MySQL felhőbe irányuló replikálás beállítását ismerteti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 8/7/2020
ms.openlocfilehash: f745e5e8b611271be9dff2131a2079abc609cf91
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539061"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Azure Database for MySQL konfigurálása felhőbe irányuló replikálás

Ez a cikk azt ismerteti, hogyan állíthatja be a Azure Database for MySQL [felhőbe irányuló replikálás](concepts-data-in-replication.md) a forrás-és a replika-kiszolgálók konfigurálásával. Ez a cikk azt feltételezi, hogy a MySQL-kiszolgálókkal és-adatbázisokkal kapcsolatos korábbi tapasztalatokkal rendelkezik.

> [!NOTE]
> Elfogultság – ingyenes kommunikáció
>
> A Microsoft sokféle és befogadó környezetet támogat. Ez a cikk a _Slave_kifejezésre mutató hivatkozásokat tartalmaz. Az [elfogultság nélküli kommunikációhoz használható Microsoft-stílus útmutatója](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) ezt a kizáró szót ismeri fel. A szó a jelen cikkben a konzisztencia miatt használatos, mert jelenleg a szoftverben megjelenő szó. Ha a szoftver frissítve lett a szó eltávolítására, a rendszer a cikket úgy frissíti, hogy az legyen az igazítás.
>

Ahhoz, hogy replikát hozzon létre a Azure Database for MySQL szolgáltatásban, [felhőbe irányuló replikálás](concepts-data-in-replication.md)  szinkronizálja az adatokat egy helyszíni MySQL-kiszolgálóról, a virtuális gépekről vagy a Felhőbeli adatbázis-szolgáltatásokból. A beérkező adatokra épülő replikáció a MySQL natív bináris naplójának (binlog) fájlpozíció-alapú replikációján alapul. A BinLog-replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL BinLog-replikáció áttekintése](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)című témakört.

A jelen cikkben ismertetett lépések végrehajtása előtt tekintse át az adatok replikálásának [korlátozásait és követelményeit](concepts-data-in-replication.md#limitations-and-considerations) .

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Replikaként használandó MySQL-kiszolgáló létrehozása

1. Új Azure Database for MySQL-kiszolgáló létrehozása

   Hozzon létre egy új MySQL-kiszolgálót (pl. "replica.mysql.database.azure.com"). A kiszolgáló létrehozásához [a Azure Portal használatával Azure Database for MySQL kiszolgáló létrehozása](quickstart-create-mysql-server-database-using-azure-portal.md) című témakörben talál további információt. Ez a kiszolgáló a felhőbe irányuló replikálás replika-kiszolgálója.

   > [!IMPORTANT]
   > A Azure Database for MySQL-kiszolgálót a általános célú vagy a memória optimalizált díjszabási szintjein kell létrehozni.
   > 

1. Azonos felhasználói fiókok és megfelelő jogosultságok létrehozása

   A felhasználói fiókok nem replikálódnak a forráskiszolgálóról a másodpéldány-kiszolgálóra. Ha azt tervezi, hogy hozzáférést biztosít a felhasználóknak a másodpéldány-kiszolgálóhoz, manuálisan kell létrehoznia az összes fiókot és a hozzá tartozó jogosultságokat az újonnan létrehozott Azure Database for MySQL-kiszolgálón.

1. Adja hozzá a forráskiszolgáló IP-címét a replika tűzfalszabály-szabályaihoz. 

   A tűzfalszabályokat az [Azure Portallal](howto-manage-firewall-using-portal.md) vagy az [Azure CLI-vel](howto-manage-firewall-using-cli.md) frissítheti.

## <a name="configure-the-source-server"></a>A forráskiszolgáló konfigurálása
Az alábbi lépéseket követve elkészítheti és konfigurálhatja a helyszínen üzemeltetett MySQL-kiszolgálót egy virtuális gépen vagy más felhőalapú szolgáltató által a felhőbe irányuló replikálás számára üzemeltetett adatbázis-szolgáltatásban. Ez a kiszolgáló a "Master" az adatok replikálásához.


1. A továbblépés előtt tekintse át a [fő kiszolgálóra vonatkozó követelményeket](concepts-data-in-replication.md#requirements) . 

   Ügyeljen például arra, hogy a forráskiszolgáló engedélyezze a bejövő és kimenő forgalmat is a 3306-as porton, valamint azt, hogy a forráskiszolgáló **nyilvános IP-címmel**rendelkezik, a DNS nyilvánosan elérhető, vagy teljes TARTOMÁNYNEVE (FQDN). 
   
   Tesztelje a kapcsolatot a forráskiszolgálóról egy olyan eszközről való csatlakozásra tett kísérlettel, amely egy másik gépen vagy a Azure Portal elérhető [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) .

1. Bináris naplózás bekapcsolása

   A következő parancs futtatásával ellenőrizze, hogy engedélyezve van-e a bináris naplózás a forráson: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Ha a változót [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) "on" értékre adja vissza, a bináris naplózás engedélyezve van a kiszolgálón. 

   Ha a `log_bin` értéket "off" értékkel adja vissza, kapcsolja be a bináris naplózást a saját. cnf fájl szerkesztésével, `log_bin=ON` és indítsa újra a kiszolgálót a módosítás érvénybe léptetéséhez.

1. Forráskiszolgáló beállításai

   Felhőbe irányuló replikálás megköveteli `lower_case_table_names` , hogy a paraméter konzisztens legyen a forrás-és a replika-kiszolgálók között. Ez a paraméter a Azure Database for MySQL alapértelmezés szerint 1. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

1. Új replikációs szerepkör létrehozása és az engedélyek beállítása

   Hozzon létre egy felhasználói fiókot a forráskiszolgálón, amely replikációs jogosultságokkal van konfigurálva. Ez az SQL-parancsokkal vagy egy olyan eszközzel végezhető el, mint például a MySQL Workbench. Gondolja át, hogy az SSL-sel való replikálást tervezi-e, mivel ezt a felhasználó létrehozásakor meg kell adni. Tekintse át a MySQL dokumentációját, hogy megtudja, hogyan [adhat hozzá felhasználói fiókokat](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) a forráskiszolgálón. 

   Az alábbi parancsokban a létrehozott új replikációs szerepkör bármely gépről képes hozzáférni a forráshoz, nem csak a forrást futtató gépről. Ezt a "syncuser@"% "beállítás megadásával teheti meg a felhasználó létrehozása parancsban. A [fiókok nevének megadásával](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)kapcsolatos további információkért tekintse meg a MySQL dokumentációját.

   **SQL-parancs**

   *Replikáció SSL használatával*

   Az SSL minden felhasználói kapcsolathoz való megköveteléséhez használja a következő parancsot egy felhasználó létrehozásához: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikáció SSL nélkül*

   Ha az SSL nem szükséges minden kapcsolathoz, használja a következő parancsot egy felhasználó létrehozásához:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   A MySQL Workbench replikációs szerepkörének létrehozásához nyissa meg a **felhasználók és jogosultságok** panelt a **felügyeleti** panelen. Ezután kattintson a **fiók hozzáadása**lehetőségre. 
 
   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="Felhasználók és jogosultságok":::

   Írja be a felhasználónevet a **bejelentkezési név** mezőbe. 

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="Felhasználók és jogosultságok":::
 
   Kattintson a **felügyeleti szerepkörök** panelre, majd válassza ki a **replikálás Slave** elemet a **globális jogosultságok**listájáról. Ezután kattintson az **Apply (alkalmaz** ) gombra a replikációs szerepkör létrehozásához.

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="Felhasználók és jogosultságok":::

1. A forráskiszolgáló beállítása írásvédett módra

   Az adatbázis kiírásának megkezdése előtt a kiszolgálót csak olvasható módban kell elhelyezni. Amíg csak olvasható módban van, a forrás nem tudja feldolgozni az írási tranzakciókat. Értékelje ki a vállalatra gyakorolt hatást, és szükség esetén ütemezze a csak olvasási időszakot.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

1. Bináris naplófájl nevének és eltolásának beolvasása

   Futtassa a [` show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) parancsot az aktuális bináris naplófájl nevének és eltolásának meghatározásához.
    
   ```sql
    show master status;
   ```
   Az eredménynek a következőhöz hasonlónak kell lennie. Ügyeljen arra, hogy a bináris fájl nevét jegyezze fel, mivel a későbbi lépésekben lesz használva.

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="Felhasználók és jogosultságok":::
 
## <a name="dump-and-restore-source-server"></a>Forráskiszolgáló kiírása és visszaállítása

1. Határozza meg, hogy mely adatbázisokat és táblákat kívánja replikálni Azure Database for MySQLba, és végezze el a memóriaképet a forráskiszolgálóról.
 
    A mysqldump segítségével kitörölheti az adatbázisokat a főkiszolgálóról. Részletekért tekintse meg a következőt: [Dump & Restore](concepts-migrate-dump-restore.md). A MySQL-függvénytár és a tesztelési könyvtár kiírása nem szükséges.

1. Forráskiszolgáló beállítása olvasási/írási módra

   Miután megtörtént az adatbázis kiírása, állítsa vissza a forrás MySQL-kiszolgálót olvasási/írási módba.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

1. Memóriakép-fájl visszaállítása új kiszolgálóra

   Állítsa vissza a memóriakép-fájlt a Azure Database for MySQL szolgáltatásban létrehozott kiszolgálóra. A dump-fájlok MySQL-kiszolgálóra való visszaállításával kapcsolatban tekintse meg a [dump & Restore](concepts-migrate-dump-restore.md) című témakört. Ha a Memóriakép fájlja nagyméretű, töltse fel az Azure-beli virtuális gépre a replika-kiszolgálóval megegyező régión belül. Állítsa vissza a Azure Database for MySQL-kiszolgálóra a virtuális gépről.

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Forrás-és replika-kiszolgálók csatolása a felhőbe irányuló replikálás indításához

1. Forráskiszolgáló beállítása

   Az összes felhőbe irányuló replikálás függvényt tárolt eljárások hajtják végre. Az összes eljárást [felhőbe irányuló replikálás tárolt eljárásokban](reference-data-in-stored-procedures.md)találja. A tárolt eljárások a MySQL-rendszerhéjban vagy a MySQL Workbenchben is futtathatók. 

   Két kiszolgáló összekapcsolásához és a replikáció megkezdéséhez jelentkezzen be a cél replikát futtató kiszolgálóra az Azure DB for MySQL szolgáltatásban, és állítsa be a külső példányt forráskiszolgálóként. Ezt a `mysql.az_replication_change_master` tárolt eljárással teheti meg a MySQL-kiszolgálóhoz készült Azure-adatbázison.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: a forráskiszolgáló állomásneve
   - master_user: a forráskiszolgáló felhasználóneve
   - master_password: a forráskiszolgáló jelszava
   - master_log_file: bináris naplófájl neve a futtatásból `show master status`
   - master_log_pos: a bináris napló pozíciója fut `show master status`
   - master_ssl_ca: HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány környezete. Ha nem használ SSL-t, adja át az üres karakterláncot.
       - Azt javasoljuk, hogy ezt a paramétert változóként adja át. További információért tekintse meg az alábbi példákat.

   > [!NOTE]
   > Ha a forráskiszolgáló egy Azure-beli virtuális gépen található, állítsa "be az Azure-szolgáltatásokhoz való hozzáférés engedélyezése" lehetőséget, hogy a forrás-és a replika-kiszolgálók kommunikálhassanak egymással. Ez a beállítás a **kapcsolatok biztonsági** beállításaiból módosítható. További információt a [Tűzfalszabályok kezelése a portál használatával](howto-manage-firewall-using-portal.md) című témakörben talál.
      
   **Példák**
   
   *Replikáció SSL használatával*
   
   A változó `@cert` létrehozásához futtassa a következő MySQL-parancsokat: 
   
      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```
   
   Az SSL-sel történő replikáció beállítása a "companya.com" tartományban üzemeltetett forráskiszolgáló és a Azure Database for MySQLban üzemeltetett replika-kiszolgáló között történik. Ez a tárolt eljárás fut a replikán. 
   
      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```
   *Replikáció SSL nélkül*
   
   Az SSL nélküli replikáció beállítása a (z) "companya.com" tartományba tartozó forráskiszolgáló és a Azure Database for MySQL rendszerben üzemeltetett replika-kiszolgáló között történik. Ez a tárolt eljárás fut a replikán.
   
      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

1. Szűrés 
 
   Ha ki szeretné hagyni egyes táblák replikálását a főkiszolgálóról, frissítse a `replicate_wild_ignore_table` kiszolgálói paramétert a másodpéldány-kiszolgálón. Vesszővel tagolt lista használatával több táblázatos mintát is megadhat.

   A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) . 
    
   A paraméter frissítéséhez használhatja a [Azure Portal](howto-server-parameters.md) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md)-t.

1. Replikáció indítása

   A `mysql.az_replication_start` replikáció indításához hívja meg a tárolt eljárást.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Replikáció állapotának megtekintése

   A [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) replikálási állapot megtekintéséhez hívja meg a parancsot a replika kiszolgálóján.
    
   ```sql
   show slave status;
   ```

   Ha az állapota `Slave_IO_Running` és `Slave_SQL_Running` a értéke "igen", és a "0" értékre van állítva `Seconds_Behind_Master` , a replikálás jól működik. `Seconds_Behind_Master` azt jelzi, hogy a replika milyen későn van. Ha az érték nem "0", az azt jelenti, hogy a replika frissítéseket dolgoz fel. 

## <a name="other-stored-procedures"></a>Egyéb tárolt eljárások

### <a name="stop-replication"></a>Replikáció leállítása

A forrás-és a replika kiszolgáló közötti replikáció leállításához használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Replikációs kapcsolat eltávolítása

A forrás-és a replika kiszolgáló közötti kapcsolat eltávolításához használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Replikálási hiba kihagyása

A replikálási hibák kihagyásához és a replikálás folytatásához használja a következő tárolt eljárást:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>További lépések
- További információ a Azure Database for MySQL [felhőbe irányuló replikálásról](concepts-data-in-replication.md) . 
