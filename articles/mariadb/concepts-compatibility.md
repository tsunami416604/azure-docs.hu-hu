---
title: Illesztőprogramok és eszközök kompatibilitása - Azure Database for MariaDB
description: Ez a cikk a MariaDB-illesztőprogramokat és a MariaDB-adatbázissal kompatibilis felügyeleti eszközöket ismerteti.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a0cee198f028fd90e04dac15e98d7cd33aee9201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532348"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>A MariaDB-illesztőprogramok és a MariaDB-hoz való Azure Database for MariaDB-vel kompatibilis felügyeleti eszközök

Ez a cikk ismerteti az okat az illesztőprogramokat és felügyeleti eszközöket, amelyek kompatibilisek az Azure Database for MariaDB-vel.

## <a name="mariadb-drivers"></a>MariaDB illesztőprogramok

A MariaDB Azure Database a MariaDB kiszolgáló közösségi kiadását használja. Ezért kompatibilis a programozási nyelvek és illesztőprogramok széles választékával. A MariaDB API-ja és protokollja kompatibilis a MySQL által használt api-val. Ez azt jelenti, hogy a MySQL-rel dolgozó csatlakozóknak a MariaDB-vel is működniük kell.

A cél az, hogy támogassa a három legújabb verzióMariaDB vezetők, és erőfeszítéseket a szerzők a nyílt forráskódú közösség folyamatosan javítja a funkcionalitásés a használhatóság mariaDB vezetők továbbra is. A tesztelt és a MariaDB 10.2-es Azure Database for MariaDB 10.2-es adatbázissal kompatibilisnek talált illesztőprogramok listáját az alábbi táblázat tartalmazza:

**Illesztőprogram** | **Hivatkozások** | **Kompatibilis verziók** | **Nem kompatibilis verziók** | **Megjegyzések**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5,5, 5,6, 7.x | 5.3 | Az SSL MySQLi-vel való PHP 7.0 kapcsolathoz adja hozzá MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT a kapcsolati karakterlánchoz. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> OEM-készlet: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` false opció.
.NET | [MySqlConnector a GitHubon](https://github.com/mysql-net/MySqlConnector) <br> [A Nuget telepítőcsomagja](https://www.nuget.org/packages/MySqlConnector/) | 0,27 és utána | 0.26.5 és előtte |
MySQL-összekötő/NET | [MySQL-összekötő/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | A kódolási hiba egyes nem UTF8 Windows rendszereken a kapcsolatok megszakadását okozhatja.
Node.js |  [MySQLjs a GitHubon](https://github.com/mysqljs/mysql/) <br> Telepítőcsomag az NPM-től:<br> Futtatás `npm install mysql` NPM-ről | 2.15 | 2.14.1.
GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 és előtte | Használja `allowNativePasswords=true` az 1.3-as verzió kapcsolati karakterláncában. Az 1.4-es `allowNativePasswords=true` verzió tartalmaz egy javítást, és már nincs szükség rá.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2. |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5. |

## <a name="management-tools"></a>Kezelőeszközök

A kompatibilitási előny kiterjed az adatbázis-kezelő eszközökre is. A meglévő eszközök továbbra is együtt kell működnie az Azure Database for MariaDB, mindaddig, amíg az adatbázis-kezelés a felhasználói engedélyek határain belül működik. Az alábbi táblázat három olyan gyakori adatbázis-kezelő eszközt tartalmaz, amelyeket teszteltek és kompatibilisnek találtak a MariaDB 10.2-es Azure Database for MariaDB 10.2-es adatbázisával:

| | **MySQL Workbench 6.x és fel** | **Navicat 12** | **PHPMyAdmin 4.x és fel**
---|---|---|---
Létrehozás, frissítés, olvasás, írás, törlés | X | X | X
SSL-kapcsolat | X | X | X
SQL-lekérdezés automatikus befejezése | X | X |
Adatok importálása és exportálása | X | X | X
Exportálás több formátumba | X | X | X
Biztonsági mentés és visszaállítás |  | X |
Kiszolgálóparamétereinek megjelenítése | X | X | X
Ügyfélkapcsolatok megjelenítése | X | X | X

## <a name="next-steps"></a>További lépések

- [Az Azure Database for MariaDB-hez való csatlakozás hibáinak elhárítása](howto-troubleshoot-common-connection-issues.md)