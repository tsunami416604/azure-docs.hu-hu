---
title: A .NET Core használata lekérdezéshez
description: Ez a témakör bemutatja, hogyan hozhat létre a .NET Core használatával egy olyan programot, amely egy Azure SQL-adatbázishoz csatlakozik, és transact-SQL utasítások használatával kérdezi le.
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
ms.openlocfilehash: 369c708fd3181076c6deb9d7ac9134c57a18f819
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73827094"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>Rövid útmutató: A .NET Core (C#) használata Azure SQL-adatbázisok lekérdezéséhez

Ebben a rövid útmutatóban a [.NET Core](https://www.microsoft.com/net/) és a C# kódot fogja használni egy Azure SQL-adatbázishoz való csatlakozáshoz. Ezután egy Transact-SQL utasítást fog futtatni az adatok lekérdezéséhez.

> [!TIP]
> A következő Microsoft Learn modul segítségével ingyenesen [megtudhatja, hogyan fejleszthet és konfigurálhat egy ASP.NET alkalmazást, amely lekérdezi az Azure SQL-adatbázist](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következőkre van szükség:

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

- [.NET Core az operációs rendszerhez](https://www.microsoft.com/net/core) telepítve.

> [!NOTE]
> Ez a rövid útmutató a *mySampleDatabase* adatbázist használja. Ha másik adatbázist szeretne használni, módosítania kell az adatbázis-hivatkozásokat, és módosítania kell a `SELECT` lekérdezést a C# kódban.

## <a name="get-sql-server-connection-information"></a>SQL-kiszolgálókapcsolati adatok beszerezése

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információk beszerezése. A közelgő eljárásokhoz szüksége lesz a teljesen minősített kiszolgáló- vagy állomásnévre, az adatbázis nevére és bejelentkezési adataira.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Keresse meg az **SQL-adatbázisok** vagy az **SQL felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a **kiszolgáló neve** melletti teljesen minősített kiszolgálónevet egyetlen adatbázishoz, vagy a teljesen minősített kiszolgálónevet a felügyelt példány **gazdagépe** mellett. A kiszolgáló vagy az állomásnév másolásához mutasson rá, és válassza a **Másolás ikont.**

## <a name="get-adonet-connection-information-optional"></a>A kapcsolat adatok ADO.NET beszerezni (nem kötelező)

1. Nyissa meg a **MySampleDatabase** lapot, és a **Beállítások**csoportban válassza a **Kapcsolati karakterláncok**lehetőséget .

2. Tekintse át az **ADO.NET** teljes kapcsolati sztringjét.

    ![ADO.NET kapcsolati sztring](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. Másolja a **ADO.NET** kapcsolati karakterláncot, ha használni kívánja.
  
## <a name="create-a-new-net-core-project"></a>Új .NET Core projekt létrehozása

1. Nyisson meg egy parancssort, és hozzon létre egy **sqltest** nevű mappát. Nyissa meg ezt a mappát, és futtassa ezt a parancsot.

    ```cmd
    dotnet new console
    ```
    Ez a parancs új alkalmazásprojekt-fájlokat hoz létre, köztük egy kezdeti C# kódfájlt (**Program.cs),** egy XML konfigurációs fájlt **(sqltest.csproj)** és a szükséges bináris fájlokat.

2. A szövegszerkesztőben nyissa meg **az sqltest.csproj fájlt,** és illessze be a következő XML-t a `<Project>` címkék közé. Ez az `System.Data.SqlClient` XML függőségként adódik hozzá.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Kód beszúrása SQL-adatbázis lekérdezéséhez

1. A szövegszerkesztőben nyissa meg **a Program.cs.**

2. Cserélje le a tartalmat a következő kódra, és adja meg a kiszolgálónak, az adatbázisnak, a felhasználónévnek és a jelszónak megfelelő értékeket.

> [!NOTE]
> Ha ADO.NET kapcsolati karakterláncot szeretne használni, cserélje le a kiszolgálót, az adatbázist, a felhasználónevet és a jelszót az alábbi sorra a kódbeállítás 4 sorára. A karakterláncban adja meg a felhasználónevét és a jelszavát.
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

1. A parancssornál futtassa a következő parancsokat.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Ellenőrizze, hogy a rendszer a felső 20 sort adja-e vissza.

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
3. Az **Alkalmazásablak** bezárásához válassza az Enter gombot.

## <a name="next-steps"></a>További lépések

- [Bevezetés a .NET Core használatába Windows/Linux/macOS rendszeren a parancssorral](/dotnet/core/tutorials/using-with-xplat-cli).
- Megtudhatja, hogy [miként csatlakozhat és kérdezheti le az Azure SQL-adatbázist a .](sql-database-connect-query-dotnet-visual-studio.md)  
- Ismerje meg, hogyan [tervezheti meg az első Azure SQL-adatbázisát SSMS](sql-database-design-first-database.md) vagy [Design an Azure SQL-adatbázis használatával, és hogyan csatlakozhat a C# és ADO.NET.](sql-database-design-first-database-csharp.md)
- A .NET-ről a [.NET dokumentációjában](https://docs.microsoft.com/dotnet/) talál további információt.
