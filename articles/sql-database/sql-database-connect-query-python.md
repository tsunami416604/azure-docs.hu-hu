---
title: Python használata Azure SQL Database-adatbázis lekérdezéséhez | Microsoft Docs
description: Ez a témakör bemutatja, hogyan használhatja a Pythont egy Azure SQL Database-adatbázishoz csatlakozó program létrehozásához, és hogyan hajthat végre lekérdezést Transact-SQL-utasításokkal.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: python
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: carlrab
ms.openlocfilehash: e8928a69436cfa0fc601c4a0c544433f3b87a90f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790814"
---
# <a name="use-python-to-query-an-azure-sql-database"></a>Python használata Azure SQL Database-adatbázis lekérdezéséhez

 Ez a rövid útmutató azt ismerteti, hogyan lehet a [Python](https://python.org) használatával Azure SQL Database-adatbázishoz csatlakozni, valamint a Transact-SQL-utasítások használatával adatokat lekérdezni. Az SDK-val kapcsolatos további részletekért tekintse meg a [referenciadokumentációt](https://docs.microsoft.com/python/api/overview/azure/sql), a [pyodbc-mintát](https://github.com/mkleehammer/pyodbc/wiki/Getting-started) és a [pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) GitHub-adattárat.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- A rövid útmutatóhoz használt számítógép nyilvános IP-címére vonatkozó [kiszolgálószintű tűzfalszabály](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).

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
cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
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

