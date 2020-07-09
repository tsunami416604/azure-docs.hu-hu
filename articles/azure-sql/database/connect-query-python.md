---
title: A Python használata adatbázis lekérdezéséhez
description: Ez a témakör bemutatja, hogyan használható a Python egy olyan program létrehozásához, amely Azure SQL Database-adatbázishoz kapcsolódik, és hogyan kérdezheti le azt a Transact-SQL-utasítások használatával.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, tracking-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: bcd5a17cce9afea3325f90cb6fbc89887ada55e5
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84554537"
---
# <a name="quickstart-use-python-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Gyors útmutató: Azure SQL Database vagy Azure SQL felügyelt példányban lévő adatbázis lekérdezése a Python használatával
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ebben a rövid útmutatóban a Python használatával csatlakozhat Azure SQL Database vagy az Azure SQL felügyelt példányához, és a T-SQL-utasítások használatával adatokat kérdez le.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  || SQL Database | Felügyelt SQL-példány | Azure virtuális gépen futó SQL Server |
  |:--- |:--- |:---|:---|
  | Létrehozás| [Portál](single-database-create-quickstart.md) | [Portál](../managed-instance/instance-create-quickstart.md) | [Portál](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [Parancssori felület](scripts/create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](firewall-create-server-level-portal-quickstart.md)| [Kapcsolódás virtuális gépről](../managed-instance/connect-vm-instance-configure.md)|
  |||[Helyszíni kapcsolat](../managed-instance/point-to-site-p2s-configure.md) | [Kapcsolódás SQL Server-példányhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Adatok betöltése|Adventure Works betöltve|[Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) | [Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) |
  |||Adventure Works visszaállítása vagy importálása [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Adventure Works visszaállítása vagy importálása [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

- [Python](https://python.org/downloads) 3 és kapcsolódó szoftverek

  # <a name="macos"></a>[macOS](#tab/macos)

  A Homebrew és a Python, az ODBC-illesztő és a SQLCMD, valamint a SQL Server Python-illesztőprogramjának telepítéséhez használja a **1,2**, **1,3**és **2,1** lépéseket a [python-alkalmazások létrehozása a MacOS rendszeren SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  További információ: [Microsoft ODBC-illesztőprogram MacOS rendszeren](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  A Python és az egyéb szükséges csomagok telepítéséhez használja a következőt: `sudo apt-get install python python-pip gcc g++ build-essential` .

  Az ODBC-illesztő, a SQLCMD és a SQL Server Python-illesztőprogram telepítéséhez lásd: [környezet konfigurálása Pyodbc Python-fejlesztéshez](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  További információ: [Microsoft ODBC-illesztőprogram Linux rendszeren](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windows"></a>[Windows](#tab/windows)

  A Python, az ODBC-illesztő és a SQLCMD, valamint a SQL Server Python-illesztőprogramjának telepítéséhez tekintse meg a [környezet konfigurálása a Pyodbc Python-fejlesztéshez](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)című témakört.

  További információ: [Microsoft ODBC-illesztő](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---

> [!IMPORTANT]
> A cikkben található parancsfájlok az **Adventure Works** -adatbázis használatára íródnak.

> [!NOTE]
> Dönthet úgy is, hogy egy Azure SQL felügyelt példányt használ.
>
> A létrehozásához és konfigurálásához használja a [Azure Portal](../managed-instance/instance-create-quickstart.md), a [PowerShellt](../managed-instance/scripts/create-configure-managed-instance-powershell.md)vagy a [parancssori](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)felületet, majd állítsa be a helyszíni vagy [a](../managed-instance/point-to-site-p2s-configure.md) [virtuális gép](../managed-instance/connect-vm-instance-configure.md) kapcsolatát.
>
> Az betöltéssel kapcsolatban lásd: [visszaállítás a BACPAC](database-import.md) az [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) -fájllal, vagy lásd: [a Wide World](../managed-instance/restore-sample-database-quickstart.md)importing-adatbázis visszaállítása.

A Python és az adatbázis Azure SQL Databaseban való megismeréséhez lásd: [Azure SQL Database kódtárak a Pythonhoz](/python/api/overview/azure/sql), a [pyodbc adattárhoz](https://github.com/mkleehammer/pyodbc/wiki/)és egy [pyodbc-mintához](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-server-connection-information"></a>Kiszolgáló-kapcsolatok adatainak beolvasása

A Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok lekérése. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Nyissa meg az **SQL-adatbázisok** vagy az SQL- **felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett, Azure SQL Database vagy a **gazdagép** melletti teljes kiszolgálónevet (vagy IP-címet) egy Azure SQL felügyelt példányhoz, vagy SQL Server az Azure virtuális gépen. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

> [!NOTE]
> SQL Server Azure-beli virtuális gépen való kapcsolódásával kapcsolatos információkért lásd: [kapcsolódás SQL Server-példányhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-your-database"></a>Kód létrehozása az adatbázis lekérdezéséhez 

1. Egy szövegszerkesztőben hozzon létre egy új, *sqltest.py*nevű fájlt.  
   
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

1. Győződjön meg arról, hogy a 20 legfontosabb kategória/termék sorait adja vissza, majd zárjuk be a parancssorablakot.

## <a name="next-steps"></a>Következő lépések

- [Az első adatbázis megtervezése Azure SQL Database](design-first-database-tutorial.md)
- [Microsoft Python-illesztőprogramok a SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python fejlesztői központ](https://azure.microsoft.com/develop/python/?v=17.23h)

