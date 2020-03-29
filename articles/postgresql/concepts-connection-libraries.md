---
title: Kapcsolattárak - Azure Database for PostgreSQL - Single Server
description: Ez a cikk számos könyvtárat és illesztőprogramot ismertet, amelyek segítségével alkalmazásokat csatlakoztathat és lekérdezhet a PostgreSQL – Single Server azure-adatbázisának lekérdezéséhez.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 2305a02e5b094ec9e98f39363ddbd0c39221ab0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768894"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Kapcsolattárak az Azure Database for PostgreSQL-hez – Egykiszolgálós
Ez a cikk felsorolja a tárak és illesztőprogramok, amelyek segítségével a fejlesztők alkalmazások at, hogy csatlakozzanak, és lekérdezi az Azure Database for PostgreSQL.

## <a name="client-interfaces"></a>Ügyfélfelületek
A PostgreSQL kiszolgálóhoz való csatlakozáshoz használt legtöbb nyelvi ügyfélkönyvtár külső projekt, és egymástól függetlenül kerül terjesztésre. A felsorolt könyvtárak támogatottak a Windows, Linux és Mac platformokon, az Azure Database for PostgreSQL-hez való csatlakozáshoz. Számos rövid útmutató példa a Következő lépések szakaszban található.

| **Nyelv** | **Ügyfél kapcsolata** | **További információk** | **Letöltés** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg között](http://initd.org/psycopg/) | DB API 2.0-kompatibilis | [Letöltés](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Adatbázis-bővítmény | [Telepítse](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm csomag](https://www.npmjs.com/package/pg) | Tiszta JavaScript nem blokkoló ügyfél | [Telepítse](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | 4. típus JDBC illesztőprogram | [Letöltés](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg drágakő](https://deveiate.org/code/pg/) | Rubin felület | [Letöltés](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Indítás | [Csomag pq](https://godoc.org/github.com/lib/pq) | Pure Go postgres illesztőprogram | [Telepítse](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql között](https://www.npgsql.org/) | ADO.NET adatszolgáltató | [Letöltés](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC-illesztő | [Letöltés](https://www.postgresql.org/ftp/odbc/versions/) |
| C# | [libpq között](https://www.postgresql.org/docs/9.6/static/libpq.html) | Elsődleges C nyelvi felület | Tartalmazza |
| C++ | [libpqxx között](http://pqxx.org/) | Új stílusú C++ felület | [Letöltés](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>További lépések
Olvassa el ezeket a rövid útmutatókat arról, hogyan csatlakozhat a PostgreSQL Azure-adatbázishoz, és hogyan kérdezheti le az Ön által választott nyelvet:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Ugrás](./connect-go.md)
