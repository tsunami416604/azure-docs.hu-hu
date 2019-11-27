---
title: Kapcsolódás Azure Database for MySQLhoz átirányítás használatával
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja az alkalmazást, hogy a Azure Database for MySQLhoz kapcsolódjon az átirányítás használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: eed52c444bbcad60b4e629cf476c285f53c9f515
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233739"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Kapcsolódás Azure Database for MySQLhoz átirányítás használatával

Ez a témakör azt ismerteti, hogyan lehet egy alkalmazást Azure Database for MySQL-kiszolgálót átirányítási móddal összekapcsolni. Az átirányítás célja az ügyfélalkalmazások és a MySQL-kiszolgálók közötti hálózati késés csökkentése azáltal, hogy az alkalmazások közvetlenül a háttérbeli kiszolgáló csomópontjaihoz csatlakoznak.

> [!IMPORTANT]
> Az átirányítás támogatása a PHP- [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) jelenleg előzetes verzióban érhető el.

## <a name="before-you-begin"></a>Előkészületek
Jelentkezzen be az [Azure Portal](https://portal.azure.com). Azure Database for MySQL-kiszolgáló létrehozása a 5,6, 5,7 vagy 8,0 verziójú motorral. Részletekért lásd: [Azure Database for MySQL kiszolgáló létrehozása a portálról](quickstart-create-mysql-server-database-using-azure-portal.md) , vagy [Azure Database for MySQL kiszolgáló létrehozása a parancssori felület használatával](quickstart-create-mysql-server-database-using-azure-cli.md).

Az átirányítás jelenleg csak akkor támogatott, ha az SSL engedélyezve van. Az SSL konfigurálásával kapcsolatos további információkért lásd: [az SSL használata a Azure Database for MySQL használatával](https://docs.microsoft.com/azure/mysql/howto-configure-ssl#step-3-enforcing-ssl-connections-in-azure). 

## <a name="php"></a>PHP

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Előfeltételek 
- PHP-verziók 7.2.15 + és 7.3.2 +
- PHP PEAR 
- PHP – MySQL
- Azure Database for MySQL-kiszolgáló SSL-kompatibilis

1. Telepítse a [mysqlnd_azuret](https://github.com/microsoft/mysqlnd_azure) a [PECL](https://pecl.php.net/package/mysqlnd_azure)-mel.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Keresse meg a kiterjesztési könyvtárat (`extension_dir`) a következő futtatásával:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Módosítsa a könyvtárakat a visszaadott mappába, és győződjön meg arról, `mysqlnd_azure.so` ebben a mappában található. 

4. Keresse meg az. ini fájlok mappáját az alábbi futtatásával: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Módosítsa a visszaadott mappába a címtárakat. 

6. Hozzon létre egy új. ini fájlt a `mysqlnd_azure`hoz. Győződjön meg arról, hogy a név ABC-sorrendje a mysqnld után van-e, mivel a modulok az ini-fájlok neve szerint vannak betöltve. Ha például a `mysqlnd`. ini fájl neve `10-mysqlnd.ini`, a mysqlnd ini nevet adja meg `20-mysqlnd-azure.ini`ként.

7. Az új. ini fájlban adja hozzá a következő sorokat az átirányítás engedélyezéséhez.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enabled=on
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Előfeltételek 
- PHP-verziók 7.2.15 + és 7.3.2 +
- PHP – MySQL
- Azure Database for MySQL-kiszolgáló SSL-kompatibilis

1. A következő parancs futtatásával állapítsa meg, hogy a PHP x64-es vagy x86-os verzióját futtatja-e:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Töltse le a [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) dll megfelelő x64-es vagy x86-os verzióját a [PECL](https://pecl.php.net/package/mysqlnd_azure) -ből, amely megfelel a PHP-verziójának. 

3. Bontsa ki a zip-fájlt, és keresse meg `php_mysqlnd_azure.dll`nevű DLL-t.

4. Keresse meg a kiterjesztési könyvtárat (`extension_dir`) az alábbi parancs futtatásával:

    ```cmd
    php -i | find "extension_dir"s
    ```

5. Másolja a `php_mysqlnd_azure.dll` fájlt a 4. lépésben visszaadott könyvtárba. 

6. Keresse meg a `php.ini` fájlt tartalmazó PHP-mappát a következő paranccsal:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Módosítsa a `php.ini` fájlt, és adja hozzá a következő extra sorokat az átirányítás engedélyezéséhez. 

    A dinamikus bővítmények szakaszban: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    A modul beállításai szakaszban:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enabled=on
    ```

### <a name="confirm-redirection"></a>Átirányítás megerősítése

Azt is megerősítheti, hogy az átirányítás az alábbi minta PHP-kóddal van konfigurálva. Hozzon létre egy `mysqlConnect.php` nevű PHP-fájlt, és illessze be az alábbi kódot. Frissítse a kiszolgálónevet, a felhasználónevet és a jelszót a saját nevével. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enabled: ", ini_get("mysqlnd_azure.enabled") == true?"On":"Off", "\n";
  $db = mysqli_init();
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
$db->close();
  }
 ?>
 ```

## <a name="next-steps"></a>Következő lépések
A kapcsolódási karakterláncokkal kapcsolatos további információkért tekintse meg a [kapcsolódási karakterláncokat](howto-connection-string.md).

