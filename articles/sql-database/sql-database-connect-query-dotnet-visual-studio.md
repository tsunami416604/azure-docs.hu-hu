---
title: Lekérdezés a Visual Studio használatával .NET és C#
description: A Visual Studio segítségével hozzon létre egy C# alkalmazást, amely csatlakozik egy Azure SQL-adatbázishoz, és lekérdezi azt a Transact-SQL utasításokkal.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/31/2019
ms.openlocfilehash: 75f84f87fc59a870a0c88fd24cb1b1b8894b3e2a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79209575"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Rövid útmutató: Azure SQL-adatbázishoz való csatlakozáshoz és lekérdezéshez használja a .NET és a C# alkalmazást a Visual Studióban

Ez a rövid útmutató bemutatja, hogyan használhatja a [.](https://www.microsoft.com/net/)

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Azure SQL-adatbázis. Az alábbi rövid útmutatók egyikével hozhat létre, majd konfigurálhat egy adatbázist az Azure SQL Database-ben:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Létrehozás| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Powershell](scripts/sql-database-create-and-configure-database-powershell.md) | [Powershell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurálás | [Kiszolgálószintű IP-tűzfal szabály](sql-database-server-level-firewall-rule.md)| [Kapcsolódás virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Helyszíni kapcsolat](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|A kalandworks betöltve egy rövid útmutató|[Széles világbeli importőrök visszaállítása](sql-database-managed-instance-get-started-restore.md)
  |||Kalandorok visszaállítása vagy importálása [a BACPAC-fájlból](sql-database-import.md) a [GitHubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > A cikkben szereplő parancsfájlok a Kalandorbolt-adatbázis használatára íródnak. Felügyelt példány esetén vagy importálnia kell a Kalandorbolt-adatbázist egy példányadatbázisba, vagy módosítania kell a cikkparancsfájljait a Wide World Importers adatbázis használatához.

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) közösségi, professzionális vagy enterprise kiadás.

## <a name="get-sql-server-connection-information"></a>SQL-kiszolgálókapcsolati adatok beszerezése

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információk beszerezése. A közelgő eljárásokhoz szüksége lesz a teljesen minősített kiszolgáló- vagy állomásnévre, az adatbázis nevére és bejelentkezési adataira.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Keresse meg az **SQL-adatbázisok** vagy az **SQL felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a **kiszolgáló neve** melletti teljesen minősített kiszolgálónevet egyetlen adatbázishoz, vagy a teljesen minősített kiszolgálónevet a felügyelt példány **gazdagépe** mellett. A kiszolgáló vagy az állomásnév másolásához mutasson rá, és válassza a **Másolás ikont.** 

## <a name="create-code-to-query-the-sql-database"></a>Az SQL-adatbázis lekérdezéséhez kód létrehozása

1. A Visual Studióban válassza az**Új** > **projekt** **fájlja** > lehetőséget. 
   
1. Az **Új projekt** párbeszédpanelen válassza a **Visual C#** lehetőséget, majd a **Console App (.NET Framework)** lehetőséget.
   
1. Írja be a projekt nevének *sqltest* tesztjét, majd kattintson az **OK gombra.** Az új projekt létrejön. 
   
1. Válassza **a Project** > **Manage NuGet csomagok lehetőséget.** 
   
1. A **NuGet csomagkezelőben**válassza a **Tallózás** lapot, majd keresse meg a **System.Data.SqlClient elemet.**
   
1. A **System.Data.SqlClient** lapon válassza a **Telepítés**lehetőséget. 
   - Ha a rendszer kéri, a telepítés folytatásához válassza az **OK gombot.** 
   - Ha megjelenik egy **Licencelfogadási** ablak, válassza az **Elfogadom**lehetőséget.
   
1. Amikor a telepítés befejeződött, bezárhatja a **NuGet csomagkezelőt.** 
   
1. A kódszerkesztőben cserélje le a **Program.cs** tartalmát a következő kódra. Cserélje le `<server>`az `<username>` `<password>`értékeket `<database>`a , , és a parancsra.
   
   >[!IMPORTANT]
   >A példában szereplő kód az AdventureWorksLT-minta adatait használja, amelyeket az adatbázis létrehozásakor forrásként választhat. Ha az adatbázis különböző adatokat használ, használjon saját adatbázistábláit a SELECT lekérdezésben. 
   
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

1. Az alkalmazás futtatásához válassza a **Hibakeresés** > **indítása hibakeresés**lehetőséget, vagy válassza a **Start** gombot az eszköztáron, vagy nyomja le az **F5 billentyűt.**
1. Ellenőrizze, hogy az adatbázis első 20 kategória-/terméksora jelenik-e meg, majd zárja be az alkalmazásablakot.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan csatlakozhat és kérdezheti le [az Azure SQL-adatbázist](sql-database-connect-query-dotnet-core.md) a .NET Core használatával Windows/Linux/macOS rendszeren.  
- További információ [a .NET Core használatának első lépéseiről Windows/Linux/macOS rendszeren a parancssorral](/dotnet/core/tutorials/using-with-xplat-cli).
- További információ [az első Azure SQL-adatbázisának SSMS-sel való megtervezéséről](sql-database-design-first-database.md) és [az első Azure SQL-adatbázisának .NET-tel való megtervezéséről](sql-database-design-first-database-csharp.md).
- A .NET-ről a [.NET dokumentációjában](https://docs.microsoft.com/dotnet/) talál további információt.
- Újrapróbálkozási logikai példa: [Rugalmasan kapcsolódjon az SQL-hez a ADO.NET.][step-4-connect-resiliently-to-sql-with-ado-net-a78n]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

