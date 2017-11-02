---
title: "Python használata Azure SQL Database-adatbázis lekérdezéséhez | Microsoft Docs"
description: "Ez a témakör bemutatja, hogyan használhatja a Pythont egy Azure SQL Database-adatbázishoz csatlakozó program létrehozásához, és hogyan hajthat végre lekérdezést Transact-SQL-utasításokkal."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: On Demand
ms.tgt_pltfrm: n
ms.devlang: python
ms.topic: quickstart
ms.date: 08/08/2017
ms.author: carlrab
ms.openlocfilehash: dc1697520080cbdc8e53686f800ad122e69c2478
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="use-python-to-query-an-azure-sql-database"></a>Python használata Azure SQL Database-adatbázis lekérdezéséhez

 Ez a gyors üzembehelyezési útmutató ismerteti, hogyan használható a [Python](https://python.org) egy Azure SQL Database-adatbázishoz való csatlakozáshoz, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.

## <a name="prerequisites"></a>Előfeltételek

A gyors üzembehelyezési útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Azure SQL Database-adatbázis. Ez a rövid útmutató az alábbi rövid útmutatók egyikében létrehozott erőforrásokat használja: 

   - [DB létrehozása – portál](sql-database-get-started-portal.md)
   - [DB létrehozása – CLI](sql-database-get-started-cli.md)
   - [DB létrehozása – PowerShell](sql-database-get-started-powershell.md)

- A gyors üzembe helyezési útmutatóhoz használt számítógép nyilvános IP-címére vonatkozó [kiszolgálószintű tűzfalszabály](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).

- Telepítette a Pythont és az operációs rendszerének megfelelő kapcsolódó szoftvereket.

    - **MacOS**: Telepítse a Homebrew-t és a Pythont, telepítse az ODBC-illesztőt és az SQLCMD-t, majd telepítse az SQL Serverhez készült Python-illesztőt. Lásd az [1.2, 1.3 és 2.1 lépést](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).
    - **Ubuntu**: Telepítse a Pythont és a többi szükséges csomagot, majd telepítse az SQL Serverhez készült Python-illesztőt. Lásd az [1.2, 1.3 és 2.1 lépést](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu/).
    - **Windows**: Telepítse a Python legújabb verzióját (a környezeti változó konfigurálása automatikusan megtörténik), telepítse az ODBC-illesztőt és az SQLCMD-t, majd telepítse az SQL Serverhez készült Python-illesztőt. Lásd az [1.2, 1.3 és 2.1 lépést](https://www.microsoft.com/sql-server/developer-get-started/python/windows/). 

## <a name="sql-server-connection-information"></a>Az SQL-kiszolgáló kapcsolatadatai

Kérje le az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. A későbbi eljárásokban szüksége lesz a teljes kiszolgálónévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az adatbázis **Áttekintés** oldalán tekintse meg a teljes kiszolgálónevet, amint az az alábbi képen látható. Ha a mutatót a kiszolgáló neve fölé viszi, megjelenik a **Kattintson a másoláshoz** lehetőség.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Amennyiben elfelejtette a kiszolgálója bejelentkezési adatait, lépjen az SQL Database-kiszolgáló oldalára, és itt megtudhatja a kiszolgáló rendszergazdájának nevét, valamint szükség esetén új jelszót kérhet.     
    
## <a name="insert-code-to-query-sql-database"></a>Kód beszúrása SQL-adatbázis lekérdezéséhez 

1. Egy tetszőleges szövegszerkesztőben hozza létre a **sqltest.py** nevű új fájlt.  

2. Cserélje le a tartalmat a következő kódra, és adja meg a kiszolgáló és az adatbázis megfelelő adatait, valamint a felhasználót és a jelszót.

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
    print (str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()
```

## <a name="run-the-code"></a>A kód futtatása

1. Futtassa az alábbi parancsokat a parancssorban:

   ```Python
   python sqltest.py
   ```

2. Győződjön meg arról, hogy a parancssori felület visszaadta az első 20 sort, majd zárja be az alkalmazásablakot.

## <a name="next-steps"></a>Következő lépések

- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)
- [SQL Serverre készült Microsoft Python-illesztőprogramok](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python fejlesztői központ](https://azure.microsoft.com/develop/python/?v=17.23h)

