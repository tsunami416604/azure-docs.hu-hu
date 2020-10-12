---
title: Kapcsolódás a szinapszis SQL-készlethez
description: Csatlakozás az SQL-készlethez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-csharp
ms.openlocfilehash: ab169f7b814dedbc63f3cb6cddbfb822bdb57ab9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89016444"
---
# <a name="connect-to-synapse-sql-pool"></a>Kapcsolódás a szinapszis SQL-készlethez

Csatlakozás az SQL-készlethez.

## <a name="find-your-server-name"></a>A kiszolgálónév lekérdezése

A kiszolgáló neve a következő példában sqlpoolservername.database.windows.net. A teljes kiszolgálónév lekérdezése:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Kattintson az **Azure szinapszis Analytics**elemre.
3. Kattintson arra az SQL-készletre, amelyhez csatlakozni szeretne.
4. Keresse meg a teljes kiszolgálónevet.

   ![Teljes kiszolgálónév](media/sql-data-warehouse-connect-overview/server-connect.PNG)

## <a name="supported-drivers-and-connection-strings"></a>Támogatott illesztők és kapcsolati sztringek

Az SQL-készlet támogatja a [ADO.net](/dotnet/framework/data/adonet?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), az [ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), a [php](/sql/connect/php/overview-of-the-php-sql-driver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)és a [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)használatát. A legújabb verzió és dokumentáció megkereséséhez kattintson a fenti illesztőprogramok egyikére.

A Azure Portal használt illesztőprogramhoz tartozó kapcsolati karakterlánc automatikus létrehozásához kattintson az előző példában az **adatbázis-kapcsolati karakterláncok megjelenítése** elemre. A következő néhány példa bemutatja, hogy néz ki a kapcsolati sztring az egyes illesztők esetében.

> [!NOTE]
> Javasoljuk, hogy a kapcsolat időkorlátjának 300 másodpercet adjon meg, hogy a kapcsolat rövid idejű kimaradások esetén is fennmaradjon.

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

Az SQL-készlet Szabványosít néhány beállítást a csatlakozás és az objektum létrehozása során. Ezeket a beállításokat nem lehet felülírni, és a következők lehetnek:

| Adatbázis-beállítások | Érték |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |ON |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |ON |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |hné |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |7 |

## <a name="next-steps"></a>Következő lépések

A Visual Studióval végzett csatlakozásról és lekérdezésről lásd: [Lekérdezés a Visual Studióval](sql-data-warehouse-query-visual-studio.md). További információ a hitelesítési lehetőségekről: [Az Azure szinapszis Analytics hitelesítése](sql-data-warehouse-authentication.md).
