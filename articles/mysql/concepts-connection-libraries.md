---
title: "MySQL az Azure-adatbázis adatkapcsolattárak |} Microsoft Docs"
description: "Ez a cikk sorolja fel, minden könyvtár vagy illesztőprogram, ügyfélprogramok MySQL az Azure-adatbázishoz való csatlakozáshoz használhat."
services: mysql
author: mswutao
ms.author: wutao
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/08/2017
ms.openlocfilehash: e746f28829063f8437ad408c4681f90aec6c0e0a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Adatkapcsolattárak MySQL az Azure-adatbázis
Ez a témakör minden könyvtár vagy illesztőprogram használó ügyfélprogramok MySQL az Azure-adatbázishoz való csatlakozáshoz.

## <a name="client-interfaces"></a>Ügyfél felületek
MySQL adatbázis standard illesztőprogram kapcsolódási MySQL ipari szabványok ODBC és JDBC kompatibilis eszközök és az alkalmazások használatára vonatkozó lehetőséget. A rendszer, amely ODBC vagy JDBC MySQL használhatja.

| **Nyelv** | **Platform** | **További erőforrások** | **Letöltés** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [A PHP - mysqlnd natív MySQL-illesztőprogram](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Letöltés](http://php.net/downloads.php) |
| ODBC | Windows, Linux, Mac OS X és Unix rendszereken | [MySQL-összekötő/ODBC fejlesztői útmutató](https://dev.mysql.com/doc/connector-odbc/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL összekötő/Net fejlesztői útmutató](https://dev.mysql.com/doc/connector-net/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Független platform | [MySQL-összekötő/J 5.1 – útmutató fejlesztőknek](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/csomópont-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Letöltés](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [MySQL összekötő/Python fejlesztői útmutató](https://dev.mysql.com/doc/connector-python/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [MySQL összekötő/C++ – útmutató fejlesztőknek](https://dev.mysql.com/doc/connector-cpp/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/python/) |
| C# | Windows, Linux, Mac OS X | [MySQL összekötő/C – útmutató fejlesztőknek](https://dev.mysql.com/doc/connector-c/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows, Linux, Mac OS X és Unix rendszereken | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Letöltés](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Következő lépések
[Azure-adatbázis MySQL áttekintés](./overview.md)
[Server kapcsolatos fogalmak, MySQL az Azure-adatbázis](./concepts-servers.md)
