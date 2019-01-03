---
title: Az Azure Database for PostgreSQL csatlakozási kódtárak
description: Ez a cikk azt ismerteti, több kódtárak és illesztőprogramokat, hogy a fejlesztők mikor a kódolási alkalmazások történő csatlakozásról és lekérdezésről, Azure Database for postgresql-hez.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 0e762a2d7cf82e2957fb276fcea0a20553f719e3
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536015"
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Az Azure Database for PostgreSQL csatlakozási kódtárak
Ez a cikk felsorolja a kódtárak és illesztőprogramokat, amelyek a fejlesztők a csatlakozhat, és a PostgreSQL-hez készült Azure Database-adatbázis lekérdezéséhez alkalmazások fejlesztéséhez.

## <a name="client-interfaces"></a>Ügyféloldali felületei
PostgreSQL-kiszolgálóhoz való kapcsolódáshoz használt legtöbb nyelvi klienskódtárak külső projektek és egymástól függetlenül vannak osztva. A felsorolt kódtárak használata támogatott a Windows, Linux és Mac platformokon, a PostgreSQL-hez készült Azure-adatbázishoz szeretne csatlakozni. A következő lépések szakasz néhány rövid példa láthatók.

| **Nyelv** | **Ügyféloldali felület** | **További információ** | **Letöltés** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0-kompatibilis | [Letöltés](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Adatbázis-bővítmény | [Telepítés](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [PG npm-csomag](https://www.npmjs.com/package/pg) | Tiszta JavaScript nem blokkoló ügyfél | [Telepítés](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | 4. típus JDBC-illesztőprogram | [Letöltés](https://jdbc.postgresql.org/download.html)  |
| Ruby | [PG gem](https://deveiate.org/code/pg/) | Ruby-kapcsolat | [Letöltés](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Indítás | [Package pq](https://godoc.org/github.com/lib/pq) | Pure Go postgres illesztőprogramot | [Telepítés](https://github.com/lib/pq/blob/master/README.md) |
| C\#ÉS .NET | [Npgsql](https://www.npgsql.org/) | Az ADO.NET-szolgáltató | [Letöltés](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC-illesztő | [Letöltés](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Elsődleges C nyelv felület | Tartalmazza |
| C++ | [libpqxx](http://pqxx.org/) | Új stílusú C++ felület | [Letöltés](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>További lépések
Olvassa el az alábbi gyorsútmutatókkal a történő csatlakoztatása és lekérdezése az Azure Database for postgresql-hez tetszőleges nyelven használatával:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md) | [go](./connect-go.md)
