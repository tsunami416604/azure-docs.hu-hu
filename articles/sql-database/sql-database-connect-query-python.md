---
title: Python használata Azure SQL Database-adatbázis lekérdezéséhez | Microsoft Docs
description: Ez a témakör bemutatja, hogyan használhatja a Pythont egy Azure SQL Database-adatbázishoz csatlakozó program létrehozásához, és hogyan hajthat végre lekérdezést Transact-SQL-utasításokkal.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 2ab5b7a5b17daef00cb62f69a7d2a798c18456bb
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912951"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Rövid útmutató: Python használata Azure SQL Database-adatbázis lekérdezéséhez

 Ez a rövid útmutató azt ismerteti, hogyan lehet a [Python](https://python.org) használatával Azure SQL Database-adatbázishoz csatlakozni, valamint a Transact-SQL-utasítások használatával adatokat lekérdezni. Az SDK-val kapcsolatos további részletekért tekintse meg a [referenciadokumentációt](https://docs.microsoft.com/python/api/overview/azure/sql), a [pyodbc-mintát](https://github.com/mkleehammer/pyodbc/wiki/Getting-started) és a [pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) GitHub-adattárat.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- A rövid útmutatóhoz használt számítógép nyilvános IP-címére vonatkozó [kiszolgálószintű tűzfalszabály](sql-database-get-started-portal-firewall.md).

- Telepítette a Pythont és az operációs rendszerének megfelelő kapcsolódó szoftvereket:

    - **MacOS**: Telepítse a Homebrew-t és a Pythont, telepítse az ODBC-illesztőt és az SQLCMD-t, majd telepítse az SQL Serverhez készült Python-illesztőt. Lásd az [1.2, 1.3 és 2.1 lépést](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).
    - **Ubuntu**: Telepítse a Pythont és a többi szükséges csomagot, majd telepítse az SQL Serverhez készült Python-illesztőt. Lásd az [1.2, 1.3 és 2.1 lépést](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu/).
    - **Windows**: Telepítse a Python legújabb verzióját (a környezeti változó konfigurálása automatikusan megtörténik), telepítse az ODBC-illesztőt és az SQLCMD-t, majd telepítse az SQL Serverhez készült Python-illesztőt. Lásd az [1.2, 1.3 és 2.1 lépést](https://www.microsoft.com/sql-server/developer-get-started/python/windows/). 

## <a name="sql-server-connection-information"></a>Az SQL-kiszolgáló kapcsolatadatai

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
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
cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
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

## <a name="next-steps"></a>További lépések

- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)
- [SQL Serverre készült Microsoft Python-illesztőprogramok](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python fejlesztői központ](https://azure.microsoft.com/develop/python/?v=17.23h)

