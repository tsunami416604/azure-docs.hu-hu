---
title: Ruby használata Azure SQL Database-adatbázis lekérdezéséhez | Microsoft Docs
description: Ez a témakör bemutatja, hogyan használhatja a Rubyt egy Azure SQL Database-adatbázishoz csatlakozó program létrehozásához, és hogyan hajthat végre lekérdezést Transact-SQL-utasításokkal.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: bea32acf4028a4ab43fcc8cd967a31ea397babf3
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234220"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Gyors útmutató: Ruby használata Azure SQL Database-adatbázis lekérdezéséhez

Ez a rövid útmutató azt ismerteti, hogyan használhatja [Ruby](https://www.ruby-lang.org) szeretne csatlakozni egy Azure SQL adatbázishoz és adatok lekérdezése Transact-SQL-utasításokkal.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató elvégzéséhez szüksége van a következő előfeltételek vonatkoznak:

- Azure SQL Database-adatbázis. Az alábbi rövid útmutatókban hozhat létre, és válassza az Azure SQL Database egy adatbázis is használja:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Létrehozás| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) |
  | Konfigurálás | [kiszolgálószintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md)| [Kapcsolat egy virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Helyszíni kapcsolat](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|Az Adventure Works betöltött száma a rövid útmutató|[Állítsa vissza a Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Állítsa vissza vagy importálása az Adventure Works [BACPAC](sql-database-import.md) fájlt [github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Ebben a cikkben a parancsfájlok az Adventure Works adatbázisa használatához készültek. Felügyelt példánnyal Ha az Adventure Works adatbázisa importálása-példány adatbázis, vagy módosítsa a parancsfájlokat ebben a cikkben a Wide World Importers-adatbázis használatára.
  
- Ruby és a kapcsolódó szoftverek az operációs rendszer:
  
  - **MacOS**: Telepítse a homebrew-val, az rbenv-t és a ruby-buildet, a Ruby, a FreeTDS és a TinyTDS. Tekintse meg a lépéseket, 1.2, 1.3, 1.4-es, 1.5-ös és a 2.1-es [macOS rendszeren az SQL Server használatával hozzon létre Ruby-alkalmazások](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Telepítse a Ruby, az rbenv-t és a ruby-buildet, a Ruby, a FreeTDS és a TinyTDS előfeltételei. Tekintse meg a lépéseket, 1.2, 1.3, 1.4-es, 1.5-ös és a 2.1-es [Ubuntu rendszeren az SQL Server használatával hozzon létre Ruby-alkalmazások](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: Telepítse a Rubyt, a Ruby fejlesztői készlet és a TinyTDS. Lásd: [fejlesztési környezet konfigurálása a Ruby fejlesztői](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Az SQL server-kapcsolati adatok lekéréséhez

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információkat kaphat. A következő eljárások szüksége a kiszolgáló teljes nevét vagy a gazdagépnév, az adatbázis neve és a bejelentkezési adatait.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Keresse meg a **SQL-adatbázisok** vagy **SQL felügyelt példányai** lapot.

3. A a **áttekintése** lapon, tekintse át a teljes kiszolgálónevet melletti **kiszolgálónév** egy önálló adatbázis vagy a kiszolgáló teljes neve melletti **gazdagép** számára egy felügyelt a példány. Másolja ki a kiszolgáló nevét vagy az állomásnevet, rámutatnak, és válassza a **másolási** ikonra. 

## <a name="create-code-to-query-your-sql-database"></a>Az SQL-adatbázis lekérdezéséhez kód létrehozása

1. A szöveges vagy a code szerkesztőt, hozzon létre egy új fájlt *sqltest.rb*.
   
1. Adja hozzá a következő kódot. Cserélje le az értékeket az Azure SQL database- `<server>`, `<database>`, `<username>`, és `<password>`.
   
   >[!IMPORTANT]
   >Ebben a példában a kódot az AdventureWorksLT mintaadatokat, amely forrásként is választja, az adatbázis létrehozásakor használ. Ha az adatbázis különböző adatokat, használja a saját adatbázis tábláinak a SELECT-lekérdezésben. 
   
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

1. Egy parancssorban futtassa a következő parancsot:

   ```bash
   ruby sqltest.rb
   ```
   
1. Győződjön meg arról, hogy az első 20 kategória/Product sort az adatbázisból ad vissza. 

## <a name="next-steps"></a>További lépések
- [Az első Azure SQL-adatbázis megtervezése](sql-database-design-first-database.md).
- [GitHub-adattár a tinytds-hez](https://github.com/rails-sqlserver/tiny_tds).
- [Problémák jelentése és kérdezés a tinytds-sel kapcsolatban](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Ruby-illesztőprogram SQL Serverhez](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
