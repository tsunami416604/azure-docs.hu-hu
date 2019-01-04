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
ms.date: 12/20/2018
ms.openlocfilehash: 66819cbd65f6f044d0dac68326eb5890476964b6
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993909"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Gyors útmutató: Ruby használata Azure SQL Database-adatbázis lekérdezéséhez

Ez a rövid útmutató azt ismerteti, hogyan használhatja [Ruby](https://www.ruby-lang.org) szeretne csatlakozni egy Azure SQL adatbázishoz és adatok lekérdezése Transact-SQL-utasításokkal.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató elvégzéséhez szüksége van a következő előfeltételek vonatkoznak:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- A rövid útmutatóhoz használt számítógép nyilvános IP-címére vonatkozó [kiszolgálószintű tűzfalszabály](sql-database-get-started-portal-firewall.md).
  
- Ruby és a kapcsolódó szoftverek az operációs rendszer:
  
  - **macOS**: Telepítse a homebrew-val, az rbenv-t és a ruby-buildet, a Ruby, a FreeTDS és a TinyTDS. Tekintse meg a lépéseket, 1.2, 1.3, 1.4-es, 1.5-ös és a 2.1-es [macOS rendszeren az SQL Server használatával hozzon létre Ruby-alkalmazások](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Telepítse a Ruby, az rbenv-t és a ruby-buildet, a Ruby, a FreeTDS és a TinyTDS előfeltételei. Tekintse meg a lépéseket, 1.2, 1.3, 1.4-es, 1.5-ös és a 2.1-es [Ubuntu rendszeren az SQL Server használatával hozzon létre Ruby-alkalmazások](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: Telepítse a Rubyt, a Ruby fejlesztői készlet és a TinyTDS. Lásd: [fejlesztési környezet konfigurálása a Ruby fejlesztői](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Az SQL server-kapcsolati adatok lekéréséhez

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

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
