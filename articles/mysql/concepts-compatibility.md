---
title: "MySQL-illesztőprogramok és a felügyeleti eszközök kompatibilitási |} Microsoft Docs"
description: "MySQL-illesztőprogramok és MySQL az Azure-adatbázis kompatibilis eszközök"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 10/27/2017
ms.openlocfilehash: 4df0dcc7d0f2bde24c1a7e12eea6fa142612a0e7
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
## <a name="mysql-drivers"></a>MySQL-illesztőprogram
Azure MySQL-adatbázis használja a világ legnépszerűbb community edition MySQL-adatbázis. Ezért összeegyeztethető számos programozási nyelveket és illesztőprogramokat. A cél három legújabb verzióját a MySQL-illesztőprogramok támogatása, és folytatni a szerzők nyílt forráskódú közösségi azon törekvéseit, hogy folyamatosan a a funkciókat és a MySQL-illesztőprogramok használhatóságuk javításában. A tesztelni és való kompatibilitás érdekében a MySQL 5.6 és 5.7 Azure-adatbázis található illesztőprogramok listája a következő táblázatban:

| **Illesztőprogram** | **Hivatkozások** | **Kompatibilis verziója** | **Kompatibilis a következő verziók** | **Megjegyzések** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | http://php.NET/downloads.php | 5.5 5.6 7.x | 5.3 | Adja hozzá MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT SSL MySQLi PHP 7.0 kapcsolatot, a kapcsolati karakterláncban. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> OEM set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` beállítást false értékre.|
| .Net | [A Githubon MySqlConnector]: https://github.com/mysql-net/MySqlConnector/releases <br> [Telepítési csomag a Nugetből]:<br> https://www.nuget.org/Packages/MySqlConnector/ | 0.27 és után | 0.26.5 és előtt | |
| Nodejs |  [A Githubon MySQLjs]:<br> https://github.com/mysqljs/MySQL/releases <br> [NPM telepítési csomagot]:<br> Futtassa a "npm telepíthessenek mysql" NPM | 2.15 | 2.14.1 és előtt | |
| NYISSA MEG | https://github.com/go-SQL-Driver/MySQL/releases | 1.3 | 1.2-es és előtt | Használja a allowNativePasswords = true, a kapcsolat-karakterláncban |
| Python | https://pypi.Python.org/pypi/MySQL-Connector-Python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 és előtt | |
| Java | https://downloads.mariadb.org/Connector-Java/ | 2.1 2.0 1.6 | 1.5.5 és előtt | |

## <a name="management-tools"></a>Felügyeleti eszközök
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