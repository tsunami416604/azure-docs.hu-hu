---
title: A Visual Studio használata a .NET és a C# használatával a lekérdezéshez
description: A Visual Studióval hozzon létre egy C#-alkalmazást, amely Azure SQL Database vagy az Azure SQL felügyelt példányában található adatbázishoz kapcsolódik, és lekérdezéseket futtat.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: a864b2b3e0379a8b0a1d67c97a63b3d5c52f9e58
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92669724"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Rövid útmutató: a .NET és a C# használata a Visual Studióban Azure SQL Database vagy Azure SQL felügyelt példányban található adatbázishoz való kapcsolódáshoz és lekérdezéshez
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ez a rövid útmutató azt ismerteti, hogyan használható a [.NET-keretrendszer](https://www.microsoft.com/net/) és a C# kód a Visual Studióban egy adatbázis lekérdezéséhez Azure SQL Database Transact-SQL-utasításokkal.

## <a name="prerequisites"></a>Előfeltételek

Ennek a rövid útmutatónak az elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy adatbázis a Azure SQL Databaseban. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist Azure SQL Databaseban:

  | Művelet | SQL Database | Felügyelt SQL-példány | Azure virtuális gépen futó SQL Server |
  |:--- |:--- |:---|:---|
  | Létrehozás| [Portál](single-database-create-quickstart.md) | [Portál](../managed-instance/instance-create-quickstart.md) | [Portál](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [Parancssori felület](scripts/create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](firewall-create-server-level-portal-quickstart.md)| [Kapcsolódás virtuális gépről](../managed-instance/connect-vm-instance-configure.md)|
  |||[Helyszíni kapcsolat](../managed-instance/point-to-site-p2s-configure.md) | [Csatlakozás az SQL Serverhez](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Adatok betöltése|Adventure Works betöltve|[Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) | [Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) |
  |||Adventure Works visszaállítása vagy importálása [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Adventure Works visszaállítása vagy importálása [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > A cikkben található parancsfájlok az Adventure Works-adatbázis használatára íródnak. A felügyelt SQL-példányok esetében importálnia kell az Adventure Works-adatbázist egy példány-adatbázisba, vagy módosítania kell a jelen cikkben szereplő parancsfájlokat a Wide World importőr-adatbázis használatára.

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Közösségi, Professional vagy Enterprise kiadás.

## <a name="get-server-connection-information"></a>Kiszolgáló-kapcsolatok adatainak beolvasása

Az adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok**  vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett Azure SQL Database vagy a **gazdagép** melletti teljes kiszolgálónevet (vagy IP-címet) egy Azure SQL felügyelt példányhoz, vagy SQL Server az Azure virtuális gépen. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

> [!NOTE]
> SQL Server Azure-beli virtuális gépen való kapcsolódásával kapcsolatos információkért lásd: [kapcsolódás SQL Server-példányhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Kód létrehozása az adatbázis lekérdezéséhez Azure SQL Database

1. Hozzon létre egy új projektet a Visual Studióban. 
   
1. Az **új projekt** párbeszédpanelen válassza a **Visual C#** , **Console app (.NET-keretrendszer)** elemet.
   
1. Adja meg a *sqltest* a projekt neveként, majd kattintson **az OK gombra** . Létrejön az új projekt. 
   
1. Válassza a **projekt**  >  **NuGet-csomagok kezelése** lehetőséget. 
   
1. A **NuGet csomagkezelő** lapján válassza a **Tallózás** lapot, majd keresse meg és válassza ki a **Microsoft. Form. SqlClient** elemet.
   
1. A **Microsoft. SqlClient** lapon válassza a **telepítés** lehetőséget. 
   - Ha a rendszer kéri, kattintson az **OK** gombra a telepítés folytatásához. 
   - Ha megjelenik a **licenc elfogadására** szolgáló ablak, válassza az **Elfogadom** lehetőséget.
   
1. A telepítés befejezésekor lezárhatja a **NuGet csomagkezelő eszközt** . 
   
1. A Kódszerkesztő eszközben cserélje le a **program.cs** tartalmát a következő kódra. Cserélje le a, `<your_server>` , `<your_username>` `<your_password>` , és a értékeit `<your_database>` .
   
   >[!IMPORTANT]
   >Az ebben a példában szereplő kód a minta AdventureWorksLT-adatait használja, amelyeket az adatbázis létrehozásakor választhat forrásként. Ha az adatbázis különböző adatokkal rendelkezik, a SELECT lekérdezésben használja a saját adatbázisában lévő táblákat. 
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>A kód futtatása

1. Az alkalmazás futtatásához válassza a **hibakeresés**  >  **megkezdése** lehetőséget, vagy válassza az eszköztáron az **Indítás** lehetőséget, vagy nyomja le az **F5** billentyűt.
1. Győződjön meg arról, hogy az adatbázisból az első 20 kategória/termék sor van visszaadva, majd az alkalmazás ablakának bezárásához.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [csatlakozhat egy adatbázishoz a Azure SQL Database a .net Core használatával](connect-query-dotnet-core.md) a Windows/Linux/MacOS rendszeren.  
- További információ [a .NET Core használatának első lépéseiről Windows/Linux/macOS rendszeren a parancssorral](/dotnet/core/tutorials/using-with-xplat-cli).
- Megtudhatja, hogyan [tervezheti meg az első adatbázisát Azure SQL Database a SSMS használatával](design-first-database-tutorial.md) , vagy [az első adatbázis megtervezésével Azure SQL Database a .NET használatával](design-first-database-csharp-tutorial.md).
- A .NET-ről a [.NET dokumentációjában](/dotnet/) talál további információt.
- Próbálja ki újra a logikai példát: [kapcsolódjon rugalmasan az Azure SQL-hez a ADO.NET használatával][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net