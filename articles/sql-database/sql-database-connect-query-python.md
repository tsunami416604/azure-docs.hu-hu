---
title: Adatbázis lekérdezése pythonnal
description: Ez a témakör bemutatja, hogyan hozhat létre python-t egy olyan program létrehozásához, amely egy Azure SQL-adatbázishoz csatlakozik, és transact-SQL-utasítások használatával lekérdezi azt.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768579"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Rövid útmutató: Python használata Azure SQL-adatbázis lekérdezéséhez

Ebben a rövid útmutatóban a Python használatával csatlakozhat egy Azure SQL-adatbázishoz, és t-SQL-utasítások használatával adatoklekérdezéséhez.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure SQL-adatbázis](sql-database-single-database-get-started.md)
- [Python](https://python.org/downloads) 3 és kapcsolódó szoftverek

  # <a name="macos"></a>[Macos](#tab/macos)

  A Homebrew és a Python, az ODBC-illesztőprogram és az SQLCMD, valamint az SQL Server Python-illesztőprogramjának telepítéséhez használja az **1.2,** **1.3**és **2.1** lépéseket a [Python-alkalmazások létrehozása a macOS rendszeren futó SQL Server használatával.](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)

  További információt a [Microsoft ODBC Driver on macOS című témakörben talál.](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  A Python és más szükséges `sudo apt-get install python python-pip gcc g++ build-essential`csomagok telepítéséhez használja a használatát.

  Az ODBC-illesztőprogram, az SQLCMD és az SQL Server Python-illesztőprogram telepítéséről a [Pyodbc Python-fejlesztés környezetének konfigurálása](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)című témakörben található.

  További információt a [Microsoft ODBC Driver on Linux című témakörben talál.](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)

  # <a name="windows"></a>[Windows](#tab/windows)

  A Python, az ODBC-illesztőprogram és az SQLCMD, valamint az SQL Server Python-illesztőprogram telepítéséről a [Pyodbc Python-fejlesztés környezetének konfigurálása című](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)témakörben található.

  További információt a [Microsoft ODBC illesztőprogram ban talál.](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)

---

> [!IMPORTANT]
> A cikkben szereplő parancsfájlok a **Kalandorbolt-adatbázis** használatára íródnak.

> [!NOTE]
> Igény szerint választhat egy Azure SQL felügyelt példány használata.
>
> Létrehozása és konfigurálása, használja az [Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)vagy [CLI,](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)majd állítsa be [a helyszínen](sql-database-managed-instance-configure-p2s.md) vagy [virtuális gép](sql-database-managed-instance-configure-vm.md) kapcsolat.
>
> Az adatok betöltéséhez olvassa el a [VISSZAÁLLÍTÁS a BACPAC fájllal](sql-database-import.md) című témakört a [Kalandorbolt](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) fájllal, vagy olvassa el [a Wide World Importers adatbázis visszaállítása című témakört.](sql-database-managed-instance-get-started-restore.md)

A Python és az Azure SQL-adatbázis további megismeréséhez tekintse meg a [Python Azure SQL-adatbázis-tárakat](/python/api/overview/azure/sql), a [pyodbc-tárházat](https://github.com/mkleehammer/pyodbc/wiki/)és egy [pyodbc-mintát.](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)

## <a name="get-sql-server-connection-information"></a>SQL-kiszolgálókapcsolati adatok beszerezése

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információk beszerezése. A közelgő eljárásokhoz szüksége lesz a teljesen minősített kiszolgáló- vagy állomásnévre, az adatbázis nevére és bejelentkezési adataira.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Nyissa meg az **SQL-adatbázisok** vagy az **SQL felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a **kiszolgáló neve** melletti teljesen minősített kiszolgálónevet egyetlen adatbázishoz, vagy a teljesen minősített kiszolgálónevet a felügyelt példány **gazdagépe** mellett. A kiszolgáló vagy az állomásnév másolásához mutasson rá, és válassza a **Másolás ikont.**

## <a name="create-code-to-query-your-sql-database"></a>Kód létrehozása az SQL-adatbázis lekérdezéséhez 

1. A szövegszerkesztőben hozzon létre egy *új, sqltest.py*nevű fájlt.  
   
1. Adja hozzá a következő kódot. Helyettesítse saját \<értékeit \<a \<kiszolgálói>, \<az adatbázis->, a felhasználónév> és a jelszó>.
   
   >[!IMPORTANT]
   >A példában szereplő kód az AdventureWorksLT-minta adatait használja, amelyeket az adatbázis létrehozásakor forrásként választhat. Ha az adatbázis különböző adatokat használ, használjon saját adatbázistábláit a SELECT lekérdezésben. 
   
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

1. Ellenőrizze, hogy a rendszer a 20 legfontosabb kategória/termék sort adja-e vissza, majd zárja be a parancsablakot.

## <a name="next-steps"></a>További lépések

- [Az első Azure SQL-adatbázis megtervezése](sql-database-design-first-database.md)
- [Microsoft Python-illesztőprogramok az SQL Server kiszolgálóhoz](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python fejlesztői központ](https://azure.microsoft.com/develop/python/?v=17.23h)

