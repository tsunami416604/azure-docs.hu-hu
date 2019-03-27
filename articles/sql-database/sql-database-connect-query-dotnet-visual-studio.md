---
title: A Visual Studio használata a .NET-tel és C# Azure SQL Database-adatbázis lekérdezéséhez |} A Microsoft Docs
description: A Visual Studio segítségével készítsen egy C# alkalmazást, amely az Azure SQL Database csatlakozik és kérdezi le azt a Transact-SQL-utasításokkal.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 7c444235ed7aa641fe218b4d1667e661ff05d77e
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443665"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Gyors útmutató: A .NET használata és C# csatlakozhat, és a egy Azure SQL database lekérdezése a Visual studióban

Ez a rövid útmutató bemutatja, hogyan használhatja a [.NET-keretrendszer](https://www.microsoft.com/net/) és C# lekérdezni egy Azure SQL database Transact-SQL-utasítások a Visual Studio code.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Azure SQL Database-adatbázis. Az alábbi rövid útmutatókban hozhat létre, és válassza az Azure SQL Database egy adatbázis is használja:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Létrehozás| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurálás | [kiszolgálószintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md)| [Kapcsolat egy virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Helyszíni kapcsolat](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|Az Adventure Works betöltött száma a rövid útmutató|[Állítsa vissza a Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Állítsa vissza vagy importálása az Adventure Works [BACPAC](sql-database-import.md) fájlt [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Ebben a cikkben a parancsfájlok az Adventure Works adatbázisa használatához készültek. Felügyelt példánnyal Ha az Adventure Works adatbázisa importálása-példány adatbázis, vagy módosítsa a parancsfájlokat ebben a cikkben a Wide World Importers-adatbázis használatára.

- [A Visual Studio 2017](https://www.visualstudio.com/downloads/) közösségi, Professional vagy Enterprise edition.

## <a name="get-sql-server-connection-information"></a>Az SQL server-kapcsolati adatok lekéréséhez

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információkat kaphat. A következő eljárások szüksége a kiszolgáló teljes nevét vagy a gazdagépnév, az adatbázis neve és a bejelentkezési adatait.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Keresse meg a **SQL-adatbázisok** vagy **SQL felügyelt példányai** lapot.

3. A a **áttekintése** lapon, tekintse át a teljes kiszolgálónevet melletti **kiszolgálónév** egy önálló adatbázis vagy a kiszolgáló teljes neve melletti **gazdagép** számára egy felügyelt a példány. Másolja ki a kiszolgáló nevét vagy az állomásnevet, rámutatnak, és válassza a **másolási** ikonra. 

## <a name="create-code-to-query-the-sql-database"></a>Az SQL Database-adatbázis lekérdezéséhez kód létrehozása

1. A Visual Studióban válassza ki a **fájl** > **új** > **projekt**. 
   
1. Az a **új projekt** párbeszédablakban válassza **Visual C#** , majd válassza ki **Console App (.NET Framework)**.
   
1. Adja meg *sqltest* a projekt nevét, és válassza ki a **OK**. Az új projekt jön létre. 
   
1. Válassza ki **projekt** > **NuGet-csomagok kezelése**. 
   
1. A **NuGet-Csomagkezelő**, jelölje be a **Tallózás** fülre, majd keresse meg és válassza ki **System.Data.SqlClient**.
   
1. Az a **System.Data.SqlClient** lapon jelölje be **telepítése**. 
   - Ha a rendszer kéri, válassza ki a **OK** a telepítés folytatásához. 
   - Ha egy **licencfeltételek elfogadását** ablak megjelenik, válassza ki **elfogadom**.
   
1. Ha a telepítés befejeződött, bezárhatja **NuGet-Csomagkezelő**. 
   
1. A code szerkesztőben cserélje le a **Program.cs** tartalmát az alábbira. Helyettesítse be a saját értékeit `<server>`, `<username>`, `<password>`, és `<database>`.
   
   >[!IMPORTANT]
   >Ebben a példában a kódot az AdventureWorksLT mintaadatokat, amely forrásként is választja, az adatbázis létrehozásakor használ. Ha az adatbázis különböző adatokat, használja a saját adatbázis tábláinak a SELECT-lekérdezésben. 
   
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
                       
                       connection.Open();       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
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

1. Futtassa az alkalmazást, válassza **hibakeresése** > **hibakeresés indítása**, vagy válasszon ki **Start** az eszköztáron, vagy nyomja meg **F5**.
1. Győződjön meg arról, hogy az első 20 kategória/Product az adatbázis azon sorait adja vissza, és zárja be az alkalmazás ablakának.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [kapcsolódás és lekérdezés a .NET Core használata Azure SQL database](sql-database-connect-query-dotnet-core.md) Windows/Linux/MacOS rendszeren.  
- További információ [a .NET Core használatának első lépéseiről Windows/Linux/macOS rendszeren a parancssorral](/dotnet/core/tutorials/using-with-xplat-cli).
- További információ [az első Azure SQL Database-adatbázisának SSMS-sel való megtervezéséről](sql-database-design-first-database.md) és [az első Azure SQL Database-adatbázisának .NET-tel való megtervezéséről](sql-database-design-first-database-csharp.md).
- A .NET-ről a [.NET dokumentációjában](https://docs.microsoft.com/dotnet/) talál további információt.
- Példa újrapróbálkozási: [Logikára kapcsolódni az SQL és ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

