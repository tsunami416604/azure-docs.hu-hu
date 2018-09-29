---
title: Visual Studio és .NET használata Azure SQL Database-adatbázis lekérdezéséhez | Microsoft Docs
description: Ez a témakör bemutatja, hogyan használhatja a Visual Studiót egy Azure SQL Database-adatbázishoz csatlakozó program létrehozásához, és hogyan hajthat végre lekérdezést Transact-SQL-utasításokkal.
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
ms.date: 04/01/2018
ms.openlocfilehash: e6eaaa67914579edd5c3b53362835aa1ad168eca
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064033"
---
# <a name="use-net-c-with-visual-studio-to-connect-and-query-an-azure-sql-database"></a>A .NET (C#) használata a Visual Studióval Azure SQL Database-adatbázishoz való kapcsolódáshoz és lekérdezéséhez

Ez a rövid útmutató azt ismerteti, hogyan használható a [.NET-keretrendszer](https://www.microsoft.com/net/) olyan C#-program a Visual Studióval való létrehozásához, amely egy Azure SQL Database-adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- A rövid útmutatóhoz használt számítógép nyilvános IP-címére vonatkozó [kiszolgálószintű tűzfalszabály](sql-database-get-started-portal-firewall.md).

- A [Visual Studio Community 2017, a Visual Studio Professional 2017 vagy a Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/) telepítése.

## <a name="sql-server-connection-information"></a>Az SQL-kiszolgáló kapcsolatadatai

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

#### <a name="for-adonet"></a>ADO.NET esetén

1. A folytatáshoz kattintson az **Adatbázis-kapcsolati sztringek megjelenítése** elemre.

2. Tekintse át az **ADO.NET** teljes kapcsolati sztringjét.

    ![ADO.NET kapcsolati sztring](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)

> [!IMPORTANT]
> Rendelkeznie kell egy tűzfalszabállyal azon számítógép nyilvános IP-címéhez, amelyen ezt az oktatóanyagot elvégzi. Ha más számítógépet használ, vagy más nyilvános IP-címe van, hozzon létre egy [kiszolgálószintű tűzfalszabályt az Azure Portalon](sql-database-get-started-portal-firewall.md). 
>
  
## <a name="create-a-new-visual-studio-project"></a>Új Visual Studio-projekt létrehozása

1. A Visual Studióban válassza a **Fájl**, **Új**, **Projekt** lehetőséget. 
2. Az **Új projekt** párbeszédpanelen bontsa ki a **Visual C#** elemet.
3. Válassza a **Konzolalkalmazás** elemet, és írja be az *sqltest* projektnevet.
4. Kattintson az **OK** gombra az új projekt létrehozásához és megnyitásához a Visual Studióban
4. A Megoldáskezelőben kattintson a jobb gombbal az **sqltest** elemre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra. 
5. A **Tallózás** fülön keresse meg a(z) ```System.Data.SqlClient``` elemet, majd válassza ki.
6. A **System.Data.SqlClient** oldalon kattintson a **Telepítés** elemre.
7. A telepítés befejezése után tekintse át a módosításokat, majd kattintson az **OK** gombra az **Előnézet** ablak bezárásához. 
8. Ha megjelenik egy **License Acceptance** (Licenc elfogadása) ablak, kattintson az **I Accept** (Elfogadom) gombra.

## <a name="insert-code-to-query-sql-database"></a>Kód beszúrása SQL-adatbázis lekérdezéséhez
1. Váltson a **Program.cs** fájlra (vagy szükség esetén nyissa meg)

2. Cserélje le a **Program.cs** tartalmát a következő kódra, és adja meg a kiszolgáló és az adatbázis megfelelő adatait, valamint a felhasználót és a jelszót.

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

1. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
2. Győződjön meg arról, hogy a parancssori felület visszaadta az első 20 sort, majd zárja be az alkalmazásablakot.

## <a name="next-steps"></a>További lépések

- További információ [az Azure SQL Database-adatbázisokhoz való csatlakozásról és a .NET Core-ral való lekérdezésükről](sql-database-connect-query-dotnet-core.md) Windows/Linux/macOS rendszeren.  
- További információ [a .NET Core használatának első lépéseiről Windows/Linux/macOS rendszeren a parancssorral](/dotnet/core/tutorials/using-with-xplat-cli).
- További információ [az első Azure SQL Database-adatbázisának SSMS-sel való megtervezéséről](sql-database-design-first-database.md) és [az első Azure SQL Database-adatbázisának .NET-tel való megtervezéséről](sql-database-design-first-database-csharp.md).
- A .NET-ről a [.NET dokumentációjában](https://docs.microsoft.com/dotnet/) talál további információt.
- [Példa újrapróbálkozási logikára: Rugalmas csatlakozás az SQL-hez az ADO.NET használatával][step-4-connect-resiliently-to-sql-with-ado-net-a78n]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

