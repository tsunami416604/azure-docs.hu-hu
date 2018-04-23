---
title: MySQL-illesztőprogramok és a felügyeleti eszközök kompatibilitása
description: Ez a cikk ismerteti a MySQL-illesztőprogramok és MySQL az Azure-adatbázis kompatibilis eszközök.
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 5fc13ef07b61feb9e9fdd73123a09daa61f6aaf3
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>MySQL-illesztőprogramok és MySQL az Azure-adatbázis kompatibilis eszközök
Ez a cikk ismerteti az illesztőprogramok és MySQL az Azure-adatbázis kompatibilis eszközök.

## <a name="mysql-drivers"></a>MySQL-illesztőprogram
Azure MySQL-adatbázis használja a világ legnépszerűbb community edition MySQL-adatbázis. Ezért összeegyeztethető számos programozási nyelveket és illesztőprogramokat. A cél három legújabb verzióját a MySQL-illesztőprogramok támogatása, és folytatni a szerzők nyílt forráskódú közösségi azon törekvéseit, hogy folyamatosan a a funkciókat és a MySQL-illesztőprogramok használhatóságuk javításában. A tesztelni és való kompatibilitás érdekében a MySQL 5.6 és 5.7 Azure-adatbázis található illesztőprogramok listája a következő táblázatban:

| **Driver** | **Hivatkozások** | **Kompatibilis verziója** | **Kompatibilis a következő verziók** | **Megjegyzések** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | http://php.net/downloads.php | 5.5 5.6 7.x | 5.3 | Adja hozzá MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT SSL MySQLi PHP 7.0 kapcsolatot, a kapcsolati karakterláncban. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> OEM set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` beállítást false értékre.|
| .Net | [A Githubon MySqlConnector](https://github.com/mysql-net/MySqlConnector) <br> [A Nugetből telepítési csomag](https://www.nuget.org/packages/MySqlConnector/) | 0.27 és után | 0.26.5 és előtt | |
| Nodejs |  [A Githubon MySQLjs](https://github.com/mysqljs/mysql/releases) <br> Az NPM telepítőcsomag:<br> Futtatás `npm install mysql` az NPM | 2.15 | 2.14.1 és előtt | |
| NYISSA MEG | https://github.com/go-sql-driver/mysql/releases | 1.3 | 1.2-es és előtt | Használja a allowNativePasswords = true, a kapcsolat-karakterláncban |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 és előtt | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1 2.0 1.6 | 1.5.5 és előtt | |

## <a name="management-tools"></a>Kezelőeszközök
A kompatibilitási ki adatbázis-felügyeleti eszközt is kiterjed. A már létező eszközök továbbra is használható az Azure-adatbázis a MySQL, mindaddig, amíg az adatbázis módosítását működik, a felhasználói engedélyek határain belül. Három közös adatbázis-felügyeleti eszközt, tesztelése és való kompatibilitás érdekében a MySQL 5.6 és 5.7 Azure-adatbázis található a következő táblázatban láthatók:

|                                     | **MySQL-munkaterület 6.x vagy afölötti** | **Navicat 12** | **PHPMyAdmin 4.x vagy afölötti** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Létrehozása, frissítése, Olvasás, írás, törlés | X | X | X |
| SSL-kapcsolat | X | X | X |
| SQL-lekérdezés automatikus kiegészítéshez | X | X |  |
| Adatok importálása és exportálása | X | X | X |
| Több exportálása | X | X | X |
| Biztonsági mentés és visszaállítás |  | X |  |
| Kiszolgáló paraméterek megjelenítése | X | X | X |
| Ügyfélkapcsolatok megjelenítése | X | X | X |
