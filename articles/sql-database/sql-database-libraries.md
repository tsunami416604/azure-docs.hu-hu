---
title: Adatkapcsolattárak az SQL Database |} A Microsoft Docs
description: Hivatkozások, amelyek ügyfél programozási nyelvek széles körű számos SQL Server és SQL Database-kapcsolat engedélyezése letöltéseket.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 874aa2610ae1785e9f40841ed3c3279aac42d660
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321823"
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>Csatlakozási kódtárak és az SQL Server keretrendszerek

Tekintse meg a [első lépések oktatóanyagok](https://aka.ms/sqldev) a programozási nyelvek, például a C#, Java, Node.js, PHP és Python használatának gyors megkezdéséhez. Egy alkalmazás készítését az SQL Server használata a Linux vagy Windows- vagy Docker macOS rendszeren.

A következő táblázat felsorolja a csatlakozási kódtárak vagy *illesztőprogramok* , hogy ügyfélalkalmazásai használhatnak a különböző nyelveken való csatlakozáshoz, és a helyszínen futó SQL Server vagy a felhőben. A használata Linux, Windows vagy a Docker, és ezek segítségével csatlakozhat Azure SQL Database és az Azure SQL Data Warehouse. 

| Nyelv | Platform | További források | Letöltés | Bevezetés |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Az SQL Server Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Letöltés](https://www.microsoft.com/net/download/) | [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC-illesztőprogram SQL Serverhez](https://msdn.microsoft.com/library/mt484311.aspx) | [Letöltés](https://go.microsoft.com/fwlink/?linkid=852460) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP-SQL-illesztőprogram SQL Serverhez](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Letöltés](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [NODE.js-illesztőprogram SQL Serverhez](https://msdn.microsoft.com/library/mt652093.aspx) | [Telepítés](https://msdn.microsoft.com/library/mt652094.aspx) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python-SQL-illesztőprogram](https://msdn.microsoft.com/library/mt652092.aspx) | Telepítse a választási lehetőségek: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby-illesztőprogram SQL Serverhez](https://msdn.microsoft.com/library/mt691981.aspx) | [Telepítés](https://msdn.microsoft.com/library/mt711041.aspx) | [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC-illesztőprogram SQL Serverhez](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Letöltés](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

Az alábbi táblázatban láthatók példák objektum-objektumrelációs (ORM) keretrendszerek és a webes keretrendszereket használó ügyfélalkalmazások számára a helyszínen futó SQL Serverrel vagy a felhőben. A keretrendszerek használata a Linux, Windows vagy a Docker, és ezek segítségével csatlakozhat az SQL Database és SQL Data warehouse-bA. 

| Nyelv | Platform | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Az ENTITY Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernálásra ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [ORM sequelize](https://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [A Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="related-links"></a>Kapcsolódó hivatkozások
- [Az SQL Server-illesztőprogramok](https://msdn.microsoft.com/library/mt654049.aspx) , amelyek segítségével az ügyfélalkalmazások csatlakoztatása
- Csatlakozás az SQL Database:
    - [Csatlakozás az SQL Database-hez a .NET (C#) használatával](sql-database-connect-query-dotnet.md)
    - [Csatlakozás az SQL Database-hez a PHP használatával](sql-database-connect-query-php.md)
    - [Csatlakozás az SQL Database-hez a Node.js használatával](sql-database-connect-query-nodejs.md)
    - [Csatlakozás az SQL Database-hez a Java használatával](sql-database-connect-query-java.md)
    - [Csatlakozás az SQL Database-hez a Python használatával](sql-database-connect-query-python.md)
    - [Csatlakozás az SQL Database-hez a Ruby használatával](sql-database-connect-query-ruby.md)
- Újrapróbálkozási logika hitelesítésikód-példák:
    - [Logikára kapcsolódni az SQL az ADO.NET-tel][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [Az SQL-PHP logikára csatlakoztatása][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

