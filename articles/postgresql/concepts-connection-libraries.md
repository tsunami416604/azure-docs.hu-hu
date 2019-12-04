---
title: Kapcsolatok kódtárai – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk számos olyan kódtárat és illesztőprogramot ismertet, amelyek az alkalmazások Azure Database for PostgreSQL-kiszolgálóval való összekapcsolásához és lekérdezéséhez használhatók.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 2305a02e5b094ec9e98f39363ddbd0c39221ab0b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768894"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – egyetlen kiszolgáló közötti kapcsolatok kódtárai
Ez a cikk felsorolja azokat a kódtárakat és illesztőprogramokat, amelyeket a fejlesztők a Azure Database for PostgreSQLhoz való kapcsolódáshoz és lekérdezéshez használhatnak az alkalmazások fejlesztéséhez.

## <a name="client-interfaces"></a>Ügyféloldali felületek
A PostgreSQL-kiszolgálóhoz való kapcsolódáshoz használt legtöbb nyelvi ügyfélszoftver külső projekt, és egymástól függetlenül oszlik meg. A felsorolt kódtárak a Windows, Linux és Mac platformokon támogatottak a Azure Database for PostgreSQLhoz való csatlakozáshoz. A következő lépések szakaszban számos gyors üzembe helyezési példa látható.

| **Nyelv** | **Ügyfél felülete** | **További információ** | **Letöltés** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | ADATBÁZIS-API 2,0-kompatibilis | [Letöltés](http://initd.org/psycopg/download/) |
| PHP | [PHP – pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Adatbázis-bővítmény | [Telepítés](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [PG NPM-csomag](https://www.npmjs.com/package/pg) | A Pure JavaScript nem blokkoló ügyfele | [Telepítés](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | 4-es típusú JDBC-illesztő | [Letöltés](https://jdbc.postgresql.org/download.html)  |
| Ruby | [PG gem](https://deveiate.org/code/pg/) | Ruby-interfész | [Letöltés](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Indítás | [Csomagok pq](https://godoc.org/github.com/lib/pq) | Pure go postgres-illesztőprogram | [Telepítés](https://github.com/lib/pq/blob/master/README.md) |
| C\#/.NET | [Npgsql](https://www.npgsql.org/) | ADO.NET-adatszolgáltató | [Letöltés](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC-illesztő | [Letöltés](https://www.postgresql.org/ftp/odbc/versions/) |
| C# | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Elsődleges C nyelvi felület | Alap |
| C++ | [libpqxx](http://pqxx.org/) | Új stílusú C++ felület | [Letöltés](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Következő lépések
Az alábbi rövid útmutatókból megtudhatja, hogyan csatlakozhat a Azure Database for PostgreSQLhoz, és hogyan kérdezheti le azt a választott nyelv használatával:

[Python](./connect-python.md) | [Node. js](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [php](./connect-php.md) | [.net (C#)](./connect-csharp.md) | [Go](./connect-go.md)
