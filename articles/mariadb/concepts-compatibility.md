---
title: Azure Database for MariaDB-illesztőprogramok és a felügyeleti eszközök kompatibilitása
description: Ez a cikk ismerteti a MariaDB-illesztőprogramok és a felügyeleti eszközök, Azure Database for MariaDB kompatibilis.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 7a3d9a5f87a565625052fc54e3ecccc99fd928a7
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259498"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>MariaDB-illesztőprogramok és a felügyeleti eszközök, Azure Database for MariaDB-kompatibilis

Ez a cikk ismerteti az illesztőprogramok és a felügyeleti eszközök, Azure Database for MariaDB kompatibilis.

## <a name="mariadb-drivers"></a>MariaDB-illesztőprogramok

Az Azure Database for MariaDB használja a MariaDB Server közösségi kiadását. Ezért így kompatibilis lesz számos különböző programozási nyelveket és illesztőprogramok. Használja a MariaDB API és a protokoll MySQL által használt kompatibilisek lesznek. Ez azt jelenti, hogy az összekötők, amelyek együttműködnek a MySQL is működnie kell a MariaDB.

A célja, hogy a legújabb verziójú három MariaDB-illesztőprogramok támogatása, és folyamatosan javíthatja az a funkciók használhatóságát és működését a MariaDB-illesztőprogramok a szerzők a nyílt forráskód Közösségtől származó erőfeszítéseket továbbra is. Az alábbi táblázatban biztosított teszteltük, és nem kompatibilis az Azure Database for MariaDB 10.2 illesztőprogramok listáját:

**Illesztőprogram** | **Hivatkozások** | **Kompatibilis verziója** | **Inkompatibilis verziók** | **Megjegyzések**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Adjon hozzá MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT SSL MySQLi PHP 7.0 kapcsolatot, a kapcsolati karakterláncban. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> OEM-készlet: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` beállítást hamis értékre.
.NET | [MySqlConnector on GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Nuget csomag telepítését](https://www.nuget.org/packages/MySqlConnector/) | 0.27 és után | 0.26.5 és előtt |
MySQL Connector/NET | [MySQL Connector/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Egy kódolási hiba okozhatja a kapcsolódás meghiúsulását bizonyos UTF8 Windows rendszereken.
Node.js |  [MySQLjs on GitHub](https://github.com/mysqljs/mysql/) <br> Az npm-ből telepítőcsomagot:<br> Futtatás `npm install mysql` az npm-ből | 2.15 | 2.14.1 és előtt
UGRÁS | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2-es és előtt | Használat `allowNativePasswords=true` 1.3-as verzió esetében a kapcsolati karakterláncban. 1.4-es verziójának javítást tartalmaz, és `allowNativePasswords=true` már nem szükséges.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 és előtt |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 és előtt |

## <a name="management-tools"></a>Kezelőeszközök

Kompatibilitási előnye az adatbázis-kezelő eszközét is kiterjeszti. A meglévő eszközökkel továbbra is működjön az Azure Database for MariaDB, mindaddig, amíg az adatbázis kezelésének felhasználói engedélyek határain belül működik. Az alábbi táblázatban felsorolt három gyakori adatbázis-kezelő eszközét, amelyek tesztelése, és nem kompatibilis az Azure Database for MariaDB 10.2:

| | **A MySQL Workbench 6.x, és akár** | **Navicat 12** | **PHPMyAdmin 4.x-es és újabb**
---|---|---|---
Létrehozás, frissítés, Olvasás, írás, törlés | X | X | X
SSL Connection | X | X | X
SQL-lekérdezés automatikus befejezése | X | X |
Adatok importálása és exportálása | X | X | X
Többféle formátumba exportálás | X | X | X
Biztonsági mentés és visszaállítás |  | X |
Kiszolgáló paramétereinek megjelenítéséhez | X | X | X
Kapcsolatok megjelenítése | X | X | X

## <a name="next-steps"></a>További lépések

- [MariaDB-hez készült Azure Database-kapcsolatok problémáinak hibaelhárítása](howto-troubleshoot-common-connection-issues.md)