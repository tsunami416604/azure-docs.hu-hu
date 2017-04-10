---
title: "Csatlakozás az Azure SQL Database-hez .NET (C#) használatával | Microsoft Docs"
description: "Az ebben a rövid útmutatóban található mintakódot arra használhatja, hogy létrehozzon egy C# nyelven írt modern alkalmazást, amelyet egy nagy teljesítményű, felhőalapú, Azure SQL Database relációs adatbázis támogat."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: quick start
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/28/2017
ms.author: andrela;sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: c6c0c218b8d0456d37a4514238675fd8e75faf9d
ms.lasthandoff: 03/30/2017


---
# <a name="azure-sql-database-use-net-c-to-connect-and-query-data"></a>Azure SQL Database: Csatlakozás és adatlekérdezés a .NET (C#) használatával

A [C# és az ADO.NET](https://msdn.microsoft.com/library/kb9s9ks0.aspx) használatával csatlakozhat egy Azure SQL-adatbázishoz, és lekérdezéseket hajthat végre. Ez az útmutató ismerteti, hogyan használható a C# az Azure SQL-adatbázishoz való csatlakozásra, majd a lekérdezési, beszúrási, frissítési és törlési utasítások végrehajtására.

Ez a rövid útmutató az alábbi rövid útmutatók egyikében létrehozott erőforrásokat használja kiindulási pontnak:

- [DB létrehozása – portál](sql-database-get-started-portal.md)
- [DB létrehozása – CLI](sql-database-get-started-cli.md)

## <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

A következő szakaszokban a meglévő Mac OS, Linux (Ubuntu) és Windows fejlesztési környezetek Azure SQL Database-zel való használatához történő konfigurálásának részleteiről olvashat.

### <a name="mac-os"></a>**Mac OS**
Nyissa meg a terminált, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a .NET Core projektet. A **brew**, az **OpenSSL** és a **.NET Core** telepítéséhez használja a következő parancsokat. 

```C#
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

Telepítse a .NET Core-t a macOS rendszeren. Töltse le a [hivatalos telepítőt](https://go.microsoft.com/fwlink/?linkid=843444). Ez a telepítő feltelepíti az eszközöket, és az elérési útra helyezi őket, így a dotnet elindítható a konzolról

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Nyissa meg a terminált, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a .NET Core projektet. A **.NET Core** telepítéséhez használja a következő parancsokat.

```C#
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-dev-1.0.1
```

### <a name="windows"></a>**Windows**
Telepítse a Visual Studio 2015 Community Editiont és a .NET keretrendszert. Ha a Visual Studio már telepítve van a számítógépén, a következő pár lépés kihagyható.

A Visual Studio 2015 Community egy minden funkcióval ellátott, bővíthető, ingyenes IDE, amellyel korszerű alkalmazásokat hozhat létre Android, iOS és Windows operációs rendszerekre, valamint webes illetve adatbázis-alkalmazásokhoz és felhőszolgáltatásokhoz.

1. Töltse le a [telepítőt](https://go.microsoft.com/fwlink/?LinkId=691978). 
2. Futtassa a telepítőt, és kövesse a telepítési utasításokat a telepítés befejezéséhez.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Kérje le a kapcsolati karakterláncot az Azure Portalon. A kapcsolati karakterlánccal csatlakozhat az Azure SQL adatbázishoz.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az adatbázis **Alapvető erőforrások** paneljén tekintse meg a teljes kiszolgálónevet. 

    <img src="./media/sql-database-connect-query-dotnet/connection-strings.png" alt="connection strings" style="width: 780px;" />

4. Kattintson az: **Adatbázis-kapcsolati karakterláncok megjelenítése** elemre.

5. Tekintse át a teljes **ADO.NET** kapcsolati karakterláncot.

    <img src="./media/sql-database-connect-query-dotnet/adonet-connection-string.png" alt="ADO.NET connection string" style="width: 780px;" />
    
## <a name="add-systemdatasqlclient"></a>Add System.Data.SqlClient
A .NET Core használatakor rendelje hozzá a projektje ***csproj*** fájljához a System.Data.SqlClient névteret függőségként.

```xml
<ItemGroup>
    <PackageReference Include="System.Data.SqlClient" Version="4.3.0" />
</ItemGroup>
```

## <a name="select-data"></a>Adatok kiválasztása

1. A fejlesztési környezetben nyisson meg egy üres kódfájlt.
2. Adja hozzá a ```using System.Data.SqlClient``` sort a kódfájlhoz ([System.Data.SqlClient névtér](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)). 

3. Az [SqlCommand.ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) metódus és egy [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL utasítás együttes használatával lekérdezheti az adatokat az Azure SQL-adatbázisban. Adja meg a kiszolgálójának megfelelő értékeket.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
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
        }
    }
}
```

## <a name="insert-data"></a>Adat beszúrása

Az [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) metódus és egy [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL utasítás együttes használatával beszúrhatja az adatokat az Azure SQL Database-be.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
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
                    Console.WriteLine("\nInsert data example:");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("INSERT INTO [SalesLT].[Product] ([Name], [ProductNumber], [Color], [StandardCost], [ListPrice], [SellStartDate]) ");
                    sb.Append("VALUES (@Name, @ProductNumber, @Color, @StandardCost, @ListPrice, @SellStartDate);");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        command.Parameters.AddWithValue("@ProductNumber", "200989");
                        command.Parameters.AddWithValue("@Color", "Blue");
                        command.Parameters.AddWithValue("@StandardCost", 75);
                        command.Parameters.AddWithValue("@ListPrice", 80);
                        command.Parameters.AddWithValue("@SellStartDate", "7/1/2016");
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) inserted");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="update-data"></a>Adatok frissítése

Az [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) metódus és egy [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL utasítás együttes használatával frissítheti az adatokat az Azure SQL-adatbázisban.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
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
                    Console.WriteLine("\nUpdate data example");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("UPDATE [SalesLT].[Product] SET ListPrice = @ListPrice WHERE Name = @Name;");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        command.Parameters.AddWithValue("@ListPrice", 500);
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) updated");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="delete-data"></a>Adat törlése

Az [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) metódus és egy [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL utasítás együttes használatával törölheti az adatokat az Azure SQL-adatbázisban.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
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
                    Console.WriteLine("\nDelete data example");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("DELETE FROM SalesLT.Product WHERE Name = @Name;");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) deleted");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések

- A .NET dokumentációját megtekintheti itt: [.NET-dokumentáció](https://docs.microsoft.com/dotnet/).
- A Visual Studio Code-ból végzett lekérdezésről és adatszerkesztés a [Visual Studio Code-ot](https://code.visualstudio.com/docs) leíró cikkben olvashat.

