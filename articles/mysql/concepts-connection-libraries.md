---
title: Az Azure Database for MySQL csatlakozási kódtárak
description: Ez a cikk felsorolja az egyes könyvtár vagy illesztőprogram, ügyfélprogramok használhat a MySQL-hez készült Azure-adatbázishoz való csatlakozáskor.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 14515aefe9635160cf99a630b0742d23352532cf
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985964"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Az Azure Database for MySQL csatlakozási kódtárak
Ez a cikk felsorolja az egyes könyvtár vagy illesztőprogram, ügyfélprogramok használhat a MySQL-hez készült Azure-adatbázishoz való csatlakozáskor.

## <a name="client-interfaces"></a>Ügyféloldali felületei
MySQL kínál a szabványos adatbázis illesztőprogram-kapcsolat a MySQL használata alkalmazások és eszközök, az iparági normák, ODBC, JDBC és kompatibilis. Bármely olyan rendszeren, amely együttműködik a ODBC, JDBC vagy MySQL használhatja.

| **Nyelv** | **Platform** | **További erőforrások** | **Letöltés** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [A PHP - mysqlnd natív MySQL-illesztőprogram](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Letöltés](https://secure.php.net/downloads.php) |
| ODBC | Windows, Linux, Mac OS X és Unix rendszerek | [MySQL Connector/ODBC fejlesztői útmutató](https://dev.mysql.com/doc/connector-odbc/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL Connector/Net fejlesztői útmutató](https://dev.mysql.com/doc/connector-net/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Független platform | [MySQL Connector/J 5.1 fejlesztői útmutató](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Letöltés](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [MySQL Connector/Python fejlesztői útmutató](https://dev.mysql.com/doc/connector-python/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [MySQL-összekötő/C++ fejlesztői útmutató](https://dev.mysql.com/doc/connector-cpp/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [MySQL Connector/C fejlesztői útmutató](https://dev.mysql.com/doc/connector-c/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows, Linux, Mac OS X és Unix rendszerek | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Letöltés](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>További lépések
Olvassa el az alábbi gyorsútmutatókkal a történő csatlakoztatása és lekérdezése az Azure Database for MySQL-hez tetszőleges nyelven használatával:

[A PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md)  |  [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [Go](./connect-go.md)

