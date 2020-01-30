---
title: A Python használata adatbázis lekérdezéséhez
description: Ez a témakör bemutatja, hogyan használható a Python olyan program létrehozásához, amely egy Azure SQL Database-adatbázishoz csatlakozik, és hogyan kérdezheti le a Transact-SQL-utasítások használatával.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: e82f8feae0096202e48a58296dd2e9d21bb61885
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768579"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Rövid útmutató: Python használata Azure SQL-adatbázis lekérdezéséhez

Ebben a rövid útmutatóban a Python használatával csatlakozik egy Azure SQL Database-adatbázishoz, és T-SQL-utasítások használatával kérdez le adatokat.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy [Azure SQL Database-adatbázis](sql-database-single-database-get-started.md)
- [Python](https://python.org/downloads) 3 és kapcsolódó szoftverek

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  A Homebrew és a Python, az ODBC-illesztő és a SQLCMD, valamint a SQL Server Python-illesztőprogramjának telepítéséhez használja a **1,2**, **1,3**és **2,1** lépéseket a [python-alkalmazások létrehozása a MacOS rendszeren SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  További információ: [Microsoft ODBC-illesztőprogram MacOS rendszeren](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  A Python és az egyéb szükséges csomagok telepítéséhez használja a `sudo apt-get install python python-pip gcc g++ build-essential`.

  Az ODBC-illesztő, a SQLCMD és a SQL Server Python-illesztőprogram telepítéséhez lásd: [környezet konfigurálása Pyodbc Python-fejlesztéshez](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  További információ: [Microsoft ODBC-illesztőprogram Linux rendszeren](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  A Python, az ODBC-illesztő és a SQLCMD, valamint a SQL Server Python-illesztőprogramjának telepítéséhez tekintse meg a [környezet konfigurálása a Pyodbc Python-fejlesztéshez](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)című témakört.

  További információ: [Microsoft ODBC-illesztő](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---

> [!IMPORTANT]
> A cikkben található parancsfájlok az **Adventure Works** -adatbázis használatára íródnak.

> [!NOTE]
> Dönthet úgy is, hogy egy Azure SQL felügyelt példányt használ.
>
> A létrehozásához és konfigurálásához használja az [Azure Portalt](sql-database-managed-instance-get-started.md), a [PowerShellt](scripts/sql-database-create-configure-managed-instance-powershell.md)vagy a [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)-t, majd a helyszíni vagy a [VM](sql-database-managed-instance-configure-vm.md) [-](sql-database-managed-instance-configure-p2s.md) kapcsolat beállítását.
>
> Az betöltéssel kapcsolatban lásd: [visszaállítás a BACPAC](sql-database-import.md) az [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) -fájllal, vagy lásd: [a Wide World](sql-database-managed-instance-get-started-restore.md)importing-adatbázis visszaállítása.

A Python és az Azure SQL Database további megismeréséhez tekintse meg a [Pythonhoz készült Azure SQL Database-kódtárakat](/python/api/overview/azure/sql), a [pyodbc-tárházat](https://github.com/mkleehammer/pyodbc/wiki/)és egy [pyodbc-mintát](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Nyissa meg az **SQL-adatbázisok** vagy az SQL- **felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egyetlen adatbázishoz vagy a felügyelt példányhoz tartozó **gazdagép** melletti teljes kiszolgálónévhez. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

## <a name="create-code-to-query-your-sql-database"></a>Kód létrehozása az SQL-adatbázis lekérdezéséhez 

1. Egy szövegszerkesztőben hozzon létre egy új, *sqltest.py*nevű fájlt.  
   
1. Adja hozzá a következő kódot. Helyettesítse be a saját értékeit \<Server >, \<adatbázis >, \<username > és \<Password >.
   
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

## <a name="next-steps"></a>Következő lépések

- [Az első Azure SQL-adatbázis megtervezése](sql-database-design-first-database.md)
- [Microsoft Python-illesztőprogramok a SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python fejlesztői központ](https://azure.microsoft.com/develop/python/?v=17.23h)

