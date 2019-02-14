---
title: A .NET Core használata Azure SQL Database-adatbázisok lekérdezéséhez | Microsoft Docs
description: Ez a témakör bemutatja, hogyan használható a .NET Core egy olyan program létrehozásához, amely kapcsolódik az Azure SQL Database, és lekérdezi a Transact-SQL-utasításokkal.
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
ms.date: 02/12/2019
ms.openlocfilehash: 82b412d7fc9e54ca213fecde783a5e27f8ee93bc
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233558"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>Gyors útmutató: A .NET Core (C#) használata Azure SQL Database-adatbázisok lekérdezéséhez

Ez a rövid útmutatóban használni kívánt [.NET Core](https://www.microsoft.com/net/) és C# code egy Azure SQL Database-adatbázishoz csatlakozhat. Majd futtassa a Transact-SQL-utasítás használatával adatokat lekérdezni.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban szüksége lesz:

- Azure SQL Database-adatbázis. Az alábbi rövid útmutatókban hozhat létre, és válassza az Azure SQL Database egy adatbázis is használja:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Létrehozás| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) |
  | Konfigurálás | [kiszolgálószintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md)| [Kapcsolat egy virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Helyszíni kapcsolat](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|Az Adventure Works betöltött száma a rövid útmutató|[Állítsa vissza a Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Állítsa vissza vagy importálása az Adventure Works [BACPAC](sql-database-import.md) fájlt [github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Ebben a cikkben a parancsfájlok az Adventure Works adatbázisa használatához készültek. Felügyelt példánnyal Ha az Adventure Works adatbázisa importálása-példány adatbázis, vagy módosítsa a parancsfájlokat ebben a cikkben a Wide World Importers-adatbázis használatára.

- [.NET core, az operációs rendszerének](https://www.microsoft.com/net/core) telepítve.

> [!NOTE]
> Ez a rövid útmutató használja a *mySampleDatabase* adatbázis. Ha egy másik adatbázishoz használni kívánt, kell módosítani az adatbázis-hivatkozásokat, és módosítsa a `SELECT` -lekérdezést a C# kódot.

## <a name="get-sql-server-connection-information"></a>Az SQL server-kapcsolati adatok lekéréséhez

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információkat kaphat. A következő eljárások szüksége a kiszolgáló teljes nevét vagy a gazdagépnév, az adatbázis neve és a bejelentkezési adatait.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Keresse meg a **SQL-adatbázisok** vagy **SQL felügyelt példányai** lapot.

3. A a **áttekintése** lapon, tekintse át a teljes kiszolgálónevet melletti **kiszolgálónév** egy önálló adatbázis vagy a kiszolgáló teljes neve melletti **gazdagép** számára egy felügyelt a példány. Másolja ki a kiszolgáló nevét vagy az állomásnevet, rámutatnak, és válassza a **másolási** ikonra.

## <a name="get-adonet-connection-information-optional"></a>Az ADO.NET kapcsolati adatok lekéréséhez (nem kötelező)

1. Keresse meg a **mySampleDatabase** oldal és a **beállítások**válassza **kapcsolati karakterláncok**.

2. Tekintse át az **ADO.NET** teljes kapcsolati sztringjét.

    ![ADO.NET kapcsolati sztring](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. Másolás a **ADO.NET** szeretne használni, ha a kapcsolati karakterláncot.
  
## <a name="create-a-new-net-core-project"></a>Hozzon létre egy új .NET Core-projektet

1. Nyisson meg egy parancssort, és hozzon létre egy **sqltest** nevű mappát. Keresse meg azt a mappát, majd futtassa a parancsot.

    ```cmd
    dotnet new console
    ```
    Ezzel a paranccsal hoz létre új alkalmazás soubory projektu, beleértve egy kezdeti C# kódfájl (**Program.cs**), egy konfigurációs XML-fájl (**sqltest.csproj**), és a szükséges bináris fájlokat.

2. Egy szövegszerkesztőben nyissa meg a **sqltest.csproj** , és illessze be a következő XML formátumú között a `<Project>` címkék. Az XML-kód hozzáadja `System.Data.SqlClient` függőségként.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Kód beszúrása SQL-adatbázis lekérdezéséhez

1. Egy szövegszerkesztőben nyissa meg a **Program.cs**.

2. Cserélje ki annak tartalmát a következő kódot, és adja meg a kiszolgáló, adatbázis, felhasználónevet és jelszót a megfelelő értékeket.

> [!NOTE]
> Az ADO.NET kapcsolati karakterlánc használatához cserélje le a beállítás a kiszolgáló, adatbázis, felhasználónév és jelszó a sor az alábbi kódot a 4 sorokat. A karakterlánc állítsa be a felhasználónevét és jelszavát.
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

2. Győződjön meg arról, hogy az első 20 sort ad vissza.

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
3. Válasszon **Enter** gombra kattintva zárja be az alkalmazásablakot.

## <a name="next-steps"></a>További lépések

- [Bevezetés a .NET Core használatába Windows/Linux/macOS rendszeren a parancssorral](/dotnet/core/tutorials/using-with-xplat-cli).
- Ismerje meg, hogyan [csatlakozhat, és a .NET-keretrendszer és a Visual Studio használatával Azure SQL Database-adatbázis lekérdezéséhez](sql-database-connect-query-dotnet-visual-studio.md).  
- Ismerje meg, hogyan [az első Azure SQL adatbázis megtervezése SSMS használatával](sql-database-design-first-database.md) vagy [ Azure SQL database tervezése és csatlakozás az C# és az ADO.NET](sql-database-design-first-database-csharp.md).
- A .NET-ről a [.NET dokumentációjában](https://docs.microsoft.com/dotnet/) talál további információt.
