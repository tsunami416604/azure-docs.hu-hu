---
title: Csatlakozási és lekérdezési útmutató
description: Az Azure SQL-adatbázisokhoz való csatlakozást és a rájuk vonatkozó lekérdezéseket ismertető rövid útmutatók.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 647bdcf5c8c49b5c942419c78155ed4f61c848bb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240561"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Gyors útmutató: Azure SQL Database a kapcsolat és a lekérdezés

A következő dokumentum az Azure SQL-adatbázisok csatlakozásával és lekérdezésével kapcsolatos Azure-példákra mutató hivatkozásokat tartalmaz. Emellett a TLS-re vonatkozó javaslatokat is tartalmaz.

## <a name="quickstarts"></a>Gyors útmutatók

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|Ez a rövid útmutató azt ismerteti, hogyan használható az SSMS egy Azure SQL-adatbázishoz való csatlakozáshoz, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Ez a rövid útmutató azt ismerteti, hogyan használható az Azure Data Studio egy Azure SQL-adatbázishoz való csatlakozáshoz, és hogyan lehet Transact-SQL- (T-SQL-) utasításokkal létrehozni az Azure Data Studio oktatóanyagaiban használt TutorialDB adatbázist.|
|[Azure Portalra](sql-database-connect-query-portal.md)|Ez a rövid útmutató ismerteti, hogyan használható a Lekérdezésszerkesztő az SQL-adatbázisokhoz való csatlakozáshoz, majd hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Visual Studio Code egy Azure SQL-adatbázishoz való csatlakozáshoz, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[.NET Visual Studióval](sql-database-connect-query-dotnet-visual-studio.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a .NET-keretrendszer olyan C#-program a Visual Studióval való létrehozásához, amely egy Azure SQL-adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|Ez a rövid útmutató ismerteti, hogyan használható a .NET Core olyan C#-program létrehozásához Windows/Linux/macOS-gépeken, amely egy Azure SQL-adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[Go](sql-database-connect-query-go.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Go egy Azure SQL-adatbázishoz való csatlakozáshoz. Emellett az adatok lekérdezéséhez és módosításához használatos Transact-SQL-utasításokat is bemutatja.|
|[Java](sql-database-connect-query-java.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Java egy Azure SQL-adatbázishoz való csatlakozáshoz, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|[Node.js](sql-database-connect-query-nodejs.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Node.js egy olyan program létrehozásához, amely egy Azure SQL-adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[PHP](sql-database-connect-query-php.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a PHP egy olyan program létrehozásához, amely egy Azure SQL-adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[Python](sql-database-connect-query-python.md)|Ez a rövid útmutató azt ismerteti, hogyan lehet a Python használatával Azure SQL-adatbázishoz csatlakozni, valamint a Transact-SQL-utasítások használatával adatokat lekérdezni. |
|[Ruby](sql-database-connect-query-ruby.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Ruby egy olyan program létrehozásához, amely egy Azure SQL-adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[R](sql-database-connect-query-r.md)|Ez a rövid útmutató bemutatja, hogyan használható az R és Azure SQL Database Machine Learning Services egy olyan program létrehozásához, amely egy Azure SQL Database-adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>TLS-megfontolások az SQL Database-kapcsolatokhoz
A Transport Layer Security (TLS) protokollt az összes Microsoft által biztosított vagy támogatott illesztő használja az Azure SQL Database-hez való csatlakozáshoz. Nincs szükség különleges konfigurációra. Az SQL Server vagy az Azure SQL Database kapcsolataihoz javasoljuk, hogy az alkalmazásokhoz a következő vagy azokkal egyenértékű konfigurációkat állítson be:

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

Egyes rendszerek eltérő, de egyenértékű kulcsszavakat használnak a fenti konfigurációs kulcsszavak helyett. Ezek a konfigurációk biztosítják, hogy az ügyfélillesztő ellenőrizze a kiszolgálótól kapott TLS-tanúsítvány identitását.

Javasoljuk továbbá, hogy tiltsa le az ügyfélen a TLS 1.1-et és 1.0-t, ha meg kell felelnie a Payment Card Industry – Data Security Standard (PCI-DSS) szabványnak.

Előfordulhat, hogy a nem a Microsofttól származó illesztők alapértelmezés szerint nem a TLS protokollt használják. Ez befolyásolhatja az Azure SQL Database-hez való csatlakozást. A beágyazott illesztőkkel rendelkező alkalmazások nem mindig teszik lehetővé a kapcsolatbeállítások szabályozását. Javasoljuk, hogy vizsgálja meg az ilyen illesztők és alkalmazások biztonságát, mielőtt bizalmas adatokat kezelő rendszereken használná őket.

## <a name="libraries"></a>Kódtárak

A Azure SQL Databasehoz való kapcsolódáshoz különböző kódtárak és keretrendszerek használhatók. Tekintse meg az első [lépéseket ismertető oktatóanyagokat](https://aka.ms/sqldev) a programozási nyelvek, például C#a Java, a Node. js, a PHP és a Python gyors megkezdéséhez. Ezután hozzon létre egy alkalmazást SQL Server on Linux vagy Windows vagy Docker használatával macOS rendszeren.

A következő táblázat felsorolja azokat a kapcsolódási könyvtárakat és *illesztőprogramokat* , amelyeket az ügyfélalkalmazások számos különböző nyelven használhatnak a helyszíni vagy a felhőben futó SQL Serverhoz való csatlakozáshoz és használatához. A Linuxon, a Windowson vagy a Docker-ben is használható, és a Azure SQL Databasehoz és Azure SQL Data Warehousehoz való kapcsolódáshoz használhatja őket. 

| Nyelv | Platform | További források | Letöltés | Első lépések |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [A SQL Server Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Letöltés](https://www.microsoft.com/net/download/) | [Bevezetés](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC-illesztőprogram a SQL Serverhoz](https://msdn.microsoft.com/library/mt484311.aspx) | [Letöltés](https://go.microsoft.com/fwlink/?linkid=852460) |  [Bevezetés](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP SQL-illesztőprogram a SQL Serverhoz](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Letöltés](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Bevezetés](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Node. js-illesztőprogram a SQL Serverhoz](https://msdn.microsoft.com/library/mt652093.aspx) | [Telepítés](https://msdn.microsoft.com/library/mt652094.aspx) |  [Bevezetés](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL-illesztőprogram](https://msdn.microsoft.com/library/mt652092.aspx) | Telepítési lehetőségek: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Bevezetés](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby-illesztőprogram a SQL Serverhoz](https://msdn.microsoft.com/library/mt691981.aspx) | [Telepítés](https://msdn.microsoft.com/library/mt711041.aspx) | [Bevezetés](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC-illesztőprogram a SQL Serverhoz](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Letöltés](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

Az alábbi táblázat felsorolja azokat az objektum-összehasonlító leképezési (ORM) keretrendszereket és webes keretrendszereket, amelyeket az ügyfélalkalmazások használhatnak a helyszíni vagy a felhőben futó SQL Server. Használhatja a Linuxon, a Windowson vagy a Docker-keretrendszereken, és a SQL Database és SQL Data Warehousehoz való kapcsolódáshoz használhatja őket. 

| Nyelv | Platform | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework mag](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernált ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (ékesszóló)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Következő lépések

- A kapcsolati architektúrával kapcsolatos információkért tekintse meg [az Azure SQL Database kapcsolati architektúráját](sql-database-connectivity-architecture.md) ismertető cikket.
- Ügyfél-alkalmazásokból való kapcsolódáshoz használt [SQL Server-illesztőprogramok](https://msdn.microsoft.com/library/mt654049.aspx) keresése
- Kapcsolódás SQL Databasehoz:
  - [Csatlakozás az SQL Database-hez a .NET (C#) használatával](sql-database-connect-query-dotnet.md) 
  - [Csatlakozás az SQL Database-hez a PHP használatával](sql-database-connect-query-php.md) 
  - [Csatlakozás az SQL Database-hez a Node.js használatával](sql-database-connect-query-nodejs.md) 
  - [Csatlakozás az SQL Database-hez a Java használatával](sql-database-connect-query-java.md) 
  - [Csatlakozás az SQL Database-hez a Python használatával](sql-database-connect-query-python.md)
  - [Csatlakozás az SQL Database-hez a Ruby használatával](sql-database-connect-query-ruby.md)
- Próbálja újra a logikai kódok példáit:
  - [Rugalmas csatlakozás az SQL-hez a ADO.NET használatával][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Rugalmas csatlakozás az SQL-hez a PHP-vel][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
