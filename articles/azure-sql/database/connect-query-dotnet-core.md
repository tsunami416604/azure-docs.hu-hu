---
title: Adatbázis összekötése és lekérdezése a .NET Core használatával
description: Ebből a témakörből megtudhatja, hogyan használhatja a .NET Core-t egy olyan program létrehozásához, amely Azure SQL Database vagy Azure SQL felügyelt példányban található adatbázishoz kapcsolódik, és a Transact-SQL-utasítások használatával kérdezi le azokat.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2, devx-track-csharp
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: c1443dcefb12a063a287e89f1c292ba39bd38dc6
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680354"
---
# <a name="quickstart-use-net-core-c-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Rövid útmutató: a .NET Core (C#) használata egy adatbázis lekérdezéséhez Azure SQL Database vagy Azure SQL felügyelt példányban
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ebben a rövid útmutatóban a [.net Core](https://www.microsoft.com/net/) és a C# kódot fogja használni az adatbázishoz való kapcsolódáshoz. Ezután futtasson egy Transact-SQL-utasítást az adatok lekérdezéséhez.

> [!TIP]
> A következő Microsoft Learn modul segítséget nyújt a [ASP.NET-alkalmazások fejlesztéséhez és konfigurálásához, amelyek egy adatbázist kérdeznek le Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Előfeltételek

Ennek a rövid útmutatónak az elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy adatbázis, amelyen futtathatja a lekérdezést. 

[!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [.Net Core az operációs rendszer](https://www.microsoft.com/net/core) telepítéséhez.

## <a name="get-server-connection-information"></a>Kiszolgáló-kapcsolatok adatainak beolvasása

A Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok lekérése. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok**  vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett, Azure SQL Database vagy a **gazdagép** melletti teljes kiszolgálónevet (vagy IP-címet) egy Azure SQL felügyelt példányhoz, vagy SQL Server az Azure virtuális gépen. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

> [!NOTE]
> SQL Server Azure-beli virtuális gépen való kapcsolódásával kapcsolatos információkért lásd: [kapcsolódás SQL Server-példányhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="get-adonet-connection-information-optional---sql-database-only"></a>ADO.NET-kapcsolatok adatainak beolvasása (nem kötelező – csak SQL Database)

1. Navigáljon a Azure Portal adatbázis paneljére, és a **Beállítások** területen válassza a **kapcsolódási karakterláncok** lehetőséget.

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

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Kód beszúrása az adatbázis lekérdezéséhez Azure SQL Database

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

                    String sql = "SELECT name, collation_name FROM sys.databases";

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

2. Ellenőrizze, hogy a rendszer visszaadja-e a sorokat.

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. Az alkalmazás ablakának bezárásához kattintson az **ENTER** gombra.

## <a name="next-steps"></a>Következő lépések

- [Bevezetés a .NET Core használatába Windows/Linux/macOS rendszeren a parancssorral](/dotnet/core/tutorials/using-with-xplat-cli).
- Ismerkedjen meg [a .NET-keretrendszer és a Visual Studio használatával a Azure SQL Database vagy az Azure SQL felügyelt példányának összekapcsolásával és lekérdezésével](connect-query-dotnet-visual-studio.md).  
- Megtudhatja, hogyan [tervezheti meg első adatbázisát a SSMS](design-first-database-tutorial.md) , vagy [megtervezheti az adatbázist, és csatlakozhat a C# és a ADO.net](design-first-database-csharp-tutorial.md)szolgáltatáshoz.
- A .NET-ről a [.NET dokumentációjában](/dotnet/) talál további információt.
