---
title: Kapcsolati karakterláncok az Azure SQL Data Warehouse |} A Microsoft Docs
description: Kapcsolati karakterláncok az SQL Data warehouse-hoz
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: de875731ffd2170f70d8034f0178d61351d86952
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43248021"
---
# <a name="connection-strings-for-azure-sql-data-warehouse"></a>Kapcsolati karakterláncok az Azure SQL Data warehouse-hoz
Kapcsolódás az SQL Data Warehouse számos különböző alkalmazás protokollokkal, mint például [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP] [ PHP] és [JDBC][JDBC]. Az alábbiakban néhány példa a kapcsolatok karakterláncok minden protokollhoz.  Az Azure portal használatával hozhat létre a kapcsolati karakterláncot.  A kapcsolati karakterláncot az Azure portal használatával hozhat létre, keresse meg az adatbázis paneljén a *Essentials* kattintson a *adatbázis kapcsolati karakterláncainak megjelenítése*.

## <a name="sample-adonet-connection-string"></a>Minta ADO.NET kapcsolati karakterlánc
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Minta ODBC kapcsolati karakterlánc
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Minta PHP kapcsolati karakterlánc
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Minta JDBC kapcsolati karakterlánc
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

> [!NOTE]
> Fontolja meg a beállítás a kapcsolat időtúllépése 300 másodperc, annak érdekében, hogy a kapcsolat rövid idejű kimaradások esetén is fennmaradjon.
> 
> 

## <a name="next-steps"></a>További lépések
A Visual Studio és más alkalmazások az adatraktár lekérdezéséről, lásd: [lekérdezés a Visual Studióval][Query with Visual Studio].

<!--Image references-->

<!--Azure.com references-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx

<!--Other references-->
