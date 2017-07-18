---
title: "A .NET Core használata Azure SQL Database-adatbázisok lekérdezéséhez | Microsoft Docs"
description: "Ez a témakör bemutatja, hogyan használhatja a .NET Core-t egy Azure SQL Database-adatbázishoz csatlakozó program létrehozásához, és hogyan hajthat végre lekérdezést Transact-SQL-utasításokkal."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/05/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 4b44064f05a24a25c3ac8157e4aa68a8a8d9856a
ms.contentlocale: hu-hu
ms.lasthandoff: 07/13/2017

---
# <a name="use-net-core-c-to-query-an-azure-sql-database"></a>A .NET Core (C#) használata Azure SQL Database-adatbázisok lekérdezéséhez

Ez a gyors üzembehelyezési oktatóanyag ismerteti, hogyan használható a [.NET Core](https://www.microsoft.com/net/) olyan C#-program létrehozásához Windows-/Linux-/macOS-gépeken, amely egy Azure SQL Database-adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.

## <a name="prerequisites"></a>Előfeltételek

A gyors üzembehelyezési útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Azure SQL Database-adatbázis. Ez a rövid útmutató az alábbi rövid útmutatók egyikében létrehozott erőforrásokat használja: 

   - [DB létrehozása – portál](sql-database-get-started-portal.md)
   - [DB létrehozása – CLI](sql-database-get-started-cli.md)
   - [DB létrehozása – PowerShell](sql-database-get-started-powershell.md)

- A gyors üzembe helyezési útmutatóhoz használt számítógép nyilvános IP-címére vonatkozó [kiszolgálószintű tűzfalszabály](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).
- Telepítette az [az operációs rendszerének megfelelő .NET Core-t](https://www.microsoft.com/net/core). 

## <a name="sql-server-connection-information"></a>Az SQL-kiszolgáló kapcsolatadatai

Kérje le az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. A későbbi eljárásokban szüksége lesz a teljes kiszolgálónévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az adatbázis **Áttekintés** oldalán tekintse meg a teljes kiszolgálónevet, amint az az alábbi képen látható. Ha a mutatót a kiszolgáló neve fölé viszi, megjelenik a **Kattintson a másoláshoz** lehetőség. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Ha elfelejtette Azure SQL Database-kiszolgálója bejelentkezési adatait, lépjen az SQL Database-kiszolgáló oldalára, és itt megtudhatja a kiszolgáló rendszergazdájának nevét. Szükség esetén új jelszót is kérhet.

5. Kattintson az: **Adatbázis-kapcsolati karakterláncok megjelenítése** elemre.

6. Tekintse át az **ADO.NET** teljes kapcsolati karakterláncát.

    ![ADO.NET kapcsolati karakterlánc](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)

> [!IMPORTANT]
> Rendelkeznie kell egy tűzfalszabállyal azon számítógép nyilvános IP-címéhez, amelyen ezt az oktatóanyagot elvégzi. Ha más számítógépet használ, vagy más nyilvános IP-címe van, hozzon létre egy [kiszolgálószintű tűzfalszabályt az Azure Portalon](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 
>
  
## <a name="create-a-new-net-project"></a>Új .NET-projekt létrehozása

1. Nyisson meg egy parancssort, és hozzon létre egy *sqltest* nevű mappát. Keresse meg a létrehozott mappát, és futtassa a következő parancsot:

    ```
    dotnet new console
    ```

2. Nyissa meg az ***sqltest.csproj*** fájlt egy tetszőleges szövegszerkesztővel, és adja hozzá a System.Data.SqlClient elemet függőségként a következő kóddal:

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.3.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Kód beszúrása SQL-adatbázis lekérdezéséhez

1. A fejlesztői környezetben vagy egy tetszőleges szövegszerkesztőben nyissa meg a **Program.cs** fájlt.

2. Cserélje le a tartalmat a következő kódra, és adja meg a kiszolgáló és az adatbázis megfelelő adatait, valamint a felhasználót és a jelszót.

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
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

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

1. Futtassa az alábbi parancsokat a parancssorban:

   ```csharp
   dotnet restore
   dotnet run
   ```

2. Győződjön meg arról, hogy a parancssori felület visszaadta az első 20 sort, majd zárja be az alkalmazásablakot.


## <a name="next-steps"></a>Következő lépések

- [Bevezetés a .NET Core használatába Windows/Linux/macOS rendszeren a parancssorral](/dotnet/core/tutorials/using-with-xplat-cli.md).
- További információ [az Azure SQL Database-adatbázisokhoz való csatlakozásról és a lekérdezésükről a .NET-keretrendszer és a Visual Studio használatával](sql-database-connect-query-dotnet-visual-studio.md).  
- További információ [az első Azure SQL Database-adatbázisának SSMS-sel való megtervezéséről](sql-database-design-first-database.md) és [az első Azure SQL Database-adatbázisának .NET-tel való megtervezéséről](sql-database-design-first-database-csharp.md).
- A .NET-ről a [.NET dokumentációjában](https://docs.microsoft.com/dotnet/) talál további információt.

