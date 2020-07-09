---
title: Az adatreplikálás konfigurálása – Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan állíthatja be a felhőbe irányuló replikálást a Azure Database for MariaDBban.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 6/11/2020
ms.openlocfilehash: 623c072cb8cb2c7fb1b9b6ec7d3ea661302d5e6a
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86104673"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>felhőbe irányuló replikálás konfigurálása Azure Database for MariaDB

Ez a cikk azt ismerteti, hogyan állíthatja be a [felhőbe irányuló replikálás](concepts-data-in-replication.md) a Azure Database for MariaDBban a fő-és a replika-kiszolgálók konfigurálásával. Ez a cikk azt feltételezi, hogy a MariaDB-kiszolgálókkal és-adatbázisokkal kapcsolatban némi korábbi tapasztalat van.

Ahhoz, hogy replikát hozzon létre a Azure Database for MariaDB szolgáltatásban, [felhőbe irányuló replikálás](concepts-data-in-replication.md) szinkronizálja a helyszíni, a virtuális gépeken vagy a Cloud Database Servicesben lévő fő MariaDB-kiszolgáló adatait. A beérkező adatokra épülő replikáció a MariaDB natív bináris naplójának (binlog) fájlpozíció-alapú replikációján alapul. A BinLog replikálásával kapcsolatos további tudnivalókért tekintse meg a [BinLog-replikáció áttekintése](https://mariadb.com/kb/en/library/replication-overview/)című témakört.

A jelen cikkben ismertetett lépések végrehajtása előtt tekintse át az adatok replikálásának [korlátozásait és követelményeit](concepts-data-in-replication.md#limitations-and-considerations) .

> [!NOTE]
> Ha a főkiszolgáló 10,2-es vagy újabb verziójú, javasoljuk, hogy a [globális tranzakció-azonosítóval](https://mariadb.com/kb/en/library/gtid/)állítsa be a felhőbe irányuló replikálás.


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Replikaként használandó MariaDB-kiszolgáló létrehozása

1. Hozzon létre egy új Azure Database for MariaDB-kiszolgálót (például replica.mariadb.database.azure.com). A kiszolgáló a felhőbe irányuló replikálás található replika-kiszolgáló.

    A kiszolgálók létrehozásával kapcsolatos további tudnivalókért lásd: [Azure Database for MariaDB kiszolgáló létrehozása a Azure Portal használatával](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > A Azure Database for MariaDB-kiszolgálót a általános célú vagy a memória optimalizált díjszabási szintjein kell létrehoznia.

2. Hozzon létre azonos felhasználói fiókokat és megfelelő jogosultságokat.
    
    A felhasználói fiókok nem replikálódnak a főkiszolgálóról a másodpéldány-kiszolgálóra. A replika-kiszolgálóhoz való felhasználói hozzáférés biztosításához manuálisan kell létrehoznia az összes fiókot és a megfelelő jogosultságokat az újonnan létrehozott Azure Database for MariaDB-kiszolgálón.

3. Adja hozzá a főkiszolgáló IP-címét a replika tűzfalszabályok számára. 

   A tűzfalszabályokat az [Azure Portal](howto-manage-firewall-portal.md) vagy az [Azure CLI](howto-manage-firewall-cli.md) használatával frissítheti.

> [!NOTE]
> Elfogultság – ingyenes kommunikáció
>
> A Microsoft sokféle és befogadó környezetet támogat. Ez a cikk a _Slave_kifejezésre mutató hivatkozásokat tartalmaz. Az [elfogultság nélküli kommunikációhoz használható Microsoft-stílus útmutatója](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) ezt a kizáró szót ismeri fel. A szó a jelen cikkben a konzisztencia miatt használatos, mert jelenleg a szoftverben megjelenő szó. Ha a szoftver frissítve lett a szó eltávolítására, a rendszer a cikket úgy frissíti, hogy az legyen az igazítás.
>

## <a name="configure-the-master-server"></a>A főkiszolgáló konfigurálása

A következő lépések előkészítik és konfigurálja a helyszínen üzemeltetett MariaDB-kiszolgálót egy virtuális gépen vagy egy felhőalapú adatbázis-szolgáltatásban felhőbe irányuló replikálás számára. A MariaDB-kiszolgáló a felhőbe irányuló replikálás főkiszolgálója.

1. A továbblépés előtt tekintse át a [fő kiszolgálóra vonatkozó követelményeket](concepts-data-in-replication.md#requirements) . 

   Tegyük fel például, hogy a főkiszolgáló engedélyezi mind a bejövő, mind a kimenő forgalmat az 3306-as porton, valamint azt, hogy a főkiszolgáló **nyilvános IP-címmel**rendelkezik, a DNS nyilvánosan elérhető, vagy teljes tartománynevet (FQDN) tartalmaz. 
   
   A főkiszolgálóval való kapcsolat tesztelése egy olyan eszközről való csatlakozásra tett kísérlettel, amely egy másik gépen vagy a Azure Portal elérhető [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) található.

2. A bináris naplózás bekapcsolása.
    
    Ha szeretné megtekinteni, hogy engedélyezve van-e a bináris naplózás a főkiszolgálón, írja be a következő parancsot:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Ha a változó [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) visszaadja az értéket `ON` , a bináris naplózás engedélyezve van a kiszolgálón.

   Ha `log_bin` az értéket adja vissza `OFF` , szerkessze a **My. cnf** fájlt úgy, hogy az `log_bin=ON` bekapcsolja a bináris naplózást. Indítsa újra a kiszolgálót a módosítás érvénybe léptetéséhez.

3. A főkiszolgáló beállításainak konfigurálása.

    Felhőbe irányuló replikálás megköveteli `lower_case_table_names` , hogy a paraméter konzisztens legyen a fő-és a replika-kiszolgálók között. A `lower_case_table_names` paraméter `1` alapértelmezett értéke Azure Database for MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Hozzon létre egy új replikációs szerepkört, és állítson be engedélyeket.

   Hozzon létre egy felhasználói fiókot a fő kiszolgálón, amely replikációs jogosultságokkal van konfigurálva. Az SQL-parancsok vagy a MySQL Workbench használatával hozhat létre fiókot. Ha az SSL-sel való replikálást tervezi, ezt a felhasználói fiók létrehozásakor kell megadnia.
   
   Ha szeretné megtudni, hogyan adhat hozzá felhasználói fiókokat a főkiszolgálón, tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/library/create-user/).

   Az alábbi parancsok használatával az új replikációs szerepkör bármely gépről elérheti a főkiszolgálót, nem csak a főkiszolgálót üzemeltető gépet. Ehhez a hozzáféréshez a ** \@ (z) "%" syncuser** kell megadnia a parancsban a felhasználó létrehozásához.
   
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


5. Állítsa a főkiszolgálót csak olvasható módra.

   Az adatbázisok kiírása előtt a kiszolgálót csak olvasható módban kell elhelyezni. Amíg csak olvasható módban van, a főkiszolgáló nem dolgozhat fel írási tranzakciókat. Az üzleti hatás elkerülése érdekében ütemezze a csak olvasási időszakot egy off-Peak idő alatt.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Az aktuális bináris naplófájl nevének és eltolásának beolvasása.

   Az aktuális bináris naplófájl nevének és eltolásának meghatározásához futtassa a parancsot [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) .
    
   ```sql
   show master status;
   ```
   Az eredmények az alábbi táblázathoz hasonlóak:
   
   ![Fő állapot eredményei](./media/howto-data-in-replication/masterstatus.png)

   Jegyezze fel a bináris fájl nevét, mert a későbbi lépésekben lesz használva.
   
7. A GTID pozíciójának beolvasása (nem kötelező, a GTID való replikáláshoz szükséges).

   Futtassa a függvényt a [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) megfelelő BinLog-fájlnév és ELTOLÁS GTID pozíciójának lekéréséhez.
  
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

   Két kiszolgáló összekapcsolásához és a replikáció megkezdéséhez jelentkezzen be a cél másodpéldány-kiszolgálóra az Azure DB for MariaDB szolgáltatásban. Ezután állítsa be a külső példányt főkiszolgálóként a `mysql.az_replication_change_master` vagy `mysql.az_replication_change_master_with_gtid` tárolt eljárás használatával a MariaDB-kiszolgálóhoz tartozó Azure-adatbázison.

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
   - master_log_file: bináris naplófájl neve a futtatásból`show master status`
   - master_log_pos: a bináris napló pozíciója fut`show master status`
   - master_gtid_pos: a GTID pozíciója fut`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány környezete. Ha nem használ SSL-t, adjon meg egy üres karakterláncot. *
    
    
    * Javasoljuk, hogy a master_ssl_ca paramétert változóként adja át. További információkért tekintse meg az alábbi példákat.

   **Példák**

   - Replikáció SSL használatával

       Hozza létre a változót a `@cert` következő parancsok futtatásával:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
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

   A `mysql.az_replication_start` replikálás elindításához hívja meg a tárolt eljárást.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Replikáció állapotának bejelölése.

   A [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) replikálási állapot megtekintéséhez hívja meg a parancsot a replika kiszolgálóján.
    
   ```sql
   show slave status;
   ```

   Ha `Slave_IO_Running` a és az `Slave_SQL_Running` állapotban van `yes` , és a értéke `Seconds_Behind_Master` , a `0` replikálás működik. `Seconds_Behind_Master`azt jelzi, hogy a replika milyen későn van. Ha az érték nem `0` , akkor a replika frissíti a frissítéseket.

4. Frissítse a megfelelő kiszolgálói változókat az adatreplikálás biztonságosabbá tételéhez (csak a GTID nélküli replikáláshoz szükséges).
    
    A MariaDB-ben a natív replikálás korlátozása miatt [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) a GTID-forgatókönyv nélkül kell beállítania és konfigurálnia [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) a replikálást.

    Ellenőrizze a Slave-kiszolgáló `sync_master_info` és a `sync_relay_log_info` változóit, és ellenőrizze, hogy az adatreplikáció stabil-e, és állítsa be a változókat a következőre: `1` .
    
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
