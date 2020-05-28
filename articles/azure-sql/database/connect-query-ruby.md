---
title: A Ruby használata a lekérdezéshez
description: Ez a témakör bemutatja, hogyan használható a Ruby egy olyan program létrehozásához, amely Azure SQL Database-adatbázishoz csatlakozik, és hogyan kérdezheti le a Transact-SQL-utasítások használatával.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2 
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 1a0f65ff6d0b98d3764cb6a7b1d4b07ccb8ae92a
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054406"
---
# <a name="quickstart-use-ruby-to-query-a-microsoft-azure-sql-database"></a>Gyors útmutató: Microsoft Azure SQL-adatbázis lekérdezése a Ruby használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a rövid útmutató bemutatja, hogyan használható a [Ruby](https://www.ruby-lang.org) egy Azure SQL Database-adatbázishoz való kapcsolódáshoz és az adatlekérdezéshez Transact-SQL-utasításokkal.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következő előfeltételek szükségesek:

- Azure SQL-adatbázis. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist az Azure SQL-ben:

  || SQL Database | SQL Managed Instance | Azure virtuális gépen futó SQL Server |
  |:--- |:--- |:---|:---|
  | Létrehozás| [Portál](single-database-create-quickstart.md) | [Portál](../managed-instance/instance-create-quickstart.md) | [Portál](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [parancssori felület](scripts/create-and-configure-database-cli.md) | [parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](firewall-create-server-level-portal-quickstart.md)| [Kapcsolódás virtuális gépről](../managed-instance/connect-vm-instance-configure.md)|
  |||[Kapcsolódás a webhelyről](../managed-instance/point-to-site-p2s-configure.md) | [Csatlakozás az SQL Serverhez](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Adatok betöltése|Adventure Works betöltve|[Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) | [Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) |
  |||Adventure Works visszaállítása vagy importálása a [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Adventure Works visszaállítása vagy importálása a [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > A cikkben található parancsfájlok az Adventure Works-adatbázis használatára íródnak. Felügyelt példány esetén importálnia kell az Adventure Works-adatbázist egy példány-adatbázisba, vagy módosítania kell a jelen cikkben szereplő parancsfájlokat a Wide World Importálós adatbázis használatára.
  
- A Ruby és az operációs rendszerhez kapcsolódó szoftverek:
  
  - **MacOS**: a Homebrew, a rbenv és a Ruby-Build, a Ruby, a FreeTDS és a TinyTDS telepítése. Tekintse meg a 1,2, 1,3, 1,4, 1,5 és 2,1 lépéseket a [Ruby-alkalmazások létrehozása a macOS SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)című témakörben.
  
  - **Ubuntu**: a Ruby, a rbenv és a Ruby-Build, a Ruby, a FreeTDS és a TinyTDS telepítésének előfeltételei. Tekintse meg a 1,2, 1,3, 1,4, 1,5 és 2,1 lépéseket a [Ruby-alkalmazások létrehozása SQL Server használatával Ubuntu rendszeren](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: telepítse a Ruby, a Ruby fejlesztői készlet és a TinyTDS. Lásd: [fejlesztői környezet konfigurálása Ruby-fejlesztéshez](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az Azure SQL Database-hez való kapcsolódáshoz szükséges kapcsolati adatok lekérése. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok** vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egy Azure SQL Database vagy a teljes kiszolgálónév (vagy IP-cím) mellett egy Azure SQL felügyelt példányhoz vagy egy Azure-beli **virtuális gépen SQL Server** . A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

> [!NOTE]
> Az Azure-beli virtuális gépek SQL Server kapcsolati információit lásd: [kapcsolódás SQL Serverhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="create-code-to-query-your-sql-database"></a>Kód létrehozása az SQL-adatbázis lekérdezéséhez

1. Egy szöveg-vagy Kódszerkesztő-szerkesztőben hozzon létre egy *sqltest. RB*nevű új fájlt.

1. Adja hozzá a következő kódot. Helyettesítse be az Azure SQL-adatbázis értékeit a,, és rendszerhez `<server>` `<database>` `<username>` `<password>` .

   >[!IMPORTANT]
   >Az ebben a példában szereplő kód a minta AdventureWorksLT-adatait használja, amelyeket az adatbázis létrehozásakor választhat forrásként. Ha az adatbázis különböző adatokkal rendelkezik, a SELECT lekérdezésben használja a saját adatbázisában lévő táblákat. 

   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>A kód futtatása

1. A parancssorban futtassa a következő parancsot:

   ```bash
   ruby sqltest.rb
   ```
   
1. Győződjön meg arról, hogy az adatbázisból az első 20 kategória/termék sorait adja vissza. 

## <a name="next-steps"></a>További lépések
- [Tervezze meg első Azure SQL Databaseét](design-first-database-tutorial.md).
- [A TinyTDS GitHub-tárháza](https://github.com/rails-sqlserver/tiny_tds).
- [Jelentheti a problémákat, vagy kérdéseket tehet fel a TinyTDS kapcsolatban](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Ruby-illesztőprogram a SQL Serverhoz](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
