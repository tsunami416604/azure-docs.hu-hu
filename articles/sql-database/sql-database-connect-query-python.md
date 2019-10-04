---
title: Python használata az Azure SQL Database lekérdezéséhez | Microsoft Docs
description: Ez a témakör bemutatja, hogyan használható a Python olyan program létrehozásához, amely egy Azure SQL Database-adatbázishoz csatlakozik, és hogyan kérdezheti le a Transact-SQL-utasítások használatával.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 659f6333d16f84cc35be45c45b7a7119e53fd0d0
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764282"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Gyors útmutató: Python használata Azure SQL-adatbázis lekérdezéséhez

 Ez a rövid útmutató azt ismerteti, hogyan lehet a [Python](https://python.org) használatával Azure SQL-adatbázishoz csatlakozni, valamint a Transact-SQL-utasítások használatával adatokat lekérdezni. További SDK-részletekért tekintse meg [a dokumentációt](https://docs.microsoft.com/python/api/overview/azure/sql) , a [pyodbc GitHub-tárházat](https://github.com/mkleehammer/pyodbc/wiki/)és egy [pyodbc-mintát](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Azure SQL-adatbázis. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist Azure SQL Databaseban:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Hozzon létre| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md)| [Kapcsolódás virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Kapcsolódás a webhelyről](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|Adventure Works betöltve|[Széles körű globális importőrök visszaállítása](sql-database-managed-instance-get-started-restore.md)
  |||Adventure Works visszaállítása vagy importálása a [BACPAC](sql-database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > A cikkben található parancsfájlok az Adventure Works-adatbázis használatára íródnak. Felügyelt példány esetén importálnia kell az Adventure Works-adatbázist egy példány-adatbázisba, vagy módosítania kell a jelen cikkben szereplő parancsfájlokat a Wide World Importálós adatbázis használatára.
  
- Python és az operációs rendszerhez kapcsolódó szoftverek:
  
  - **MacOS**: Telepítse a Homebrew-t és a Pythont, telepítse az ODBC-illesztőt és a SQLCMD, majd telepítse a Python-illesztőprogramot a SQL Serverhoz. Tekintse meg a 1,2, 1,3 és 2,1 lépéseket a [Python-alkalmazások létrehozása a macOS SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)című témakörben. További információ: [a Microsoft ODBC-illesztőprogram telepítése Linux és MacOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)rendszereken.

  - **Ubuntu**: Telepítse a Pythont és az egyéb `sudo apt-get install python python-pip gcc g++ build-essential`szükséges csomagokat a-val. Töltse le és telepítse az ODBC-illesztőt, a SQLCMD-t és a SQL Server Python-illesztőprogramját. Útmutatásért lásd: [fejlesztési környezet konfigurálása Pyodbc Python-fejlesztéshez](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  - **Windows**: Telepítse a Pythont, az ODBC-illesztőt és a SQLCMD, valamint a SQL Server Python-illesztőprogramját. Útmutatásért lásd: [fejlesztési környezet konfigurálása Pyodbc Python-fejlesztéshez](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Nyissa meg az **SQL-adatbázisok** vagy az SQL- **felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egyetlen adatbázishoz vagy a felügyelt példányhoz tartozó **gazdagép** melletti teljes kiszolgálónévhez. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

## <a name="create-code-to-query-your-sql-database"></a>Kód létrehozása az SQL-adatbázis lekérdezéséhez 

1. Egy szövegszerkesztőben hozzon létre egy új, *sqltest.py*nevű fájlt.  
   
1. Adja hozzá a következő kódot. Helyettesítse be a saját \<értékeit a \<kiszolgálói >, \<az adatbázis-> \<, a Felhasználónév > és a jelszó >.
   
   >[!IMPORTANT]
   >Az ebben a példában szereplő kód a minta AdventureWorksLT-adatait használja, amelyeket az adatbázis létrehozásakor választhat forrásként. Ha az adatbázis különböző adatokkal rendelkezik, a SELECT lekérdezésben használja a saját adatbázisában lévő táblákat. 
   
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

1. A parancssorban futtassa a következő parancsot:

   ```cmd
   python sqltest.py
   ```

1. Győződjön meg arról, hogy a 20 legfontosabb kategória/termék sorait adja vissza, majd a parancsablakban.

## <a name="next-steps"></a>További lépések

- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)
- [Microsoft Python-illesztőprogramok a SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python fejlesztői központ](https://azure.microsoft.com/develop/python/?v=17.23h)

