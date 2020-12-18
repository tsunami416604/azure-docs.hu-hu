---
title: A Python használata adatbázis lekérdezéséhez
description: Ez a témakör bemutatja, hogyan használható a Python egy olyan program létrehozásához, amely Azure SQL Database-adatbázishoz kapcsolódik, és hogyan kérdezheti le azt a Transact-SQL-utasítások használatával.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: a87c08d81724a6639fce6b6673b139b3ef94e410
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680050"
---
# <a name="quickstart-use-python-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Gyors útmutató: Azure SQL Database vagy Azure SQL felügyelt példányban lévő adatbázis lekérdezése a Python használatával
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ebben a rövid útmutatóban a Python használatával csatlakozhat Azure SQL Database vagy az Azure SQL felügyelt példányához, és a T-SQL-utasítások használatával adatokat kérdez le.

## <a name="prerequisites"></a>Előfeltételek

Ennek a rövid útmutatónak az elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

[!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 és kapcsolódó szoftverek

  # <a name="macos"></a>[macOS](#tab/macos)

  A Homebrew és a Python, az ODBC-illesztő és a SQLCMD, valamint a SQL Server Python-illesztőprogramjának telepítéséhez használja a **1,2**, **1,3** és **2,1** lépéseket a [python-alkalmazások létrehozása a MacOS rendszeren SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  További információ: [Microsoft ODBC-illesztőprogram MacOS rendszeren](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  A Python és az egyéb szükséges csomagok telepítéséhez használja a következőt: `sudo apt-get install python python-pip gcc g++ build-essential` .

  Az ODBC-illesztő, a SQLCMD és a SQL Server Python-illesztőprogram telepítéséhez lásd: [környezet konfigurálása Pyodbc Python-fejlesztéshez](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  További információ: [Microsoft ODBC-illesztőprogram Linux rendszeren](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windows"></a>[Windows](#tab/windows)

  A Python, az ODBC-illesztő és a SQLCMD, valamint a SQL Server Python-illesztőprogramjának telepítéséhez tekintse meg a [környezet konfigurálása a Pyodbc Python-fejlesztéshez](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)című témakört.

  További információ: [Microsoft ODBC-illesztő](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---
A Python és az adatbázis Azure SQL Databaseban való megismeréséhez lásd: [Azure SQL Database kódtárak a Pythonhoz](/python/api/overview/azure/sql), a [pyodbc adattárhoz](https://github.com/mkleehammer/pyodbc/wiki/)és egy [pyodbc-mintához](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-server-connection-information"></a>Kiszolgáló-kapcsolatok adatainak beolvasása

A Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok lekérése. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Nyissa meg az **SQL-adatbázisok**  vagy az SQL- **felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett, Azure SQL Database vagy a **gazdagép** melletti teljes kiszolgálónevet (vagy IP-címet) egy Azure SQL felügyelt példányhoz, vagy SQL Server az Azure virtuális gépen. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

> [!NOTE]
> SQL Server Azure-beli virtuális gépen való kapcsolódásával kapcsolatos információkért lásd: [kapcsolódás SQL Server-példányhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-your-database"></a>Kód létrehozása az adatbázis lekérdezéséhez 

1. Egy szövegszerkesztőben hozzon létre egy új, *sqltest.py* nevű fájlt.  
   
1. Adja hozzá a következő kódot. Helyettesítse be a, a, a és a saját értékeit \<server> \<database> \<username> \<password> .
   
   >[!IMPORTANT]
   >Az ebben a példában szereplő kód a minta AdventureWorksLT-adatait használja, amelyeket az adatbázis létrehozásakor választhat forrásként. Ha az adatbázis különböző adatokkal rendelkezik, a SELECT lekérdezésben használja a saját adatbázisában lévő táblákat. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
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

1. Győződjön meg arról, hogy a 20 legfontosabb kategória/termék sorait adja vissza, majd zárjuk be a parancssorablakot.

## <a name="next-steps"></a>Következő lépések

- [Az első adatbázis megtervezése Azure SQL Database](design-first-database-tutorial.md)
- [Microsoft Python-illesztőprogramok a SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Python fejlesztői központ](https://azure.microsoft.com/develop/python/?v=17.23h)
