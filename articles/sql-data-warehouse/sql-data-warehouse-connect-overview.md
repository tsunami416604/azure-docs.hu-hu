---
title: Csatlakozás az Azure SQL Data Warehouse-hoz | Microsoft Docs
description: Csatlakozhat az Azure SQL Data warehouse-bA.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 85bce59563cf0424816661c97406060bcbb423ec
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460447"
---
# <a name="connect-to-azure-sql-data-warehouse"></a>Csatlakozás az Azure SQL Data Warehouse-hoz
Csatlakozhat az Azure SQL Data warehouse-bA.

## <a name="find-your-server-name"></a>A kiszolgálónév lekérdezése
A kiszolgáló nevét az alábbi példában samplesvr.Database.Windows.NET. A teljes kiszolgálónév lekérdezése:

1. Nyissa meg az [Azure Portalt][Azure portal].
2. Kattintson az **SQL-adattárházak** elemre.
3. Kattintson arra az adattárházra, amelyhez csatlakozni kíván.
4. Keresse meg a teljes kiszolgálónevet.
   
    ![Teljes kiszolgálónév][1]

## <a name="supported-drivers-and-connection-strings"></a>Támogatott illesztők és kapcsolati sztringek
Az Azure SQL Data Warehouse a következő illesztőprogramokat támogatja: [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP] és [JDBC][JDBC]. A legújabb verziók és dokumentációk megkereséséhez kattintson az egyik előző illesztőre. Automatikusan létrehozni az Ön által használt illesztőprogram kapcsolati karakterláncát az Azure Portalon, kattintson a a **adatbázis kapcsolati karakterláncainak megjelenítése** az előző példából. A következő néhány példa bemutatja, hogy néz ki a kapcsolati sztring az egyes illesztők esetében.

> [!NOTE]
> Javasoljuk, hogy a kapcsolat időkorlátjának 300 másodpercet adjon meg, hogy a kapcsolat rövid idejű kimaradások esetén is fennmaradjon.
> 
> 

### <a name="adonet-connection-string-example"></a>Minta ADO.NET kapcsolati sztring
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Minta ODBC kapcsolati sztring
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Minta PHP kapcsolati sztring
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Minta JDBC kapcsolati sztring
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Kapcsolati beállítások
Az SQL Data Warehouse szabványosít néhány beállítást a csatlakozás és az objektumlétrehozás során. Ezeket a beállításokat nem lehet felülírni, és a következők lehetnek:

| Adatbázis-beállítások | Érték |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |ON |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |ON |
| [DATEFORMAT][DATEFORMAT] |hné |
| [DATEFIRST][DATEFIRST] |7 |

## <a name="next-steps"></a>További lépések
A Visual Studióval végzett csatlakozásról és lekérdezésről lásd: [Lekérdezés a Visual Studióval][Query with Visual Studio]. A hitelesítési lehetőségekkel kapcsolatos további információkért lásd: [Authentication to Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] (Hitelesítés az Azure SQL Warehouse-szal).

<!--Articles-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/server-connect.PNG


