---
title: "Azure-adatbázis a PostgreSQL adatkapcsolattárak |} Microsoft Docs"
description: "Ez a cikk ismerteti a több szalagtárak és illesztőprogramokat, hogy a fejlesztők használhatják mikor kódolási kapcsolódás és lekérdezés az Azure-adatbázis a PostgreSQL-alkalmazások."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 7d6f13336b3913299433e25c69a41f8c94f598b2
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2017
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Adatkapcsolattárak PostgreSQL Azure-adatbázis
Ez a témakör felsorolja a szalagtárak és illesztőprogramokat, amelyek a fejlesztők a program alkalmazások csatlakozzanak, Azure-adatbázis lekérdezése a PostgreSQL.

## <a name="client-interfaces"></a>Ügyfél felületek
A legtöbb nyelvi ügyfél függvénytárainak PostgreSQL-kiszolgálóhoz való csatlakozáshoz használt külső projektek, és egymástól függetlenül küld. Ezek a Windows, Linux és Mac rendszerek támogatottak. Egy népszerű ügyfél illesztőprogramot a következő táblázat tartalmazza:

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

## <a name="next-steps"></a>Következő lépések
Olvassa el ezen quickstarts csatlakozni, és az Azure-adatbázis lekérdezése a PostgreSQL a választott nyelv használatával:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md) | [nyissa meg](./connect-go.md)
