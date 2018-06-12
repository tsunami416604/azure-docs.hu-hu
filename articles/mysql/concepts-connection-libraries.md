---
title: Adatkapcsolattárak MySQL az Azure-adatbázis
description: Ez a cikk sorolja fel, minden könyvtár vagy illesztőprogram, ügyfélprogramok MySQL az Azure-adatbázishoz való csatlakozáshoz használhat.
services: mysql
author: mswutao
ms.author: wutao
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: c826bf1cf17230563b608e764c443b6166f13924
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264003"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Adatkapcsolattárak MySQL az Azure-adatbázis
Ez a cikk sorolja fel, minden könyvtár vagy illesztőprogram, ügyfélprogramok MySQL az Azure-adatbázishoz való csatlakozáshoz használhat.

## <a name="client-interfaces"></a>Ügyfél felületek
MySQL adatbázis standard illesztőprogram kapcsolódási MySQL ipari szabványok ODBC és JDBC kompatibilis eszközök és az alkalmazások használatára vonatkozó lehetőséget. A rendszer, amely ODBC vagy JDBC MySQL használhatja.

| **Nyelv** | **Platform** | **További erőforrások** | **Letöltés** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [A PHP - mysqlnd natív MySQL-illesztőprogram](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Letöltés](http://php.net/downloads.php) |
| ODBC | Windows, Linux, Mac OS X és Unix rendszereken | [MySQL-összekötő/ODBC fejlesztői útmutató](https://dev.mysql.com/doc/connector-odbc/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL összekötő/Net fejlesztői útmutató](https://dev.mysql.com/doc/connector-net/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Független platform | [MySQL-összekötő/J 5.1 – útmutató fejlesztőknek](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Letöltés](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [MySQL összekötő/Python fejlesztői útmutató](https://dev.mysql.com/doc/connector-python/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [MySQL összekötő/C++ – útmutató fejlesztőknek](https://dev.mysql.com/doc/connector-cpp/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [MySQL összekötő/C – útmutató fejlesztőknek](https://dev.mysql.com/doc/connector-c/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows, Linux, Mac OS X és Unix rendszereken | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Letöltés](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>További lépések
Olvassa el ezen quickstarts csatlakozni, és az Azure-adatbázis lekérdezése a MySQL a választott nyelv használatával:

[A PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md)  |  [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [nyissa meg](./connect-go.md)

