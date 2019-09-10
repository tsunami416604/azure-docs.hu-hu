---
title: Hibaelhárítási Azure SQL Data Warehouse | Microsoft Docs
description: Hibaelhárítási Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: ebdeaf21253e89a9a14e3a56ca7be0f6e8adceb0
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859230"
---
# <a name="troubleshooting-connectivity-issues"></a>Kapcsolódási problémák elhárítása

Ez a cikk a SQL Data Warehousehoz való csatlakozással kapcsolatos gyakori hibaelhárítási technikákat sorolja fel.
- [Szolgáltatás rendelkezésre állásának keresése](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Szüneteltetett vagy skálázási művelet keresése](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [A tűzfal beállításainak megtekintése](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [A VNet/szolgáltatás végpontjának beállításai](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [A legújabb illesztőprogramok keresése](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [A kapcsolatok karakterláncának keresése](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Időnkénti kapcsolatok problémái](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Gyakori hibaüzenetek](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Szolgáltatás rendelkezésre állásának keresése

Ellenőrizze, hogy elérhető-e a szolgáltatás. A Azure Portal lépjen a csatlakoztatni kívánt SQL Data Warehouse. A bal oldali tartalomjegyzék panelen kattintson a **problémák diagnosztizálása és megoldása**elemre.

![Erőforrás állapotának kiválasztása](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

A SQL Data Warehouse állapota itt jelenik meg. Ha a szolgáltatás nem elérhetőkéntjelenik meg, tekintse meg a további lépéseket.

![Elérhető szolgáltatás](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Ha az erőforrás állapota azt mutatja, hogy az adattárház szüneteltetve van vagy méretezést végez, kövesse az útmutatást az adattárház folytatásához.

![A szolgáltatás szüneteltette](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) a Resource Health további információit itt találja.

## <a name="check-for-paused-or-scaling-operation"></a>Szüneteltetett vagy skálázási művelet keresése

Ellenőrizze a portálon, hogy a SQL Data Warehouse szüneteltetve van-e, vagy a méretezést.

![Szolgáltatás szüneteltetve](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Ha úgy látja, hogy a szolgáltatás szüneteltetve van vagy méretezést végez, ellenőrizze, hogy nem a karbantartási ütemterv alatt van-e. A SQL Data Warehouse *áttekintését ismertető*portálon láthatja a választott karbantartási ütemtervet.

![Az Áttekintés karbantartási ütemterve](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Ellenkező esetben kérdezze meg a rendszergazdát, hogy a karbantartás nem ütemezett esemény-e. A SQL Data Warehouse folytatásához kövesse az [itt](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute)ismertetett lépéseket.

## <a name="check-your-firewall-settings"></a>A tűzfal beállításainak megtekintése

Az SQL Data Warehouse az 1433-as portot használja a kommunikációhoz.   Ha vállalati hálózaton belülről próbál csatlakozni, előfordulhat, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem tud csatlakozni a Azure SQL Database-kiszolgálóhoz, ha az informatikai részleg nem nyitja meg a 1433-es portot. A tűzfal-konfigurációkról további információt [itt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)találhat.

## <a name="check-your-vnetservice-endpoint-settings"></a>A VNet/szolgáltatás végpontjának beállításai

Ha a 40914-es és a 40615-es hibákat kapja, tekintse meg [itt a hiba leírása és megoldása](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)című témakört.

## <a name="check-for-the-latest-drivers"></a>A legújabb illesztőprogramok keresése

### <a name="software"></a>Szoftver

Győződjön meg arról, hogy a legújabb eszközöket használja a SQL Data Warehousehoz való kapcsolódáshoz:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Illesztőprogramok

Győződjön meg arról, hogy a legújabb illesztőprogram-verziókat használja.  Az illesztőprogramok egy régebbi verziója váratlan viselkedést eredményezhet, mivel a régebbi illesztőprogramok nem támogatják az új szolgáltatásokat.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>A kapcsolatok karakterláncának keresése

Győződjön meg arról, hogy a kapcsolódási karakterláncok megfelelően vannak beállítva.  Az alábbiakban néhány példa látható.  Itt további információkat talál a [kapcsolatok karakterláncáról](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

ADO.NET kapcsolati sztring

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC-kapcsolatok karakterlánca

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP-kapcsolatok karakterlánca

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC-kapcsolati sztring

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Időnkénti kapcsolatok problémái

Ellenőrizze, hogy nagy mennyiségű várólistán lévő kéréssel tapasztal-e nagy terhelést a kiszolgálón. Előfordulhat, hogy további erőforrásokhoz is szükség van az adattárház vertikális felskálázására.

## <a name="common-error-messages"></a>Gyakori hibaüzenetek

A 40914-es és a 40615-es hibákért itt tekintse meg a [hiba leírását és megoldását](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Továbbra is csatlakozási problémák léptek fel?
Hozzon létre egy [támogatási jegyet](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) , hogy a mérnöki csapat támogassa Önt.
