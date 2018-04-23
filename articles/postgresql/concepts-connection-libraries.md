---
title: Adatkapcsolattárak PostgreSQL Azure-adatbázis
description: Ez a cikk ismerteti a több szalagtárak és illesztőprogramokat, hogy a fejlesztők használhatják mikor kódolási kapcsolódás és lekérdezés az Azure-adatbázis a PostgreSQL-alkalmazások.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 7f0dd217a124f530ba009c9a5588691f604827e5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Adatkapcsolattárak PostgreSQL Azure-adatbázis
Ez a cikk felsorolja a szalagtárak és illesztőprogramokat, amelyek a fejlesztők a csatlakozhat, és az Azure-adatbázis lekérdezése a PostgreSQL-alkalmazások fejlesztéséhez.

## <a name="client-interfaces"></a>Ügyfél felületek
A legtöbb nyelvi ügyfél függvénytárainak PostgreSQL-kiszolgálóhoz való csatlakozáshoz használt külső projektek, és egymástól függetlenül küld. A felsorolt könyvtárak támogatottak a Windows, Linux és Mac rendszerek PostgreSQL az Azure-adatbázishoz szeretne csatlakozni. Gyors üzembe helyezés néhány példa a következő lépéseket szakaszban találhatók.

| **Nyelv** | **Ügyféloldali felület** | **További információ** | **Letöltés** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0-kompatibilis | [Letöltés](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | Adatbázis bővítmény | [Telepítés](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [A védelmi npm csomag](https://www.npmjs.com/package/pg) | Tiszta JavaScript nem blokkoló ügyfél | [Telepítés](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | 4 típus JDBC-illesztőt | [Letöltés](https://jdbc.postgresql.org/download.html)  |
| Ruby | [A védelmi gem](https://deveiate.org/code/pg/) | Ruby felület | [Letöltés](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Indítás | [Csomag pq](https://godoc.org/github.com/lib/pq) | Tiszta Ugrás postgres illesztőprogram | [Telepítés](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | Az ADO.NET-szolgáltató | [Letöltés](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC-illesztő | [Letöltés](http://www.postgresql.org/ftp/odbc/versions/) |
| C# | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | C nyelvi elsődleges felület | Tartalmazza |
| C++ | [libpqxx](http://pqxx.org/) | Új stílusú C++ felület | [Letöltés](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>További lépések
Olvassa el ezen quickstarts csatlakozni, és az Azure-adatbázis lekérdezése a PostgreSQL a választott nyelv használatával:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
