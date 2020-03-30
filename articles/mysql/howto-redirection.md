---
title: Csatlakozás átirányításhoz - Azure Database for MySQL
description: Ez a cikk bemutatja, hogyan konfigurálhatja az alkalmazást, hogy az átirányítással csatlakozzon az Azure Database for MySQL-hez.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246335"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Csatlakozás a MySQL Azure-adatbázisához átirányítással

Ez a témakör bemutatja, hogyan csatlakoztathat egy alkalmazást az Azure Database for MySQL-kiszolgálóát átirányítási móddal. Az átirányítás célja, hogy csökkentse az ügyfélalkalmazások és a MySQL-kiszolgálók közötti hálózati késést azáltal, hogy lehetővé teszi az alkalmazások számára, hogy közvetlenül csatlakozzanak a háttérkiszolgáló-csomópontokhoz.

## <a name="before-you-begin"></a>Előkészületek
Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Hozzon létre egy Azure-adatbázist a MySQL-kiszolgálóhoz az 5.6-os, 5.7-es vagy 8.0-s motorverzióval. További információt a [MySQL-kiszolgáló Azure-adatbázisának létrehozása](quickstart-create-mysql-server-database-using-azure-portal.md) a Portálról vagy az Azure Database for [MySQL-kiszolgáló létrehozása a CLI használatával című részében](quickstart-create-mysql-server-database-using-azure-cli.md)talál.

Az átirányítás jelenleg csak akkor támogatott, ha **az SSL engedélyezve van az** Azure Database for MySQL-kiszolgálón. Az SSL konfigurálásáról az [SSL használata az Azure Database for MySQL című](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure)témakörben talál további részleteket.

## <a name="php"></a>PHP

A PHP alkalmazások átirányításának támogatása a Microsoft által kifejlesztett [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) bővítményen keresztül érhető el. 

A mysqlnd_azure kiterjesztés a PECL-en keresztül bővítheti a PHP alkalmazásokat, és erősen ajánlott a kiterjesztés telepítése és konfigurálása a hivatalosan közzétett [PECL csomagon](https://pecl.php.net/package/mysqlnd_azure)keresztül.

> [!IMPORTANT]
> A PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) bővítményben az átirányítás támogatása jelenleg előzetes verzióban érhető el.

### <a name="redirection-logic"></a>Átirányítási logika

>[!IMPORTANT]
> Az átirányítási logika/viselkedés az 1.1.0-s verziót kezdve frissült, és **az 1.1.0+ verzió használata ajánlott.**

Az átirányítási viselkedést a értéke `mysqlnd_azure.enableRedirect`határozza meg. Az alábbi táblázat az átirányítás viselkedését mutatja be az **1.1.0+ verzióban**kezdődő paraméter értéke alapján.

Ha a mysqlnd_azure bővítmény régebbi verzióját (1.0.0-1.0.3-as verzió) használja, az `mysqlnd_azure.enabled`átirányítási viselkedést a értéke határozza meg. Az `off` érvényes értékek (hasonlóan működik, mint az alábbi táblázatban leírt viselkedés) és `on` (az alábbi táblázatban látható módon `preferred` működik).  

