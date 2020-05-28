---
title: A Visual Studio használata a .NET és a C# használatával a lekérdezéshez
description: A Visual Studióval olyan C#-alkalmazást hozhat létre, amely egy Microsoft Azure SQL-adatbázishoz csatlakozik, és a Transact-SQL-utasításokkal kérdezi le azokat.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2 
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/31/2019
ms.openlocfilehash: ea274805e936c327bf9db1c5eedc7e55a32d6c5f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054262"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-azure-sql-database"></a>Rövid útmutató: a .NET és a C# használata a Visual Studióban egy Azure SQL Database-adatbázishoz való kapcsolódáshoz és lekérdezéshez
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a rövid útmutató bemutatja, hogyan használható a [.NET-keretrendszer](https://www.microsoft.com/net/) és a C# kód a Visual Studióban egy Azure SQL Database-adatbázis Transact-SQL-utasításokkal való lekérdezéséhez.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure SQL-adatbázis. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist az Azure SQL-ben:

  || SQL Database | SQL Managed Instance | Azure virtuális gépen futó SQL Server |
  |:--- |:--- |:---|:---|
  | Létrehozás| [Portál](single-database-create-quickstart.md) | [Portál](../managed-instance/instance-create-quickstart.md) | [Portál](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [parancssori felület](scripts/create-and-configure-database-cli.md) | [parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](firewall-create-server-level-portal-quickstart.md)| [Kapcsolódás virtuális gépről](../managed-instance/connect-vm-instance-configure.md)|
  |||[Kapcsolódás a webhelyről](../managed-instance/point-to-site-p2s-configure.md) | [Csatlakozás az SQL Serverhez](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Adatok betöltése|Adventure Works betöltve|[Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) | [Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) |
  |||Adventure Works visszaállítása vagy importálása a [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Adventure Works visszaállítása vagy importálása a [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > A cikkben található parancsfájlok az Adventure Works-adatbázis használatára íródnak. A felügyelt SQL-példányok esetében importálnia kell az Adventure Works-adatbázist egy példány-adatbázisba, vagy módosítania kell a jelen cikkben szereplő parancsfájlokat a Wide World importőr-adatbázis használatára.

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Közösségi, Professional vagy Enterprise kiadás.

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok** vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egy Azure SQL Database vagy a teljes kiszolgálónév (vagy IP-cím) mellett egy Azure SQL felügyelt példányhoz vagy egy Azure-beli **virtuális gépen SQL Server** . A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

> [!NOTE]
> Az Azure-beli virtuális gépek SQL Server kapcsolati információit lásd: [kapcsolódás SQL Serverhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="create-code-to-query-the-sql-database"></a>Kód létrehozása az SQL-adatbázis lekérdezéséhez

1. A Visual Studióban válassza a **fájl**  >  **új**  >  **projekt**lehetőséget. 
   
1. Az **új projekt** párbeszédpanelen válassza a **Visual C#** lehetőséget, majd válassza a **Console app (.NET-keretrendszer)** lehetőséget.
   
1. Adja meg a *sqltest* a projekt neveként, majd kattintson **az OK gombra**. Létrejön az új projekt. 
   
1. Válassza a **projekt**  >  **NuGet-csomagok kezelése**lehetőséget. 
   
1. A **NuGet csomagkezelő**lapján válassza a **Tallózás** fület, majd keresse meg és válassza a **System. Form. SqlClient**elemet.
   
1. A **System. SqlClient** lapon válassza a **telepítés**lehetőséget. 
   - Ha a rendszer kéri, kattintson az **OK** gombra a telepítés folytatásához. 
   - Ha megjelenik a **licenc elfogadására** szolgáló ablak, válassza az **Elfogadom**lehetőséget.
   
1. A telepítés befejezésekor lezárhatja a **NuGet csomagkezelő eszközt**. 
   
1. A Kódszerkesztő eszközben cserélje le a **program.cs** tartalmát a következő kódra. Cserélje le a, `<server>` , `<username>` `<password>` , és a értékeit `<database>` .
   
   >[!IMPORTANT]
   >Az ebben a példában szereplő kód a minta AdventureWorksLT-adatait használja, amelyeket az adatbázis létrehozásakor választhat forrásként. Ha az adatbázis különböző adatokkal rendelkezik, a SELECT lekérdezésben használja a saját adatbázisában lévő táblákat. 
   
   ```csharp
   using System;
   using System.Data.SqlClient;
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
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
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

1. Az alkalmazás futtatásához válassza a **hibakeresés**  >  **megkezdése**lehetőséget, vagy válassza az eszköztáron az **Indítás** lehetőséget, vagy nyomja le az **F5**billentyűt.
1. Győződjön meg arról, hogy az adatbázisból az első 20 kategória/termék sor van visszaadva, majd az alkalmazás ablakának bezárásához.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [csatlakozhat egy Azure SQL Database-adatbázishoz a .net Core használatával](connect-query-dotnet-core.md) Windows/Linux/MacOS rendszeren.  
- További információ [a .NET Core használatának első lépéseiről Windows/Linux/macOS rendszeren a parancssorral](/dotnet/core/tutorials/using-with-xplat-cli).
- További információ [az első Azure SQL-adatbázisának SSMS-sel való megtervezéséről](design-first-database-tutorial.md) és [az első Azure SQL-adatbázisának .NET-tel való megtervezéséről](design-first-database-csharp-tutorial.md).
- A .NET-ről a [.NET dokumentációjában](https://docs.microsoft.com/dotnet/) talál további információt.
- Újrapróbálkozási logika példája: [rugalmas csatlakozás az SQL-hez a ADO.NET használatával][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

