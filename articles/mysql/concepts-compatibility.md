---
title: MySQL-illesztőprogramok és a felügyeleti eszközök kompatibilitása
description: Ez a cikk ismerteti a MySQL-illesztőprogramok és a felügyeleti eszközöket, amelyek kompatibilisek az Azure Database for MySQL-hez.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 46f2ac9114ac1191ea85b428711cb25d8557a61a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837575"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>MySQL-illesztőprogramok és a felügyeleti eszközök, Azure Database for MySQL-kompatibilis
Ez a cikk ismerteti az illesztőprogramok és a felügyeleti eszközöket, amelyek kompatibilisek az Azure Database for MySQL-hez.

## <a name="mysql-drivers"></a>MySQL Drivers
Azure Database for MySQL a világ legnépszerűbb közösségi kiadásának MySQL-adatbázist használja. Ezért így kompatibilis lesz számos különböző programozási nyelveket és illesztőprogramok. A célja, hogy a legújabb verziójú három MySQL illesztőprogramokat támogatja, és folytatja a szerzők a nyílt forráskód Közösségtől származó erőfeszítéseket folyamatosan javíthatja az a funkciók és a MySQL-illesztőprogramok használhatóságát. Az alábbi táblázatban biztosított teszteltük, és nem kompatibilis az Azure Database for MySQL 5.6-os és 5.7-es illesztőprogramok listáját:

| **Illesztőprogram** | **Hivatkozások** | **Kompatibilis verziója** | **Inkompatibilis verziók** | **Megjegyzések** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Adjon hozzá MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT SSL MySQLi PHP 7.0 kapcsolatot, a kapcsolati karakterláncban. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> OEM-készlet: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` beállítást hamis értékre.|
| .NET | [MySqlConnector on GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Nuget csomag telepítését](https://www.nuget.org/packages/MySqlConnector/) | 0.27 és után | 0.26.5 és előtt | |
| MySQL Connector/NET | [MySQL Connector/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Egy kódolási hiba okozhatja a kapcsolódás meghiúsulását bizonyos UTF8 Windows rendszereken. |
| Nodejs |  [MySQLjs on GitHub](https://github.com/mysqljs/mysql/) <br> Az npm-ből telepítőcsomagot:<br> Futtatás `npm install mysql` az npm-ből | 2.15 | 2.14.1 és előtt | |
| UGRÁS | https://github.com/go-sql-driver/mysql/releases | 1.3 | 1.2-es és előtt | Használja a allowNativePasswords = true, a kapcsolati karakterlánc |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 és előtt | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 és előtt | |

## <a name="management-tools"></a>Kezelőeszközök
Kompatibilitási előnye az adatbázis-kezelő eszközét is kiterjeszti. A meglévő eszközökkel továbbra is működnek az Azure Database for MySQL, mindaddig, amíg az adatbázis kezelésének felhasználói engedélyek határain belül működik. Három gyakori adatbázis-kezelő eszközét, amelyek tesztelése, és nem kompatibilis az Azure Database for MySQL 5.6-os és 5.7 az alábbi táblázatban láthatók:

|                                     | **A MySQL Workbench 6.x, és akár** | **Navicat 12** | **PHPMyAdmin 4.x-es és újabb** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Létrehozás, frissítés, Olvasás, írás, törlés | X | X | X |
| SSL Connection | X | X | X |
| SQL-lekérdezés automatikus befejezése | X | X |  |
| Adatok importálása és exportálása | X | X | X |
| Többféle formátumba exportálás | X | X | X |
| Biztonsági mentés és visszaállítás |  | X |  |
| Kiszolgáló paramétereinek megjelenítéséhez | X | X | X |
| Kapcsolatok megjelenítése | X | X | X |