|**mysqlnd_azure.enableRedirect érték**| **Viselkedés**|
|----------------------------------------|-------------|
|`off` vagy `0`|Az átirányítás nem lesz használva. |
|`on` vagy `1`|- Ha az SSL nincs engedélyezve az Azure Database for MySQL-kiszolgálón, nem lesz kapcsolat. A következő hiba lesz visszaadva: *"mysqlnd_azure.enableRedirect be van kapcsolva, de az SSL beállítás nincs beállítva a kapcsolati karakterláncban. Az átirányítás csak SSL-lel lehetséges."*<br>- Ha az SSL engedélyezve van a MySQL kiszolgálón, de az átirányítás nem támogatott a kiszolgálón, az első kapcsolat megszakad, és a következő hibaüzenet jelenik meg: *"A kapcsolat megszakadt, mert az átirányítás nincs engedélyezve a MySQL kiszolgálón, vagy a hálózati csomag nem felel meg az átirányítási protokollnak."*<br>- Ha a MySQL szerver támogatja az átirányítást, de az átirányított kapcsolat bármilyen okból nem sikerült, akkor az első proxykapcsolatot is megszakítja. Az átirányított kapcsolat hibáját adja vissza.|
|`preferred` vagy `2`<br> (alapértelmezett érték)|- mysqlnd_azure, ha lehetséges, átirányítást alkalmaznak.<br>- Ha a kapcsolat nem használ SSL-t, a kiszolgáló nem támogatja az átirányítást, vagy az átirányított kapcsolat nem tud csatlakozni, miközben a proxykapcsolat még mindig érvényes, akkor visszaáll az első proxykapcsolatra.|

A dokumentum következő szakaszai ismertetik a `mysqlnd_azure` kiterjesztés PECL használatával történő telepítésének módját, és beállítják ennek a paraméternek az értékét.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Előfeltételek 
- PHP verziók 7.2.15+ és 7.3.2+
- PHP KÖRTE 
- php-mysql
- Azure Database for MySQL server ssl engedélyezve

1. Telepítse [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) [PECL](https://pecl.php.net/package/mysqlnd_azure)- Az 1.1.0+ verzió használata ajánlott.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Keresse meg a`extension_dir`kiterjesztés könyvtárat ( ) az alábbi futtatásával:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Módosítsa a könyvtárakat a `mysqlnd_azure.so` visszaadott mappára, és győződjön meg arról, hogy ebben a mappában található. 

4. Keresse meg az .ini fájlok mappáját az alábbi futtatásával: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Módosítsa a könyvtárakat erre a visszaadott mappára. 

6. Hozzon létre egy új `mysqlnd_azure`.ini fájlt a számára. Győződjön meg arról, hogy a név betűrendje a mysqnld után van, mivel a modulok az ini fájlok névsorrendjének megfelelően töltődnek be. Ha például `mysqlnd` az .ini nevet kapta, `10-mysqlnd.ini`nevezze el `20-mysqlnd-azure.ini`a mysqlnd ini nevet .

7. Az új .ini fájlban adja hozzá a következő sorokat az átirányítás engedélyezéséhez.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Előfeltételek 
- PHP verziók 7.2.15+ és 7.3.2+
- php-mysql
- Azure Database for MySQL server ssl engedélyezve

1. A következő parancs futtatásával állapítsa meg, hogy a PHP x64-es vagy x86-os verzióját futtatja-e:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Töltse le a megfelelő x64 vagy x86-os verzióját a [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL-nek a [PECL-ből,](https://pecl.php.net/package/mysqlnd_azure) amely megfelel a PHP verziójának. Az 1.1.0+ verzió használata ajánlott.

3. Bontsa ki a zip `php_mysqlnd_azure.dll`fájlt, és keresse meg a DLL nevű .

4. Keresse meg a`extension_dir`kiterjesztés könyvtárat ( ) az alábbi parancs futtatásával:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Másolja `php_mysqlnd_azure.dll` a fájlt a 4. 

6. Keresse meg a fájlt `php.ini` tartalmazó PHP mappát a következő paranccsal:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Módosítsa `php.ini` a fájlt, és adja hozzá a következő további sorokat az átirányítás engedélyezéséhez. 

    A Dinamikus bővítmények szakaszban: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    A Modulbeállítások szakaszban:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Átirányítás megerősítése

Azt is ellenőrizheti, hogy az átirányítás az alábbi PHP-kóddal van konfigurálva. Hozzon létre `mysqlConnect.php` egy PHP fájlt hívott, és illessze be az alábbi kódot. Frissítse a kiszolgáló nevét, felhasználónevét és jelszavát a sajátjával. 
 
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
A kapcsolati karakterláncokról a [Kapcsolati karakterláncok](howto-connection-string.md)című témakörben talál további információt.
