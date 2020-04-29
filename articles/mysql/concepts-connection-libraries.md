---
title: Kapcsolatok kódtárai – Azure Database for MySQL
description: Ez a cikk felsorolja az összes olyan könyvtárat vagy illesztőprogramot, amelyet az ügyfélalkalmazások használhatnak Azure Database for MySQLhoz való csatlakozáskor.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537193"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Azure Database for MySQLhoz tartozó kapcsolatok kódtárai
Ez a cikk felsorolja az összes olyan könyvtárat vagy illesztőprogramot, amelyet az ügyfélalkalmazások használhatnak Azure Database for MySQLhoz való csatlakozáskor.

## <a name="client-interfaces"></a>Ügyféloldali felületek
A MySQL szabványos adatbázis-illesztőprogram-kapcsolatot biztosít a MySQL-hez olyan alkalmazásokkal és eszközökkel, amelyek kompatibilisek az ODBC és a JDBC iparági szabványokkal. Az ODBC-vel vagy JDBC-vel használható rendszerek a MySQL-t használhatják.

| **Nyelv** | **Platform** | **További erőforrás** | **Letöltés** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [MySQL natív illesztőprogram a PHP-hez – mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Letöltés](https://secure.php.net/downloads.php) |
| ODBC | Windows, Linux, Mac OS X és UNIX platform | [MySQL-összekötő/ODBC fejlesztői útmutató](https://dev.mysql.com/doc/connector-odbc/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL-összekötő/NET fejlesztői útmutató](https://dev.mysql.com/doc/connector-net/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Független platform | [MySQL-összekötő/J 5,1 fejlesztői útmutató](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/Node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Letöltés](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [MySQL-összekötő/Python fejlesztői útmutató](https://dev.mysql.com/doc/connector-python/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [MySQL-összekötő/C++ fejlesztői útmutató](https://dev.mysql.com/doc/connector-cpp/en/) | [Letöltés](https://dev.mysql.com/downloads/connector/python/) |
| C# | Windows, Linux, Mac OS X | [MySQL-összekötő/C fejlesztői útmutató](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [Letöltés](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows, Linux, Mac OS X és UNIX platform | [DBD:: MySQL](https://metacpan.org/pod/DBD::mysql) | [Letöltés](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>További lépések
Az alábbi rövid útmutatókból megtudhatja, hogyan csatlakozhat a Azure Database for MySQLhoz, és hogyan kérdezheti le azt a választott nyelv használatával:

[PHP](./connect-php.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [Python](./connect-python.md) |  | [Go](./connect-go.md) [.NET (C#)](./connect-csharp.md) | [C++](connect-cpp.md)[Node.JS](./connect-nodejs.md).net (C#) Python Node. js Ruby C++ go |  

