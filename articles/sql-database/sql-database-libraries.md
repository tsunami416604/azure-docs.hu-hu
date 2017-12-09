---
title: "SQL-adatbázis adatkapcsolattárak |} Microsoft Docs"
description: "Hivatkozások a modulokat, amelyek lehetővé teszik az ügyfél programozási nyelvek széles választékában SQL Server és SQL-adatbázis kapcsolati letöltések."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: genemi
ms.assetid: 13d899d3-cf46-4e4d-8919-cf4b41ca836d
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: genemi
ms.openlocfilehash: ce7cfdfa4695283c588bc5c73791b1c04cd2f772
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="connectivity-libraries-and-frameworks-for-microsoft-sql-server"></a>Kapcsolat kódtárai és keretrendszerei a Microsoft SQL Server

Tekintse meg a [első lépéseket bemutató Oktatóanyagainkat](http://aka.ms/sqldev) gyors Ismerkedés a programozási nyelvek, például a C#, Java, Node.js, PHP és Python, és hozza létre az SQL Server használata a Linux- vagy Windows- vagy Docker macOS alkalmazását.

Az alábbi táblázat felsorolja a kapcsolat tárak vagy *illesztőprogramok* , hogy az ügyfélalkalmazások nyelvi csatlakozhat és a Microsoft SQL Server rendszert futtató helyszíni vagy a felhőben, Linux, Windows vagy Docker és a különböző használhatja Az Azure SQL Database és az Azure SQL Data Warehouse. 

| Nyelv | Platform | További források | Letöltés | Első lépések |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Az SQL Server Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Letöltés](https://www.microsoft.com/net/download/) | [Első lépések](https://www.microsoft.com/en-us/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC-illesztőprogram SQL Serverhez](http://msdn.microsoft.com/library/mt484311.aspx) | [Letöltés](https://go.microsoft.com/fwlink/?linkid=852460) |  [Első lépések](https://www.microsoft.com/en-us/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Az SQL Server PHP SQL-illesztőprogram](http://msdn.microsoft.com/library/dn865013.aspx) | Operációs rendszer: <br/> \*[Windows](https://www.microsoft.com/download/details.aspx?id=20098) <br/> \*[Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \*[macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [Első lépések](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [Az SQL Server node.js-illesztőprogram](http://msdn.microsoft.com/library/mt652093.aspx) | [Telepítés](https://msdn.microsoft.com/library/mt652094.aspx) |  [Első lépések](https://www.microsoft.com/en-us/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL-illesztőprogram](http://msdn.microsoft.com/library/mt652092.aspx) | Telepítse a választási lehetőségek: <br/> \*[pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \*[pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [Első lépések](https://www.microsoft.com/en-us/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Az SQL Server Ruby illesztőprogram](http://msdn.microsoft.com/library/mt691981.aspx) | [Telepítés](https://msdn.microsoft.com/library/mt711041.aspx) | [Első lépések](https://www.microsoft.com/en-us/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Az SQL Server Microsoft ODBC-illesztőprogram](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) | [Letöltés](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) |  

Az alábbi táblázatban néhány példa a objektum relációs leképezési (ORM) keretrendszerek és a webes keretrendszerek, amellyel az ügyfél alkalmazások a Microsoft SQL Server rendszert futtató helyszíni vagy a felhőben, Linux, Windows vagy Docker és az Azure SQL Database és Azure Az SQL Data Warehouse. 

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
- [SQL Server illesztőprogramok](http://msdn.microsoft.com/library/mt654049.aspx) ügyfélalkalmazások való csatlakozáshoz
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

