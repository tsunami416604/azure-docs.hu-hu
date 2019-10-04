---
title: A .NET Core használata az Azure SQL Database lekérdezéséhez | Microsoft Docs
description: Ebből a témakörből megtudhatja, hogyan használhatja a .NET Core-t olyan program létrehozására, amely egy Azure SQL Databasehoz csatlakozik, és a Transact-SQL-utasítások használatával kérdezi le azokat.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 1c6fdc6f6d2e642db8b57745573260c819c6c307
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640926"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>Gyors útmutató: A .NET Core (C#) használata Azure SQL-adatbázisok lekérdezéséhez

Ebben a rövid útmutatóban a [.net Core](https://www.microsoft.com/net/) és C# a Code használatával csatlakozhat egy Azure SQL Database-adatbázishoz. Ezután futtasson egy Transact-SQL-utasítást az adatok lekérdezéséhez.

> [!TIP]
> A következő Microsoft Learn modul segít megtanulni az ingyenes [ASP.NET-alkalmazások fejlesztését és konfigurálását Azure SQL Database](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a következőkre lesz szüksége:

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

- [.Net Core az operációs rendszer](https://www.microsoft.com/net/core) telepítéséhez.

> [!NOTE]
> Ez a rövid útmutató a *mySampleDatabase* -adatbázist használja. Ha másik adatbázist szeretne használni, módosítania kell az adatbázis-hivatkozásokat, és módosítania `SELECT` kell a lekérdezést a C# kódban.

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok** vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egyetlen adatbázishoz vagy a felügyelt példányhoz tartozó **gazdagép** melletti teljes kiszolgálónévhez. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

## <a name="get-adonet-connection-information-optional"></a>ADO.NET-kapcsolatok adatainak beolvasása (nem kötelező)

1. Navigáljon a **mySampleDatabase** lapra, és a **Beállítások**területen válassza a **kapcsolódási karakterláncok**lehetőséget.

2. Tekintse át az **ADO.NET** teljes kapcsolati sztringjét.

    ![ADO.NET kapcsolati sztring](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. Ha használni kívánja, másolja a **ADO.net** -kapcsolódási karakterláncot.
  
## <a name="create-a-new-net-core-project"></a>Új .NET Core-projekt létrehozása

1. Nyisson meg egy parancssort, és hozzon létre egy **sqltest** nevű mappát. Navigáljon erre a mappára, és futtassa ezt a parancsot.

    ```cmd
    dotnet new console
    ```
    Ez a parancs új alkalmazás-projektfájlok létrehozását hozza létre, C# beleértve egy kezdeti**program.cs**, egy XML-konfigurációs fájlt (**sqltest. csproj**) és a szükséges bináris fájlokat.

2. Egy szövegszerkesztőben nyissa meg a **sqltest. csproj** fájlt, és illessze be a `<Project>` következő XML-kódot a címkék közé. Ez az XML `System.Data.SqlClient` függőségként adja meg.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Kód beszúrása SQL-adatbázis lekérdezéséhez

1. Egy szövegszerkesztőben nyissa meg a **program.cs**.

2. Cserélje le a tartalmát a következő kódra, és adja hozzá a kiszolgáló, az adatbázis, a Felhasználónév és a jelszó megfelelő értékeit.

> [!NOTE]
> Ha ADO.NET-kapcsolódási karakterláncot szeretne használni, cserélje le a kód 4 sorát a kiszolgáló, az adatbázis, a Felhasználónév és a jelszó beállítására az alábbi sorral. A karakterláncban adja meg a felhasználónevét és a jelszavát.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

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

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
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
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>A kód futtatása

1. A parancssorban futtassa a következő parancsokat.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Ellenőrizze, hogy a rendszer az első 20 sort adja vissza.

   ```text
   Query data example:
   =========================================

   Road Frames HL Road Frame - Black, 58
   Road Frames HL Road Frame - Red, 58
   Helmets Sport-100 Helmet, Red
   Helmets Sport-100 Helmet, Black
   Socks Mountain Bike Socks, M
   Socks Mountain Bike Socks, L
   Helmets Sport-100 Helmet, Blue
   Caps AWC Logo Cap
   Jerseys Long-Sleeve Logo Jersey, S
   Jerseys Long-Sleeve Logo Jersey, M
   Jerseys Long-Sleeve Logo Jersey, L
   Jerseys Long-Sleeve Logo Jersey, XL
   Road Frames HL Road Frame - Red, 62
   Road Frames HL Road Frame - Red, 44
   Road Frames HL Road Frame - Red, 48
   Road Frames HL Road Frame - Red, 52
   Road Frames HL Road Frame - Red, 56
   Road Frames LL Road Frame - Black, 58
   Road Frames LL Road Frame - Black, 60
   Road Frames LL Road Frame - Black, 62

   Done. Press enter.
   ```
3. Az alkalmazás ablakának bezárásához kattintson az **ENTER** gombra.

## <a name="next-steps"></a>További lépések

- [Bevezetés a .NET Core használatába Windows/Linux/macOS rendszeren a parancssorral](/dotnet/core/tutorials/using-with-xplat-cli).
- Ismerje meg, hogyan [csatlakozhat egy Azure SQL Database-adatbázishoz, és hogyan kérhet le lekérdezést a .NET-keretrendszer és a Visual Studio használatával](sql-database-connect-query-dotnet-visual-studio.md).  
- Ismerje meg, hogyan [tervezheti meg első Azure SQL-adatbázisát az SSMS használatával](sql-database-design-first-database.md) , vagy megtervezheti [Az Azure SQL Database-t C# , és kapcsolódhat és ADO.net](sql-database-design-first-database-csharp.md).
- A .NET-ről a [.NET dokumentációjában](https://docs.microsoft.com/dotnet/) talál további információt.
