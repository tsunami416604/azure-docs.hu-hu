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
ms.date: 02/12/2019
ms.openlocfilehash: d0dcfa3d5e387b03fe76eff34e32860ae5b17e76
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235370"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Gyors útmutató: Python használata Azure SQL Database-adatbázis lekérdezéséhez

 Ez a rövid útmutató azt ismerteti, hogyan lehet a [Python](https://python.org) használatával Azure SQL Database-adatbázishoz csatlakozni, valamint a Transact-SQL-utasítások használatával adatokat lekérdezni. További részletekért SDK-t, tekintse meg a [referencia](https://docs.microsoft.com/python/api/overview/azure/sql) dokumentációjában, a [pyodbc GitHub-adattár](https://github.com/mkleehammer/pyodbc/wiki/), és a egy [pyodbc-mintát](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

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
  
- Python és az operációs rendszerhez kapcsolódó szoftvereket:
  
  - **MacOS**: Telepítse a homebrew-t és a Python, telepítse az ODBC-illesztőt és az Sqlcmd-t, és telepítse az SQL Server a Python-illesztőt. 1.2, 1.3 és 2.1-es, a lépések [létrehozása Python-alkalmazásokat macOS rendszeren az SQL Server használata](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). További információkért lásd: [a Microsoft ODBC-illesztő telepítése Linux és macOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  - **Ubuntu**: Telepítse a Python és a többi szükséges csomagot `sudo apt-get install python python-pip gcc g++ build-essential`. Töltse le és telepítse az ODBC-illesztő Sqlcmd-t és a Python illesztőprogram SQL Serverhez készült. Útmutatásért lásd: [egy fejlesztési környezet konfigurálása a Python fejlesztési pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  - **Windows**: Telepítse a Python, az ODBC-illesztő és az Sqlcmd-t és a Python-illesztőt SQL Serverhez. Útmutatásért lásd: [egy fejlesztési környezet konfigurálása a Python fejlesztési pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>Az SQL server-kapcsolati adatok lekéréséhez

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információkat kaphat. A következő eljárások szüksége a kiszolgáló teljes nevét vagy a gazdagépnév, az adatbázis neve és a bejelentkezési adatait.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Keresse meg a **SQL-adatbázisok** vagy **SQL felügyelt példányai** lapot.

3. A a **áttekintése** lapon, tekintse át a teljes kiszolgálónevet melletti **kiszolgálónév** egy önálló adatbázis vagy a kiszolgáló teljes neve melletti **gazdagép** számára egy felügyelt a példány. Másolja ki a kiszolgáló nevét vagy az állomásnevet, rámutatnak, és válassza a **másolási** ikonra.

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

