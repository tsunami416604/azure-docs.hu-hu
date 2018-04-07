---
title: SQL-adatbázis adatkapcsolattárak |} Microsoft Docs
description: Hivatkozások a modulokat, amelyek lehetővé teszik az SQL Server és SQL-adatbázis kapcsolati ügyfél programozási nyelvek széles választékában a letöltéseket.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: a1e74e9fd2f1ed5eec27d6ede9ba51a7ba93263d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>Kapcsolat kódtárai és keretrendszerei az SQL Server

Tekintse meg a [első lépések oktatóanyagok](http://aka.ms/sqldev) gyors megkezdéséhez programozási nyelvek, például a C#, Java, Node.js, PHP és Python. Majd hozhat létre egy alkalmazást az SQL Server használata a Linux- vagy Windows- vagy Docker macOS.

A következő táblázat felsorolja a kapcsolat tárak vagy *illesztőprogramok* , hogy az ügyfélalkalmazások, különböző nyelvi csatlakozhat és a helyszíni SQL Serveren, illetve a felhőben használhatja. Használhat Linux, Windows vagy Docker, és használhatja őket az Azure SQL Database és Azure SQL Data warehouse-ba való csatlakozást. 

| Nyelv | Platform | További források | Letöltés | Bevezetés |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Az SQL Server Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Letöltés](https://www.microsoft.com/net/download/) | [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Az SQL Server Microsoft JDBC-illesztőprogram](http://msdn.microsoft.com/library/mt484311.aspx) | [Letöltés](https://go.microsoft.com/fwlink/?linkid=852460) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Az SQL Server PHP SQL-illesztőprogram](http://msdn.microsoft.com/library/dn865013.aspx) | Operációs rendszer: <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=55642) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [Az SQL Server node.js-illesztőprogram](http://msdn.microsoft.com/library/mt652093.aspx) | [Telepítés](https://msdn.microsoft.com/library/mt652094.aspx) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL-illesztőprogram](http://msdn.microsoft.com/library/mt652092.aspx) | Telepítse a választási lehetőségek: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Az SQL Server Ruby illesztőprogram](http://msdn.microsoft.com/library/mt691981.aspx) | [Telepítés](https://msdn.microsoft.com/library/mt711041.aspx) | [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Az SQL Server Microsoft ODBC-illesztőprogram](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Letöltés](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

Az alábbi táblázatban láthatók példák objektum relációs leképezési (ORM) keretrendszerek és a webes keretrendszerek használó ügyfélalkalmazások SQL Server rendszert futtató helyszíni vagy a felhőben. A keretrendszerek használata Linux, Windows vagy Docker, és SQL-adatbázis és az SQL Data Warehouse való csatlakozáskor használandó őket. 

| Nyelv | Platform | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernálásra ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [ORM sequelize](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[A Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby sínen](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>Kapcsolódó hivatkozások
- [SQL Server illesztőprogramok](http://msdn.microsoft.com/library/mt654049.aspx) csatlakozhat a ügyfélalkalmazások használt
- Csatlakozás SQL Database adatbázishoz:
    - [Csatlakozás az SQL Database-hez a .NET (C#) használatával](sql-database-connect-query-dotnet.md)
    - [Csatlakozás az SQL Database-hez a PHP használatával](sql-database-connect-query-php.md)
    - [Csatlakozás az SQL Database-hez a Node.js használatával](sql-database-connect-query-nodejs.md)
    - [Csatlakozás az SQL Database-hez a Java használatával](sql-database-connect-query-java.md)
    - [Csatlakozás az SQL Database-hez a Python használatával](sql-database-connect-query-python.md)
    - [Csatlakozás az SQL Database-hez a Ruby használatával](sql-database-connect-query-ruby.md)
- Újrapróbálkozási logika hitelesítésikód-példák:
    - [Az ADO.NET SQL eltolódhasson kapcsolódni][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [A PHP SQL eltolódhasson kapcsolódni][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

