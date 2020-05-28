---
title: A .NET Core használata a lekérdezéshez
description: Ebből a témakörből megtudhatja, hogyan használhatja a .NET Core-t egy olyan program létrehozásához, amely Azure SQL Database-adatbázishoz kapcsolódik, és a Transact-SQL-utasítások használatával kérdezi le azokat.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 2fdb20019b1a1ee62e6d9b6bf3f6892ee3683b5c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054268"
---
# <a name="quickstart-use-net-core-c-to-query-a-azure-sql-database"></a>Rövid útmutató: .NET Core (C#) használata Azure SQL Database-adatbázis lekérdezéséhez
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebben a rövid útmutatóban [.net Core](https://www.microsoft.com/net/) -és C#-kódot fog használni egy Azure SQL Database-adatbázishoz való kapcsolódáshoz. Ezután futtasson egy Transact-SQL-utasítást az adatok lekérdezéséhez.

> [!TIP]
> A következő Microsoft Learn modul segít megtanulni az ingyenes [ASP.NET-alkalmazások fejlesztését és konfigurálását Azure SQL Database](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)

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

- [.Net Core az operációs rendszer](https://www.microsoft.com/net/core) telepítéséhez.

> [!NOTE]
> Ez a rövid útmutató a *mySampleDatabase* -adatbázist használja. Ha másik adatbázist szeretne használni, módosítania kell az adatbázis-hivatkozásokat, és módosítania kell a `SELECT` lekérdezést a C#-kódban.

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok** vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egy Azure SQL Database vagy a teljes kiszolgálónév (vagy IP-cím) mellett egy Azure SQL felügyelt példányhoz vagy egy Azure-beli **virtuális gépen SQL Server** . A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

> [!NOTE]
> Az Azure-beli virtuális gépek SQL Server kapcsolati információit lásd: [kapcsolódás SQL Serverhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="get-adonet-connection-information-optional---sql-database-only"></a>ADO.NET-kapcsolatok adatainak beolvasása (nem kötelező – csak SQL Database)

1. Navigáljon a **mySampleDatabase** lapra, és a **Beállítások**területen válassza a **kapcsolódási karakterláncok**lehetőséget.

2. Tekintse át az **ADO.NET** teljes kapcsolati sztringjét.

    ![ADO.NET kapcsolati sztring](./media/connect-query-dotnet-core/adonet-connection-string2.png)

3. Ha használni kívánja, másolja a **ADO.net** -kapcsolódási karakterláncot.
  
## <a name="create-a-new-net-core-project"></a>Új .NET Core-projekt létrehozása

1. Nyisson meg egy parancssort, és hozzon létre egy **sqltest** nevű mappát. Navigáljon erre a mappára, és futtassa ezt a parancsot.

    ```cmd
    dotnet new console
    ```

    Ez a parancs új alkalmazás-projektfájlok létrehozását hozza létre, beleértve a kezdeti C#-programkódot (**program.cs**), egy XML-konfigurációs fájlt (**sqltest. csproj**) és a szükséges bináris fájlokat.

2. Egy szövegszerkesztőben nyissa meg a **sqltest. csproj** fájlt, és illessze be a következő XML-kódot a `<Project>` címkék közé. Ez az XML `System.Data.SqlClient` függőségként adja meg.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-azure-sql-database"></a>Kód beszúrása az Azure SQL Database-adatbázis lekérdezéséhez

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
- Ismerje meg, hogyan [csatlakozhat egy Azure SQL Database-adatbázishoz, és hogyan kérhet le lekérdezést a .NET-keretrendszer és a Visual Studio használatával](connect-query-dotnet-visual-studio.md).  
- Megtudhatja, hogyan [tervezheti meg első Azure SQL-adatbázisát a SSMS használatával](design-first-database-tutorial.md) , illetve hogyan [tervezhet Azure SQL Database-adatbázist, és hogyan csatlakozhat a C# és a ADO.net](design-first-database-csharp-tutorial.md).
- A .NET-ről a [.NET dokumentációjában](https://docs.microsoft.com/dotnet/) talál további információt.
