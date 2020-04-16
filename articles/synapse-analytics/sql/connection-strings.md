---
title: Kapcsolati karakterláncok a Synapse SQL-hez (előzetes verzió)
description: Kapcsolati karakterláncok a Synapse SQL-hez (előzetes verzió)
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 8c1f13449c8ee5152e4e5b48af87f41004e599dc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424558"
---
# <a name="connection-strings-for-synapse-sql-preview"></a>Kapcsolati karakterláncok a Synapse SQL-hez (előzetes verzió)

A Synapse SQL -hez (előzetes verzió) számos különböző alkalmazásprotokollt használhat, például [ADO.NET,](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx) [ODBC,](https://msdn.microsoft.com/library/jj730314.aspx) [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)és [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx)protokollal. Az alábbiakban néhány példa a kapcsolatok karakterláncok az egyes protokollok. 

Az Azure Portal on is létrehozhatja a kapcsolati karakterláncot.  Ha az Azure Portal használatával szeretné felépíteni a kapcsolati karakterláncot, keresse meg az adatbázis paneljét, az *Essentials* csoportban kattintson az *Adatbázis-kapcsolati karakterláncok megjelenítése*elemre.

## <a name="sample-adonet-connection-string"></a>Minta ADO.NET kapcsolati karakterlánc

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Minta ODBC kapcsolati karakterlánc

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Példa PHP kapcsolati karakterláncra

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Minta JDBC kapcsolati karakterlánc

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> Fontolja meg a kapcsolat időtúllépésének 300 másodpercre állítását, hogy a kapcsolat túlélhesse a rövid elérhetetlenségi időt.

## <a name="recommendations"></a>Javaslatok

Az **SQL igény szerinti** lekérdezések végrehajtásához ajánlott eszközök az Azure Data [Studio](get-started-azure-data-studio.md) és az Azure Synapse Studio.

## <a name="next-steps"></a>További lépések

Ha meg szeretné kezdeni az elemzés lekérdezését a Visual Studio-val és más alkalmazásokkal, olvassa el [a Lekérdezés a Visual Studióval](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
