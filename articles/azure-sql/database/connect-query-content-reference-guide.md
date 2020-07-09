---
title: Csatlakozás és lekérdezés
description: Hivatkozások Azure SQL Database rövid útmutatókra, amelyek azt mutatják be, hogyan lehet csatlakozni a Azure SQL Databasehoz és a lekérdezésekhez, valamint az Azure SQL felügyelt példányához.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: ceaff4575713d4bf40bea621cbb08313aad11611
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267477"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-connect-and-query-articles"></a>A Azure SQL Database és az Azure SQL felügyelt példányának csatlakozási és lekérdezési cikkei
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Az alábbi dokumentum olyan Azure-példákra mutató hivatkozásokat tartalmaz, amelyek a Azure SQL Database és az Azure SQL felügyelt példányának összekapcsolását és lekérdezését mutatják be. Az átviteli szintű biztonsággal kapcsolatos egyes javaslatokért lásd: [TLS-megfontolások az adatbázis-kapcsolathoz](#tls-considerations-for-database-connectivity).

## <a name="quickstarts"></a>Rövid útmutatók

| |  |
|---|---|
|[SQL Server Management Studio](connect-query-ssms.md)|Ez a rövid útmutató bemutatja, hogyan használható a SSMS egy adatbázishoz való kapcsolódáshoz, majd hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Ez a rövid útmutató azt ismerteti, hogyan használható a Azure Data Studio egy adatbázishoz való kapcsolódáshoz, majd hogyan lehet Transact-SQL-(T-SQL-) utasítások használatával létrehozni a Azure Data Studio oktatóanyagokban használt oktatóanyagaiban.|
|[Azure Portal](connect-query-portal.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a lekérdezéstervező egy adatbázishoz való kapcsolódáshoz (csak Azure SQL Database), majd hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[Visual Studio Code](connect-query-vscode.md)|Ez a rövid útmutató bemutatja, hogyan használható a Visual Studio Code egy adatbázishoz való kapcsolódáshoz, majd hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[.NET Visual Studióval](connect-query-dotnet-visual-studio.md)|Ez a rövid útmutató bemutatja, hogyan használható a .NET-keretrendszer egy C#-program létrehozásához a Visual Studióval egy adatbázishoz való kapcsolódáshoz, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|[.NET Core](connect-query-dotnet-core.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a .NET Core a Windows/Linux/macOS rendszeren egy olyan C#-program létrehozásához, amely egy adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|[Ugrás](connect-query-go.md)|Ez a rövid útmutató bemutatja, hogyan használható a go egy adatbázishoz való kapcsolódáshoz. Emellett az adatok lekérdezéséhez és módosításához használatos Transact-SQL-utasításokat is bemutatja.|
|[Java](connect-query-java.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Java egy adatbázishoz való kapcsolódáshoz, majd hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|[Node.js](connect-query-nodejs.md)|Ez a rövid útmutató bemutatja, hogyan használható a Node. js egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|[PHP](connect-query-php.md)|Ez a rövid útmutató bemutatja, hogyan használható a PHP egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|[Python](connect-query-python.md)|Ez a rövid útmutató bemutatja, hogyan használható a Python egy adatbázishoz való kapcsolódáshoz, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni. |
|[Ruby](connect-query-ruby.md)|Ez a rövid útmutató bemutatja, hogyan használható a Ruby egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|[R](connect-query-r.md)|Ez a rövid útmutató azt ismerteti, hogyan használható az R és Azure SQL Database Machine Learning Services egy olyan program létrehozásához, amely a Azure SQL Database adatbázisához csatlakozik, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|||

## <a name="tls-considerations-for-database-connectivity"></a>TLS-megfontolások az adatbázis-kapcsolathoz

A Transport Layer Security (TLS) a Microsoft által szolgáltatott és a Azure SQL Database vagy az Azure SQL felügyelt példányain lévő adatbázisokhoz való csatlakozást támogató összes illesztőprogramhoz használható. Nincs szükség különleges konfigurációra. A SQL Server-példányok, a Azure SQL Database-adatbázisok vagy az Azure SQL felügyelt példányok egy példányának minden kapcsolata esetén azt javasoljuk, hogy minden alkalmazás a következő konfigurációkat vagy azok megfelelőit állítsa be:

- **Encrypt = On**
- **TrustServerCertificate = Off**

Egyes rendszerek eltérő, de egyenértékű kulcsszavakat használnak a fenti konfigurációs kulcsszavak helyett. Ezek a konfigurációk biztosítják, hogy az ügyfélillesztő ellenőrizze a kiszolgálótól kapott TLS-tanúsítvány identitását.

Javasoljuk továbbá, hogy tiltsa le az ügyfélen a TLS 1.1-et és 1.0-t, ha meg kell felelnie a Payment Card Industry – Data Security Standard (PCI-DSS) szabványnak.

Előfordulhat, hogy a nem a Microsofttól származó illesztők alapértelmezés szerint nem a TLS protokollt használják. Ez a Azure SQL Database vagy az Azure SQL felügyelt példányaihoz való csatlakozás során lehet tényező. A beágyazott illesztőkkel rendelkező alkalmazások nem mindig teszik lehetővé a kapcsolatbeállítások szabályozását. Javasoljuk, hogy vizsgálja meg az ilyen illesztők és alkalmazások biztonságát, mielőtt bizalmas adatokat kezelő rendszereken használná őket.

## <a name="libraries"></a>Kódtárak

A Azure SQL Database vagy az Azure SQL felügyelt példányaihoz való kapcsolódáshoz különböző kódtárak és keretrendszerek használhatók. Tekintse meg az első [lépéseket ismertető oktatóanyagokat](https://aka.ms/sqldev) , amelyekkel gyorsan megkezdheti a programozási nyelvek, például a C#, a Java, a Node. js, a PHP és a Python használatát. Ezután hozzon létre egy alkalmazást SQL Server on Linux vagy Windows vagy Docker használatával macOS rendszeren.

A következő táblázat felsorolja azokat a kapcsolódási könyvtárakat és *illesztőprogramokat* , amelyeket az ügyfélalkalmazások számos különböző nyelven használhatnak a helyszíni vagy a felhőben futó SQL Serverhoz való csatlakozáshoz és használatához. A Linuxon, a Windowson vagy a Docker-ben is használható, és a Azure SQL Database, az Azure SQL felügyelt példányához és a Azure SQL Data Warehousehoz való kapcsolódáshoz használhatja őket.

| Nyelv | Platform | További források | Letöltés | Bevezetés |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [A SQL Server Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Letöltés](https://www.microsoft.com/net/download/) | [Bevezetés](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC-illesztőprogram a SQL Serverhoz](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/) | [Letöltés](https://go.microsoft.com/fwlink/?linkid=852460) |  [Bevezetés](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP SQL-illesztőprogram a SQL Serverhoz](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Letöltés](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Bevezetés](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Node. js-illesztőprogram a SQL Serverhoz](/sql/connect/node-js/node-js-driver-for-sql-server/) | [Telepítse](/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/) |  [Bevezetés](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL-illesztőprogram](/sql/connect/python/python-driver-for-sql-server/) | Telepítési lehetőségek: <br/> \*[pymssql](/sql/connect/python/pymssql/step-1-configure-development-environment-for-pymssql-python-development/) <br/> \*[pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development/) |  [Bevezetés](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby-illesztőprogram a SQL Serverhoz](/sql/connect/ruby/ruby-driver-for-sql-server/) | [Telepítse](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/) | [Bevezetés](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC-illesztőprogram a SQL Serverhoz](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) | [Letöltés](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) |  

Az alábbi táblázat felsorolja azokat az objektum-összehasonlító leképezési (ORM) keretrendszereket és webes keretrendszereket, amelyeket az ügyfélalkalmazások használhatnak SQL Server, Azure SQL Database, Azure SQL felügyelt példány vagy Azure szinapszis Analytics használatával. A keretrendszerek Linux, Windows vagy Docker rendszereken is használhatók.

| Nyelv | Platform | ORM (ok) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernált ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (ékesszóló)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>További lépések

- A kapcsolati architektúrával kapcsolatos információkért tekintse meg [az Azure SQL Database kapcsolati architektúráját](connectivity-architecture.md) ismertető cikket.
- Megtalálhatja SQL Server az ügyfélalkalmazások közötti kapcsolódáshoz használt [illesztőprogramokat](/sql/connect/sql-connection-libraries/) .
- Kapcsolódás Azure SQL Database vagy Azure SQL felügyelt példányhoz:
  - [A .NET (C#) használatával való kapcsolat és lekérdezés](connect-query-dotnet-core.md)
  - [A PHP-vel való kapcsolat és lekérdezés](connect-query-php.md)
  - [Csatlakozási és lekérdezés a Node. js használatával](connect-query-nodejs.md)
  - [Kapcsolat és lekérdezés a Java használatával](connect-query-java.md)
  - [Kapcsolat és lekérdezés a Python használatával](connect-query-python.md)
  - [Csatlakozási és lekérdezés a Ruby használatával](connect-query-ruby.md)
- Próbálja újra a logikai kódok példáit:
  - [Rugalmas csatlakozás a ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Rugalmas csatlakozás a PHP-vel][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
