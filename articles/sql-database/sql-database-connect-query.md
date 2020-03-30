---
title: Csatlakozás és lekérdezés – rövid útmutatók
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240561"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Rövid útmutatók: Azure SQL Database-csatlakozás és lekérdezés

A következő dokumentum az Azure SQL-adatbázisok csatlakozásával és lekérdezésével kapcsolatos Azure-példákra mutató hivatkozásokat tartalmaz. Emellett a TLS-re vonatkozó javaslatokat is tartalmaz.

## <a name="quickstarts"></a>Rövid útmutatók

| |  |
|---|---|
|[SQL Server Management Stúdió](sql-database-connect-query-ssms.md)|Ez a rövid útmutató azt ismerteti, hogyan használható az SSMS egy Azure SQL-adatbázishoz való csatlakozáshoz, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Ez a rövid útmutató azt ismerteti, hogyan használható az Azure Data Studio egy Azure SQL-adatbázishoz való csatlakozáshoz, és hogyan lehet Transact-SQL- (T-SQL-) utasításokkal létrehozni az Azure Data Studio oktatóanyagaiban használt TutorialDB adatbázist.|
|[Azure-portál](sql-database-connect-query-portal.md)|Ez a rövid útmutató ismerteti, hogyan használható a Lekérdezésszerkesztő az SQL-adatbázisokhoz való csatlakozáshoz, majd hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[Visual Studio kód](sql-database-connect-query-vscode.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Visual Studio Code egy Azure SQL-adatbázishoz való csatlakozáshoz, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[.NET Visual Studióval](sql-database-connect-query-dotnet-visual-studio.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a .NET-keretrendszer olyan C#-program a Visual Studióval való létrehozásához, amely egy Azure SQL-adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[.NET mag](sql-database-connect-query-dotnet-core.md)|Ez a rövid útmutató ismerteti, hogyan használható a .NET Core olyan C#-program létrehozásához Windows/Linux/macOS-gépeken, amely egy Azure SQL-adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[Ugrás](sql-database-connect-query-go.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Go egy Azure SQL-adatbázishoz való csatlakozáshoz. Emellett az adatok lekérdezéséhez és módosításához használatos Transact-SQL-utasításokat is bemutatja.|
|[Java](sql-database-connect-query-java.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Java egy Azure SQL-adatbázishoz való csatlakozáshoz, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|[Node.js](sql-database-connect-query-nodejs.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Node.js egy olyan program létrehozásához, amely egy Azure SQL-adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[Php](sql-database-connect-query-php.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a PHP egy olyan program létrehozásához, amely egy Azure SQL-adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[Python](sql-database-connect-query-python.md)|Ez a rövid útmutató azt ismerteti, hogyan lehet a Python használatával Azure SQL-adatbázishoz csatlakozni, valamint a Transact-SQL-utasítások használatával adatokat lekérdezni. |
|[Ruby](sql-database-connect-query-ruby.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Ruby egy olyan program létrehozásához, amely egy Azure SQL-adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[R](sql-database-connect-query-r.md)|Ez a rövid útmutató bemutatja, hogyan használhatja az R-t az Azure SQL Database Machine Learning Services-szel egy Azure SQL-adatbázishoz való csatlakozásra és a Transact-SQL utasítások használatával az adatok lekérdezésére.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>TLS-megfontolások az SQL Database-kapcsolatokhoz
A Transport Layer Security (TLS) protokollt az összes Microsoft által biztosított vagy támogatott illesztő használja az Azure SQL Database-hez való csatlakozáshoz. Nincs szükség különleges konfigurációra. Az SQL Server vagy az Azure SQL Database kapcsolataihoz javasoljuk, hogy az alkalmazásokhoz a következő vagy azokkal egyenértékű konfigurációkat állítson be:

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

Egyes rendszerek eltérő, de egyenértékű kulcsszavakat használnak a fenti konfigurációs kulcsszavak helyett. Ezek a konfigurációk biztosítják, hogy az ügyfélillesztő ellenőrizze a kiszolgálótól kapott TLS-tanúsítvány identitását.

Javasoljuk továbbá, hogy tiltsa le az ügyfélen a TLS 1.1-et és 1.0-t, ha meg kell felelnie a Payment Card Industry – Data Security Standard (PCI-DSS) szabványnak.

Előfordulhat, hogy a nem a Microsofttól származó illesztők alapértelmezés szerint nem a TLS protokollt használják. Ez befolyásolhatja az Azure SQL Database-hez való csatlakozást. A beágyazott illesztőkkel rendelkező alkalmazások nem mindig teszik lehetővé a kapcsolatbeállítások szabályozását. Javasoljuk, hogy vizsgálja meg az ilyen illesztők és alkalmazások biztonságát, mielőtt bizalmas adatokat kezelő rendszereken használná őket.

## <a name="libraries"></a>Kódtárak

Különböző kódtárak és keretrendszerek segítségével csatlakozhat az Azure SQL Database-hez. Tekintse meg [az Első lépések oktatóanyagait,](https://aka.ms/sqldev) amelyekkel gyorsan elkezdheti az olyan programozási nyelveket, mint a C#, a Java, a Node.js, a PHP és a Python. Ezután hozzon létre egy alkalmazást az SQL Server linuxos vagy Windows vagy Docker macOS rendszeren.

Az alábbi táblázat azokat a kapcsolódási tárakat vagy *illesztőprogramokat* sorolja fel, amelyeket az ügyfélalkalmazások különböző nyelvekről használhatnak a helyszíni vagy a felhőben futó SQL Server-kiszolgálóhoz való csatlakozáshoz és azok használatához. Használhatja őket Linux, Windows vagy Docker, és használja őket az Azure SQL Database és az Azure SQL Data Warehouse. 

| Nyelv | Platform | További források | Letöltés | Bevezetés |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET SQL Server rendszerhez](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Letöltés](https://www.microsoft.com/net/download/) | [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC illesztőprogram az SQL Server kiszolgálóhoz](https://msdn.microsoft.com/library/mt484311.aspx) | [Letöltés](https://go.microsoft.com/fwlink/?linkid=852460) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP SQL-illesztőprogram az SQL Server hez](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Letöltés](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Node.js illesztőprogram az SQL Server kiszolgálóhoz](https://msdn.microsoft.com/library/mt652093.aspx) | [Telepítse](https://msdn.microsoft.com/library/mt652094.aspx) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL-illesztőprogram](https://msdn.microsoft.com/library/mt652092.aspx) | Telepítési lehetőségek: <br/> \*[pymssql között](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \*[pyodbc között](https://msdn.microsoft.com/library/mt763257.aspx) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby illesztőprogram az SQL Server hez](https://msdn.microsoft.com/library/mt691981.aspx) | [Telepítse](https://msdn.microsoft.com/library/mt711041.aspx) | [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC illesztőprogram az SQL Server kiszolgálóhoz](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Letöltés](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

Az alábbi táblázat példákat sorol fel az objektumrelációs leképezési (ORM) keretrendszerekre és webes keretrendszerekre, amelyeket az ügyfélalkalmazások a helyszínen vagy a felhőben futó SQL Server relativis használatával használhatnak. Használhatja a keretrendszereket Linux, Windows vagy Docker, és használja őket az SQL Database és az SQL Data Warehouse. 

| Nyelv | Platform | ORM(ok) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[HibernálásI ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (ékesszóló)](https://laravel.com/docs/eloquent)<br>[Doktrína](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Folytatás ORM](https://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby a sínek](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>További lépések

- A kapcsolati architektúrával kapcsolatos információkért tekintse meg [az Azure SQL Database kapcsolati architektúráját](sql-database-connectivity-architecture.md) ismertető cikket.
- Ügyfélalkalmazásokból való csatlakozáshoz használt [SQL Server-illesztőprogramok](https://msdn.microsoft.com/library/mt654049.aspx) keresése
- Csatlakozás SQL-adatbázishoz:
  - [Csatlakozás SQL Database adatbázishoz  .NET (C#) használatával](sql-database-connect-query-dotnet.md) 
  - [Csatlakozás az SQL Database-hez a PHP használatával](sql-database-connect-query-php.md) 
  - [Csatlakozás az SQL Database-hez a Node.js használatával](sql-database-connect-query-nodejs.md) 
  - [Csatlakozás az SQL Database-hez a Java használatával](sql-database-connect-query-java.md) 
  - [Csatlakozás az SQL Database-hez a Python használatával](sql-database-connect-query-python.md)
  - [Csatlakozás az SQL Database-hez a Ruby használatával](sql-database-connect-query-ruby.md)
- Példák az Újrapróbálkozás logikai kódjának:
  - [Rugalmasan csatlakozhat az SQL-hez ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Csatlakozzon rugalmasan az SQL-hez a PHP-vel][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
