---
title: Lekérdezés ruby használatával
description: Ez a témakör bemutatja, hogyan használhatja a Rubyt egy Azure SQL-adatbázishoz csatlakozó program létrehozásához, és hogyan hajthat végre lekérdezést Transact-SQL-utasításokkal.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 4bef55b049ee542efdb9d72d13fa196c989c75ec
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826924"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Rövid útmutató: Ruby használata Azure SQL-adatbázis lekérdezéséhez

Ez a rövid útmutató bemutatja, hogyan [lehet](https://www.ruby-lang.org) ruby segítségével csatlakozni egy Azure SQL-adatbázis és a transact-SQL utasítások lekérdezési adatok.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához a következő előfeltételekre van szükség:

- Azure SQL-adatbázis. Az alábbi rövid útmutatók egyikével hozhat létre, majd konfigurálhat egy adatbázist az Azure SQL Database-ben:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Létrehozás| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Powershell](scripts/sql-database-create-and-configure-database-powershell.md) | [Powershell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurálás | [Kiszolgálószintű IP-tűzfal szabály](sql-database-server-level-firewall-rule.md)| [Kapcsolódás virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Helyszíni kapcsolat](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|A kalandworks betöltve egy rövid útmutató|[Széles világbeli importőrök visszaállítása](sql-database-managed-instance-get-started-restore.md)
  |||Kalandorok visszaállítása vagy importálása [a BACPAC-fájlból](sql-database-import.md) a [GitHubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > A cikkben szereplő parancsfájlok a Kalandorbolt-adatbázis használatára íródnak. Felügyelt példány esetén vagy importálnia kell a Kalandorbolt-adatbázist egy példányadatbázisba, vagy módosítania kell a cikkparancsfájljait a Wide World Importers adatbázis használatához.
  
- Ruby és a kapcsolódó szoftver az operációs rendszer:
  
  - **MacOS**: Telepítse a Homebrew, rbenv és ruby-build, Ruby, FreeTDS és TinyTDS. Lásd: 1.2, 1.3, 1.4, 1.5 és 2.1 lépés a [Ruby-alkalmazások létrehozása macOS rendszeren az SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)című témakörben.
  
  - **Ubuntu**: Telepítse előfeltételeit Ruby, rbenv és rubin-build, Ruby, FreeTDS, és TinyTDS. Lásd: 1.2, 1.3, 1.4, 1.5 és 2.1 [lépés a Ruby alkalmazások létrehozása az SQL Server használatával ubuntuban](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)című témakörben.
  
  - **Windows**: Telepítse a Ruby, Ruby Devkit és TinyTDS. Lásd: [Fejlesztői környezet konfigurálása a Ruby fejlesztéshez.](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)

## <a name="get-sql-server-connection-information"></a>SQL-kiszolgálókapcsolati adatok beszerezése

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információk beszerezése. A közelgő eljárásokhoz szüksége lesz a teljesen minősített kiszolgáló- vagy állomásnévre, az adatbázis nevére és bejelentkezési adataira.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Keresse meg az **SQL-adatbázisok** vagy az **SQL felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a **kiszolgáló neve** melletti teljesen minősített kiszolgálónevet egyetlen adatbázishoz, vagy a teljesen minősített kiszolgálónevet a felügyelt példány **gazdagépe** mellett. A kiszolgáló vagy az állomásnév másolásához mutasson rá, és válassza a **Másolás ikont.** 

## <a name="create-code-to-query-your-sql-database"></a>Kód létrehozása az SQL-adatbázis lekérdezéséhez

1. Szöveg- vagy kódszerkesztőben hozzon létre egy *új fájlt sqltest.rb*néven.
   
1. Adja hozzá a következő kódot. Helyettesítse az Azure SQL-adatbázis értékeit a `<server>`, `<database>`, `<username>`és `<password>`a lehetőségre.
   
   >[!IMPORTANT]
   >A példában szereplő kód az AdventureWorksLT-minta adatait használja, amelyeket az adatbázis létrehozásakor forrásként választhat. Ha az adatbázis különböző adatokat használ, használjon saját adatbázistábláit a SELECT lekérdezésben. 
   
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
   
1. Ellenőrizze, hogy az adatbázis első 20 kategória/termék sorát adja-e vissza. 

## <a name="next-steps"></a>További lépések
- [Tervezze meg első Azure SQL-adatbázisát.](sql-database-design-first-database.md)
- [GitHub-tárház a TinyTDS számára.](https://github.com/rails-sqlserver/tiny_tds)
- [Jelentse a TinyTDS-sel kapcsolatos kérdéseket, vagy tegyen fel kérdéseket.](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Ruby driver az SQL Server hez](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
