---
title: Illesztőprogram- és eszközkompatibilitás – Azure Database for MySQL
description: Ez a cikk ismerteti a MySQL-illesztőprogramok és felügyeleti eszközök, amelyek kompatibilisek az Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: e8917a0a5678c4c6b72352a0d4c1523bfea3c96d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537210"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>A MySQL Azure Database for MySQL-rel kompatibilis MySQL-illesztőprogramok és felügyeleti eszközök
Ez a cikk ismerteti az okat az illesztőprogramokat és felügyeleti eszközöket, amelyek kompatibilisek az Azure Database for MySQL.

## <a name="mysql-drivers"></a>MySQL illesztőprogramok
Az Azure Database for MySQL a mySQL-adatbázis legnépszerűbb közösségi kiadását használja. Ezért kompatibilis a programozási nyelvek és illesztőprogramok széles választékával. A cél az, hogy támogassa a három legújabb verziómySQL-illesztőprogramok, és erőfeszítéseket a szerzők a nyílt forráskódú közösség folyamatosan javítja a funkcionalitást és a használhatóság a MySQL vezetők továbbra is. A tesztelt és az Azure Database for MySQL 5.6 és 5.7 szolgáltatáshoz készült Azure Database szolgáltatással kompatibilisnek talált illesztőprogramok listáját az alábbi táblázat tartalmazza:

| **Programozási nyelv** | **Illesztőprogram** | **Hivatkozások** | **Kompatibilis verziók** | **Nem kompatibilis verziók** | **Megjegyzések** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5,5, 5,6, 7.x | 5.3 | Az SSL MySQLi-vel való PHP 7.0 kapcsolathoz adja hozzá MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT a kapcsolati karakterlánchoz. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> OEM-készlet: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` false opció.|
| .NET | Async MySQL Connector a .NET-hez | https://github.com/mysql-net/MySqlConnector <br> [A Nuget telepítőcsomagja](https://www.nuget.org/packages/MySqlConnector/) | 0,27 és utána | 0.26.5 és előtte | |
| .NET | MySQL-összekötő/NET | https://github.com/mysql/mysql-connector-net | 6.6.3 ,7.0 ,8.0 |  | A kódolási hiba egyes nem UTF8 Windows rendszereken a kapcsolatok megszakadását okozhatja. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Telepítőcsomag az NPM-től:<br> Futtatás `npm install mysql` NPM-ről | 2.15 | 2.14.1. | |
| Node.js | csomópont-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4+ | | |
| Indítás | Menj MySQL driver | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 és előtte | Használja `allowNativePasswords=true` az 1.3-as verzió kapcsolati karakterláncában. Az 1.4-es `allowNativePasswords=true` verzió tartalmaz egy javítást, és már nincs szükség rá. |
| Python | MySQL-összekötő/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2, használja a 8.0.16+ és a MySQL 8.0  | 1.2.2. | |
| Python | PyMySQL között | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3+ | 0.9.0 - 0.9.2 (regresszió a web2py-ben) | |
| Java | MariaDB összekötő/J | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5. | | 
| Java | MySQL összekötő/J | https://github.com/mysql/mysql-connector-j | 5.1.21+, használja a 8.0.17+ készüléket a MySQL 8.0-val | 5.1.20. | |
| C# | MySQL Összekötő/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2+ | | |
| C# | MySQL Csatlakozó/ODBC (myodbc) | https://github.com/mysql/mysql-connector-odbc | 3.51.29+ | | |
| C++ | MySQL csatlakozó/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9+ | 1.1.3 és az alatt | | 
| C++ | MySQL++| https://tangentsoft.net/mysql++ | 3.2.3+ | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10+ | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16+ | | |
| Swift | mysql-gyors | https://github.com/novi/mysql-swift | 0,7,2+ | | |
| Swift | gőz/ mysql | https://github.com/vapor/mysql-kit | 2.0.1+ | | |

## <a name="management-tools"></a>Kezelőeszközök
A kompatibilitási előny kiterjed az adatbázis-kezelő eszközökre is. A meglévő eszközök továbbra is együtt kell működnie az Azure Database for MySQL, mindaddig, amíg az adatbázis-kezelés a felhasználói engedélyek határain belül működik. Három olyan gyakori adatbázis-kezelő eszközt, amelyeket teszteltek, és amelyekről megállapították, hogy kompatibilisek a MySQL 5.6-os és 5.7-es Azure-adatbázisával, az alábbi táblázat tartalmazza:

|                                     | **MySQL Workbench 6.x és fel** | **Navicat 12** | **PHPMyAdmin 4.x és fel** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Létrehozás, frissítés, olvasás, írás, törlés | X | X | X |
| SSL-kapcsolat | X | X | X |
| SQL-lekérdezés automatikus befejezése | X | X |  |
| Adatok importálása és exportálása | X | X | X | 
| Exportálás több formátumba | X | X | X |
| Biztonsági mentés és visszaállítás |  | X |  |
| Kiszolgálóparamétereinek megjelenítése | X | X | X |
| Ügyfélkapcsolatok megjelenítése | X | X | X |

## <a name="next-steps"></a>További lépések

- [Az Azure Database for MySQL csatlakoztatási hibáinak elhárítása](howto-troubleshoot-common-connection-issues.md)