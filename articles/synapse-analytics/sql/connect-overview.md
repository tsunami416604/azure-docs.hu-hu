---
title: Csatlakozás a Synapse SQL-hez
description: Csatlakozzon a Synapse SQL-hez.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 9748b0354ce09752296fb7d736e09af716f19351
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424607"
---
# <a name="connect-to-synapse-sql"></a>Csatlakozás a Synapse SQL-hez
Csatlakozzon a Synapse SQL-funkcióhoz az Azure Synapse Analytics szolgáltatásban.

## <a name="supported-tools-for-sql-on-demand-preview"></a>Támogatott eszközök az igény szerinti SQL-hez (előzetes verzió)

A teljes mértékben támogatott eszköz az Azure Data Studio (előzetes verzió).

Az SQL Server Management Studio részben a 18.4-es verzióból támogatott. Vannak korlátozott jellegét meghatározza mint összekapcsolás és kérdezés.

## <a name="find-your-server-name"></a>A kiszolgálónév lekérdezése

Az SQL Pool kiszolgálóneve a következő példában a következő: showdemoweu.sql.azuresynapse.net.
Az SQL on-demand kiszolgálóneve a következő példában a következő: showdemoweu-ondemand.sql.azuresynapse.net.

A teljes kiszolgálónév lekérdezése:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. Kattintson a **Synapse munkaterületekre.**
3. Kattintson arra a munkaterületre, amelyhez csatlakozni szeretne.
4. Ugrás az áttekintésre.
5. Keresse meg a teljes kiszolgálónevet.

## <a name="sql-pool"></a>**SQL-készlet**

![Teljes kiszolgálónév](./media/connect-overview/server-connect-example.png)

## <a name="sql-on-demand"></a>**SQL igény szerint**

![Teljes kiszolgálónév SQL igény szerint](./media/connect-overview/server-connect-example-sqlod.png)

## <a name="supported-drivers-and-connection-strings"></a>Támogatott illesztők és kapcsolati sztringek
A Synapse SQL támogatja [a ADO.NET,](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx) [odbc,](https://msdn.microsoft.com/library/jj730314.aspx) [php](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)és [JDBC .](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx) A legújabb verzió és dokumentáció megkereséséhez kattintson az előző illesztőprogramok egyikére. Ha automatikusan létre szeretné hozni a kapcsolati karakterláncot az Azure Portalon használt illesztőprogramhoz, kattintson az előző példa **adatbázis-kapcsolati karakterláncok megjelenítése** elemre. A következő néhány példa bemutatja, hogy néz ki a kapcsolati sztring az egyes illesztők esetében.

> [!NOTE]
> Javasoljuk, hogy a kapcsolat időkorlátjának 300 másodpercet adjon meg, hogy a kapcsolat rövid idejű kimaradások esetén is fennmaradjon.

### <a name="adonet-connection-string-example"></a>Minta ADO.NET kapcsolati sztring

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Minta ODBC kapcsolati sztring

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Minta PHP kapcsolati sztring

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Minta JDBC kapcsolati sztring

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Kapcsolati beállítások
A Synapse SQL szabványosítja a kapcsolatok és az objektumok létrehozása során bizonyos beállításokat. Ezeket a beállításokat nem lehet felülírni, és a következők lehetnek:

| Adatbázis-beállítások | Érték |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |ON |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |ON |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |hné |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |7 |

## <a name="recommendations"></a>Javaslatok

Az **SQL igény szerinti** lekérdezések végrehajtásához ajánlott eszközök az Azure Data [Studio](get-started-azure-data-studio.md) és az Azure Synapse Studio.

## <a name="next-steps"></a>További lépések
A Visual Studióval végzett csatlakozásról és lekérdezésről lásd: [Lekérdezés a Visual Studióval](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). A hitelesítési beállításokról a [Synapse SQL hitelesítése](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)című témakörben olvashat bővebben.