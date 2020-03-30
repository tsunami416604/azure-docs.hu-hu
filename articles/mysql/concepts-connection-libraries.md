---
title: Kapcsolatkönyvtárak – Azure Database for MySQL
description: Ez a cikk felsorolja az okat a függvénytárat vagy illesztőprogramot, amelyeket az ügyfélprogramok használhatnak az Azure Database for MySQL-hez való csatlakozáskor.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537193"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Kapcsolatkönyvtárak a MySQL-hez készült Azure Database kapcsolattárakhoz
Ez a cikk felsorolja az okat a függvénytárat vagy illesztőprogramot, amelyeket az ügyfélprogramok használhatnak az Azure Database for MySQL-hez való csatlakozáskor.

## <a name="client-interfaces"></a>Ügyfélfelületek
A MySQL szabványos adatbázis-illesztőprogram-kapcsolatot kínál a MySQL olyan alkalmazásokkal és eszközökkel való használatára, amelyek kompatibilisek az ODBC és a JDBC iparági szabványokkal. Az ODBC-vel vagy a JDBC-vel működő bármely rendszer használhatja a MySQL-t.

| **Nyelv** | **Platform** | **További erőforrás** | **Letöltés** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [MySQL natív vezető PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Letöltés](https://secure.php.net/downloads.php) |
| ODBC | Windows, Linux, Mac OS X és Unix platformok | [MySQL Connector/ODBC fejlesztői útmutató](https://dev.mysql.com/doc/connector-odbc/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL Connector/Net fejlesztői útmutató](https://dev.mysql.com/doc/connector-net/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Platform független | [MySQL Connector/J 5.1 fejlesztői útmutató](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Letöltés](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [MySQL Connector/Python fejlesztői útmutató](https://dev.mysql.com/doc/connector-python/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [MySQL Connector/C++ fejlesztői útmutató](https://dev.mysql.com/doc/connector-cpp/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/python/) |
| C# | Windows, Linux, Mac OS X | [MySQL Connector/C fejlesztői útmutató](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [Letöltés](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows, Linux, Mac OS X és Unix platformok | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Letöltés](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>További lépések
Olvassa el ezeket a rövid útmutatókat arról, hogyan csatlakozhat a MySQL Azure-adatbázisához, és hogyan kérdezheti le őket az Ön által választott nyelv használatával:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [Go](./connect-go.md)

