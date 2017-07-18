---
title: "Csatlakozás az Azure SQL Database-hez a Ruby használatával | Microsoft Docs"
description: "Egy Ruby-kódmintát jelenít meg, amellyel csatlakozhat egy Azure SQL Database-adatbázishoz, és lekérdezéseket hajthat végre."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: b25ef8333a2836f976a974d6ea6e7fdcea2745e3
ms.contentlocale: hu-hu
ms.lasthandoff: 07/08/2017

---

# <a name="azure-sql-database-use-ruby-to-connect-and-query-data"></a>Azure SQL Database: Csatlakozás és adatok lekérdezése a Ruby használatával

Ez a gyors üzembehelyezési útmutató ismerteti, hogyan használható a [Ruby](https://www.ruby-lang.org) az Azure SQL Database-adatbázishoz való csatlakozáshoz, majd hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban Mac OS és Ubuntu Linux platformokról.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató az alábbi rövid útmutatók egyikében létrehozott erőforrásokat használja kiindulási pontnak:

- [DB létrehozása – portál](sql-database-get-started-portal.md)
- [DB létrehozása – CLI](sql-database-get-started-cli.md)
- [DB létrehozása – PowerShell](sql-database-get-started-powershell.md)

## <a name="install-ruby-and-database-communication-libraries"></a>A Ruby és az adatbázis-kommunikációs könyvtárak telepítése

A jelen szakaszban ismertetett lépések feltételezik, hogy Ön rendelkezik fejlesztési tapasztalatokkal a Ruby használatával, az Azure SQL Database használatában pedig még járatlan. Ha még nincs tapasztalata a Ruby-fejlesztésekben, lépjen az [alkalmazások SQL Serverrel való készítését](https://www.microsoft.com/en-us/sql-server/developer-get-started/) bemutató cikkre, válassza a **Ruby** lehetőséget, majd válassza ki az operációs rendszert.

### <a name="mac-os"></a>**Mac OS**
Nyissa meg a terminált, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a Ruby-szkriptet. A **brew**, a **FreeTDS** és a **TinyTDS** telepítéséhez használja a következő parancsokat.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install FreeTDS
gem install tiny_tds
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Nyissa meg a terminált, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a Ruby-szkriptet. A **FreeTDS** és a **TinyTDS** telepítéséhez használja a következő parancsokat.

```bash
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.00.27.tar.gz
tar -xzf freetds-1.00.27.tar.gz
cd freetds-1.00.27
./configure --prefix=/usr/local --with-tdsver=7.3
make
make install
gem install tiny_tds
```

## <a name="sql-server-connection-information"></a>Az SQL-kiszolgáló kapcsolatadatai

Kérje le az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. A későbbi eljárásokban szüksége lesz a teljes kiszolgálónévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az adatbázis **Áttekintés** lapján tekintse meg a teljes kiszolgálónevet, amint az alábbi képen is látható. Ha a mutatót a kiszolgáló neve fölé viszi, megjelenik a **Kattintson a másoláshoz** lehetőség. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Amennyiben elfelejtette a kiszolgálója bejelentkezési adatait, lépjen az SQL Database-kiszolgáló oldalára, és itt megtudhatja a kiszolgáló rendszergazdájának nevét, valamint szükség esetén új jelszót kérhet.
    

## <a name="select-data"></a>Adatok kiválasztása
A következő kóddal lekérdezheti kategóriánként az első 20 terméket a [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) függvény és egy [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL-utasítás használatával. A TinyTDS::Client függvény elfogad egy lekérdezést, és visszaad egy eredményhalmazt. Az eredményhalmazon a rendszer a [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds) kóddal iterál végig. Cserélje le a kiszolgáló, az adatbázis, a felhasználónév és a jelszó paramétereit azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksL- mintaadatokkal létrehozta az adatbázist.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
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

## <a name="insert-data"></a>Adat beszúrása
A következő kóddal beszúrhat egy új terméket a SalesLT.Product táblába a [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) függvény és egy [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL-utasítás használatával. Cserélje le a kiszolgáló, az adatbázis, a felhasználónév és a jelszó paramétereit azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksL- mintaadatokkal létrehozta az adatbázist.

Ez a példa bemutatja, hogyan hajtható végre biztonságosan egy INSERT utasítás, hogyan adhatók át az alkalmazást az [SQL-injektálási](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) támadások ellen védő paraméterek, és hogyan kérhető le az automatikusan létrehozott [elsődleges kulcs](https://docs.microsoft.com/sql/relational-databases/tables/primary-and-foreign-key-constraints) értéke.    
  
A TinyTDS Azure-ral való használatához egyes `SET` utasítások végrehajtása ajánlott annak módosításához, hogyan kezelje az aktuális munkamenet az egyes adatokat. Az ajánlott `SET` utasítások a kódmintában találhatók. Például a(z) `SET ANSI_NULL_DFLT_ON` engedélyezi az újonnan létrehozott oszlopok számára a nullértékek engedélyezését akkor is, ha az adott oszlop nullázhatósági állapota nincs explicit módon meghatározva.  
  
A Microsoft SQL Server [datetime](https://docs.microsoft.com/sql/t-sql/data-types/datetime-transact-sql) formátumával való egyeztetéshez használja a [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) függvényt, amely átalakítja a formátumot a megfelelő datetime formátumra.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def insert(name, productnumber, color, standardcost, listprice, sellstartdate)
    tsql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) 
        VALUES (N'#{name}', N'#{productnumber}',N'#{color}',N'#{standardcost}',N'#{listprice}',N'#{sellstartdate}')"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
insert('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')
```

## <a name="update-data"></a>Adatok frissítése
A következő kóddal frissítheti az előzőleg felvett új terméket a [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) függvény és egy [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL-utasítás használatával. Cserélje le a kiszolgáló, az adatbázis, a felhasználónév és a jelszó paramétereit azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksL- mintaadatokkal létrehozta az adatbázist.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true
    
def update(name, listPrice, client)
    tsql = "UPDATE SalesLT.Product SET ListPrice = N'#{listPrice}' WHERE Name =N'#{name}'";
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
update('BrandNewProduct', 500, client)
```

## <a name="delete-data"></a>Adat törlése
A következő kóddal törölheti az előzőleg hozzáadott új terméket a [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) függvény és egy [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL-utasítás használatával. Cserélje le a kiszolgáló, az adatbázis, a felhasználónév és a jelszó paramétereit azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksL- mintaadatokkal létrehozta az adatbázist.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def delete(name, client)
    tsql = "DELETE FROM SalesLT.Product WHERE Name = N'#{name}'"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
delete('BrandNewProduct', client)
```

## <a name="next-steps"></a>Következő lépések
- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)
- [GitHub-adattár a TinyTDS-hez](https://github.com/rails-sqlserver/tiny_tds)
- [Hibák jelentése/kérdések felvetése](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Ruby-illesztőprogramok az SQL Serverhez](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)


