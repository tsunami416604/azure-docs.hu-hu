---
title: "Csatlakozás az Azure SQL Database-hez Python használatával | Microsoft Docs"
description: "Egy Python-kódmintát jelenít meg, amellyel csatlakozhat egy Azure SQL Database-adatbázishoz, és lekérdezéseket hajthat végre."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 99195b43a1577f978562864bac5fa12cdeb95d63
ms.contentlocale: hu-hu
ms.lasthandoff: 06/17/2017

---
<a id="azure-sql-database-use-python-to-connect-and-query-data" class="xliff"></a>

# Azure SQL Database: Csatlakozás és adatlekérdezés a Python használatával

 Ez a gyors üzembehelyezési útmutató ismerteti, hogyan használható a [Python](https://python.org) az Azure SQL Database-adatbázishoz való csatlakozáshoz, majd hogyan lehet Transact-SQL-utasítások használatával lekérdezni, beszúrni, frissíteni és adatot törölni az adatbázisban a Mac OS, Ubuntu Linux és Windows platformokról.

<a id="prerequisites" class="xliff"></a>

## Előfeltételek

Ez a rövid útmutató az alábbi rövid útmutatók egyikében létrehozott erőforrásokat használja kiindulási pontnak:

- [DB létrehozása – portál](sql-database-get-started-portal.md)
- [DB létrehozása – CLI](sql-database-get-started-cli.md)
- [DB létrehozása – PowerShell](sql-database-get-started-powershell.md)

<a id="install-the-python-and-database-communication-libraries" class="xliff"></a>

## A Python és az adatbázisokkal való kommunikációra való kódtárak telepítése

A jelen szakaszban ismertetett lépések feltételezik, hogy Ön rendelkezik a Pythonnal végzett fejlesztésekkel kapcsolatos tapasztalatokkal, az Azure SQL Database használatában pedig még járatlan. Ha még tapasztalatlan a Python-fejlesztésekben, lépjen az [alkalmazások SQL Serverrel történő összeállításával](https://www.microsoft.com/sql-server/developer-get-started/) foglalkozó témakörre, válassza ki a **Python** nyelvet, majd a használt operációs rendszert.

<a id="mac-os" class="xliff"></a>

### **Mac OS**
Nyissa meg a terminált, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a Python szkriptet. A **brew**, a **Mac rendszerre készült Microsoft ODBC-illesztő** és a **pyodbc** telepítéséhez használja a következő parancsokat. A pyodbc a Linux rendszeren a Microsoft ODBC-illesztőt használja az SQL Database-adatbázisokhoz való csatlakozásra.

``` bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

<a id="linux-ubuntu" class="xliff"></a>

### **Linux (Ubuntu)**
Nyissa meg a terminált, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a Python szkriptet. A **Linux rendszerre készült Microsoft ODBC-illesztő** és a **pyodbc** telepítéséhez használja a következő parancsokat. A pyodbc a Linux rendszeren a Microsoft ODBC-illesztőt használja az SQL Database-adatbázisokhoz való csatlakozásra.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev
sudo pip install pyodbc==3.1.1
```

<a id="windows" class="xliff"></a>

### **Windows**
Telepítse a [Microsoft ODBC-illesztő 13.1 verzióját](https://www.microsoft.com/download/details.aspx?id=53339) (frissítse az illesztőt, ha a gép erre kéri). A pyodbc a Linux rendszeren a Microsoft ODBC-illesztőt használja az SQL Database-adatbázisokhoz való csatlakozásra. 

Ezután telepítse a **pyodbc**-t a pip használatával.

```cmd
pip install pyodbc==3.1.1
```

A pip használatának engedélyezésével kapcsolatos utasításokat [itt](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows) találja

<a id="get-connection-information" class="xliff"></a>

## Kapcsolatadatok lekérése

Kérje le az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. A későbbi eljárásokban szüksége lesz a teljes kiszolgálónévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az adatbázis **Áttekintés** lapján tekintse meg a teljes kiszolgálónevet, amint az alábbi képen is látható. Ha a mutatót a kiszolgáló neve fölé viszi, megjelenik a **Kattintson a másoláshoz** lehetőség. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Amennyiben elfelejtette a kiszolgálója bejelentkezési adatait, lépjen az SQL Database-kiszolgáló oldalára, és itt megtudhatja a kiszolgáló rendszergazdájának nevét, valamint szükség esetén új jelszót kérhet.     
   
<a id="select-data" class="xliff"></a>

## Adatok kiválasztása

A következő kód használatával lekérdezheti kategóriánként az első 20 terméket a [pyodbc.connect](https://github.com/mkleehammer/pyodbc/wiki) függvény egy [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL-utasítással való használatával. A [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) függvénnyel lekérheti egy lekérdezés eredményhalmazát egy SQL Database-adatbázisból. Ez a függvény elfogad egy lekérdezést és visszaad egy eredményhalmazt, amely megismételhető a **cursor.fetchone()** segítségével. Cserélje le a kiszolgáló, az adatbázis, a felhasználónév és a jelszó paramétereit azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksL- mintaadatokkal létrehozta az adatbázist.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1])
    row = cursor.fetchone()
```

<a id="insert-data" class="xliff"></a>

## Adat beszúrása
A következő kód használatával beszúrhat egy új terméket a SalesLT.Product táblába a [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) függvénnyel és az [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL-utasítással. Cserélje le a kiszolgáló, az adatbázis, a felhasználónév és a jelszó paramétereit azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksL- mintaadatokkal létrehozta az adatbázist.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

<a id="update-data" class="xliff"></a>

## Adatok frissítése
A következő kód használatával frissítheti az előzőleg hozzáadott új terméket a [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) függvénnyel és az [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL-utasítással. Cserélje le a kiszolgáló, az adatbázis, a felhasználónév és a jelszó paramétereit azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksL- mintaadatokkal létrehozta az adatbázist.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```

<a id="delete-data" class="xliff"></a>

## Adat törlése
A következő kód használatával törölheti az előzőleg hozzáadott új terméket a [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) függvénnyel és a [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL-utasítással. Cserélje le a kiszolgáló, az adatbázis, a felhasználónév és a jelszó paramétereit azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksL- mintaadatokkal létrehozta az adatbázist.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

<a id="next-steps" class="xliff"></a>

## Következő lépések

- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)
- [SQL Serverre készült Microsoft Python-illesztőprogramok](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python fejlesztői központ](https://azure.microsoft.com/develop/python/?v=17.23h)


