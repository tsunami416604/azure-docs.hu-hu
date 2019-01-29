---
title: Python használata Azure SQL Database-adatbázis lekérdezéséhez | Microsoft Docs
description: Ez a témakör bemutatja, hogyan használhatja a Pythont egy Azure SQL Database-adatbázishoz csatlakozó program létrehozásához, és végre lekérdezést Transact-SQL-utasításokkal.
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
ms.date: 01/28/2019
ms.openlocfilehash: b611eb02203c872e3497b5b7c12acddd9eab14c0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188388"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Gyors útmutató: Python használata Azure SQL Database-adatbázis lekérdezéséhez

 Ez a rövid útmutató azt ismerteti, hogyan lehet a [Python](https://python.org) használatával Azure SQL Database-adatbázishoz csatlakozni, valamint a Transact-SQL-utasítások használatával adatokat lekérdezni. További részletekért SDK-t, tekintse meg a [referencia](https://docs.microsoft.com/python/api/overview/azure/sql) dokumentációjában, a [pyodbc GitHub-adattár](https://github.com/mkleehammer/pyodbc/wiki/), és a egy [pyodbc-mintát](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- Python és az operációs rendszerhez kapcsolódó szoftvereket:
  
  - **MacOS**: Telepítse a homebrew-t és a Python, telepítse az ODBC-illesztőt és az Sqlcmd-t, és telepítse az SQL Server a Python-illesztőt. 1.2, 1.3 és 2.1-es, a lépések [létrehozása Python-alkalmazásokat macOS rendszeren az SQL Server használata](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). További információkért lásd: [a Microsoft ODBC-illesztő telepítése Linux és macOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).
    
  - **Ubuntu**: Telepítse a Python és a többi szükséges csomagot `sudo apt-get install python python-pip gcc g++ build-essential`. Töltse le és telepítse az ODBC-illesztő Sqlcmd-t és a Python illesztőprogram SQL Serverhez készült. Útmutatásért lásd: [egy fejlesztési környezet konfigurálása a Python fejlesztési pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).
    
  - **Windows**: Telepítse a Python, az ODBC-illesztő és az Sqlcmd-t és a Python-illesztőt SQL Serverhez. Útmutatásért lásd: [egy fejlesztési környezet konfigurálása a Python fejlesztési pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>Az SQL server-kapcsolati adatok lekéréséhez

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="create-code-to-query-your-sql-database"></a>Az SQL-adatbázis lekérdezéséhez kód létrehozása 

1. Egy szövegszerkesztőben hozzon létre egy új fájlt *sqltest.py*.  
   
1. Adja hozzá a következő kódot. A saját értékeit helyettesítse \<kiszolgáló >, \<adatbázis >, \<username >, és \<jelszó >.
   
   >[!IMPORTANT]
   >Ebben a példában a kódot az AdventureWorksLT mintaadatokat, amely forrásként is választja, az adatbázis létrehozásakor használ. Ha az adatbázis különböző adatokat, használja a saját adatbázis tábláinak a SELECT-lekérdezésben. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>A kód futtatása

1. Egy parancssorban futtassa a következő parancsot:

   ```cmd
   python sqltest.py
   ```

1. Győződjön meg arról, hogy a felső 20 kategória/Product sorokat adja vissza, majd zárja a parancsablakot.

## <a name="next-steps"></a>További lépések

- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)
- [SQL Serverre készült Microsoft Python-illesztőprogramok](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python fejlesztői központ](https://azure.microsoft.com/develop/python/?v=17.23h)

