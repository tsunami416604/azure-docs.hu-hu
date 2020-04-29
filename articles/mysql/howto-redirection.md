---
title: Kapcsolat átirányítással – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja az alkalmazást, hogy a Azure Database for MySQLhoz kapcsolódjon az átirányítás használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246335"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Kapcsolódás Azure Database for MySQLhoz átirányítás használatával

Ez a témakör azt ismerteti, hogyan lehet egy alkalmazást Azure Database for MySQL-kiszolgálót átirányítási móddal összekapcsolni. Az átirányítás célja az ügyfélalkalmazások és a MySQL-kiszolgálók közötti hálózati késés csökkentése azáltal, hogy az alkalmazások közvetlenül a háttérbeli kiszolgáló csomópontjaihoz csatlakoznak.

## <a name="before-you-begin"></a>Előkészületek
Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Azure Database for MySQL-kiszolgáló létrehozása a 5,6, 5,7 vagy 8,0 verziójú motorral. Részletekért lásd: [Azure Database for MySQL kiszolgáló létrehozása a portálról](quickstart-create-mysql-server-database-using-azure-portal.md) , vagy [Azure Database for MySQL kiszolgáló létrehozása a parancssori felület használatával](quickstart-create-mysql-server-database-using-azure-cli.md).

Az átirányítás jelenleg csak akkor támogatott, ha az **SSL engedélyezve van** a Azure Database for MySQL kiszolgálón. Az SSL konfigurálásával kapcsolatos további információkért lásd: [az SSL használata a Azure Database for MySQL használatával](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure).

## <a name="php"></a>PHP

A PHP-alkalmazások átirányításának támogatása a Microsoft által fejlesztett [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -bővítményen keresztül érhető el. 

A mysqlnd_azure bővítmény elérhető a PHP-alkalmazások számára a PECL-n keresztül, és erősen ajánlott a bővítmény telepítése és konfigurálása a hivatalosan közzétett [PECL-csomagon](https://pecl.php.net/package/mysqlnd_azure)keresztül.

> [!IMPORTANT]
> Az átirányítás támogatása a PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) bővítményben jelenleg előzetes verzióban érhető el.

### <a name="redirection-logic"></a>Átirányítási logika

>[!IMPORTANT]
> Az átirányítási logika/viselkedés elején lévő 1.1.0-es verziójának frissítése megtörtént, és a **1.1.0 + használata javasolt**.

Az átirányítási viselkedést a értéke határozza meg `mysqlnd_azure.enableRedirect`. Az alábbi táblázat az átirányítás viselkedését ismerteti a paraméternek a **1.1.0 + verziótól**kezdődő értéke alapján.

Ha a mysqlnd_azure bővítmény (1.0.0-1.0.3 verzió) régebbi verzióját használja, az átirányítási viselkedést a értéke határozza meg `mysqlnd_azure.enabled`. Az érvényes értékek `off` (az alábbi táblázatban szereplő viselkedéssel hasonlóan viselkednek), és `on` (az alábbi táblázat szerint viselkednek `preferred` ).  

|**mysqlnd_azure. enableRedirect érték**| **Viselkedés**|
|----------------------------------------|-------------|
|`off` vagy `0`|Az átirányítás nem lesz használatban. |
|`on` vagy `1`|-Ha az SSL nincs engedélyezve a Azure Database for MySQL-kiszolgálón, akkor nem történik kapcsolat. A rendszer a következő hibaüzenetet adja vissza: *"mysqlnd_azure. enableRedirect, de az SSL-beállítás nincs beállítva a kapcsolati karakterláncban. Az átirányítás csak SSL használatával lehetséges. "*<br>– Ha az SSL engedélyezve van a MySQL-kiszolgálón, de az átirányítás nem támogatott a kiszolgálón, az első kapcsolat megszakad, és a rendszer a következő hibaüzenetet adja vissza: *"a kapcsolat megszakadt, mert az átirányítás nincs engedélyezve a MySQL-kiszolgálón, vagy a hálózati csomag nem felel meg az átirányítási protokollnak."*<br>-Ha a MySQL-kiszolgáló támogatja az átirányítás használatát, de az átirányított csatlakozás valamilyen okból meghiúsult, akkor az első proxy-csatlakozást is megszakítja. Az átirányított kapcsolatok hibájának visszaadása.|
|`preferred` vagy `2`<br> (alapértelmezett érték)|– Ha lehetséges, a mysqlnd_azure átirányítást fog használni.<br>– Ha a kapcsolat nem használ SSL-t, a kiszolgáló nem támogatja az átirányítást, vagy az átirányított kapcsolat nem tud csatlakozni a nem végzetes okból, amíg a proxy kapcsolata még érvényes, akkor az az első proxy kapcsolatra fog visszatérni.|

A dokumentum következő fejezetei azt ismertetik, hogyan kell telepíteni a `mysqlnd_azure` bővítményt a PECL használatával, és beállítani a paraméter értékét.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Előfeltételek 
- PHP-verziók 7.2.15 + és 7.3.2 +
- PHP PEAR 
- PHP – MySQL
- Azure Database for MySQL-kiszolgáló SSL-kompatibilis

1. Telepítse a [mysqlnd_azuret](https://github.com/microsoft/mysqlnd_azure) a [PECL](https://pecl.php.net/package/mysqlnd_azure)-mel. Azt javasoljuk, hogy a 1.1.0 + verziót használja.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Keresse meg a kiterjesztés könyvtárát (`extension_dir`) a következő futtatásával:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Módosítsa a könyvtárakat a visszaadott mappába `mysqlnd_azure.so` , és győződjön meg róla, hogy a mappában található. 

4. Keresse meg az. ini fájlok mappáját az alábbi futtatásával: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Módosítsa a visszaadott mappába a címtárakat. 

6. Hozzon létre egy új. ini `mysqlnd_azure`fájlt a következőhöz:. Győződjön meg arról, hogy a név ABC-sorrendje a mysqnld után van-e, mivel a modulok az ini-fájlok neve szerint vannak betöltve. Ha `mysqlnd` például az. ini nevet kapta `10-mysqlnd.ini`, nevezze el a mysqlnd ini- `20-mysqlnd-azure.ini`t a következőként:.

7. Az új. ini fájlban adja hozzá a következő sorokat az átirányítás engedélyezéséhez.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
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

2. Töltse le a [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) dll megfelelő x64-es vagy x86-os verzióját a [PECL](https://pecl.php.net/package/mysqlnd_azure) -ből, amely megfelel a PHP-verziójának. Azt javasoljuk, hogy a 1.1.0 + verziót használja.

3. Bontsa ki a zip-fájlt, és `php_mysqlnd_azure.dll`keresse meg a nevű DLL-t.

4. Keresse meg a kiterjesztés könyvtárát (`extension_dir`) az alábbi parancs futtatásával:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Másolja a `php_mysqlnd_azure.dll` fájlt a 4. lépésben visszaadott könyvtárba. 

6. Keresse meg a `php.ini` fájlt tartalmazó php-mappát a következő parancs használatával:

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
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Átirányítás megerősítése

Azt is megerősítheti, hogy az átirányítás az alábbi minta PHP-kóddal van konfigurálva. Hozzon létre egy nevű `mysqlConnect.php` php-fájlt, és illessze be az alábbi kódot. Frissítse a kiszolgálónevet, a felhasználónevet és a jelszót a saját nevével. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
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

## <a name="next-steps"></a>További lépések
További információ a kapcsolatok karakterláncáról: a [kapcsolatok karakterláncai](howto-connection-string.md).
