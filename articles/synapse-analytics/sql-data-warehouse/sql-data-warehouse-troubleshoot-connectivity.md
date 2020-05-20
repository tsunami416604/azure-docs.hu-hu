---
title: Kapcsolat hibaelhárítása
description: A szinapszis SQL-készlet kapcsolatának hibaelhárítása.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: b4fbfb65a609742105056fa7fb849f84579245cb
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650499"
---
# <a name="troubleshooting-connectivity-issues-in-synapse-sql-pool"></a>A szinapszis SQL-készlet kapcsolódási problémáinak elhárítása

Ez a cikk az SQL Pool-adatbázishoz való csatlakozással kapcsolatos gyakori hibaelhárítási technikákat sorolja fel.

## <a name="check-service-availability"></a>Szolgáltatás rendelkezésre állásának keresése

Ellenőrizze, hogy elérhető-e a szolgáltatás. A Azure Portal nyissa meg a csatlakoztatni kívánt SQL-készletet. A bal oldali tartalomjegyzék panelen kattintson a **problémák diagnosztizálása és megoldása**elemre.

![Erőforrás állapotának kiválasztása](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Az SQL-készlet állapota itt jelenik meg. Ha a szolgáltatás nem **elérhetőként**jelenik meg, tekintse meg a további lépéseket.

![Elérhető szolgáltatás](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Ha az erőforrás állapota azt mutatja, hogy az SQL Pool-példány szüneteltetve van vagy méretezést végez, kövesse az útmutatást a példány folytatásához.

![A szolgáltatás szüneteltette ](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) a Resource Health további információit itt találja.

## <a name="check-for-paused-or-scaling-operation"></a>Szüneteltetett vagy skálázási művelet keresése

Ellenőrizze a portálon, hogy az SQL Pool-példány szüneteltetve van-e, illetve hogy van-e skálázás.

![Szolgáltatás szüneteltetve](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Ha úgy látja, hogy a szolgáltatás szüneteltetve van vagy méretezést végez, ellenőrizze, hogy nem a karbantartási ütemterv alatt van-e. Az SQL-készlet *áttekintését*bemutató portálon láthatja a választott karbantartási ütemtervet.

![Az Áttekintés karbantartási ütemterve](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Ellenkező esetben kérdezze meg a rendszergazdát, hogy a karbantartás nem ütemezett esemény-e. Az SQL Pool-példány folytatásához kövesse az [alábbi lépéseket](pause-and-resume-compute-portal.md).

## <a name="check-your-firewall-settings"></a>A tűzfalbeállítások ellenőrzése

Az SQL-készlet adatbázisa a 1433-as porton keresztül kommunikál.Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL-adatbáziskiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 1433-as portot. A tűzfal-konfigurációkról további információt [itt](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules)találhat.

## <a name="check-your-vnetservice-endpoint-settings"></a>A Vnet/szolgáltatásvégpont beállításainak ellenőrzése

Ha a 40914-es és a 40615-es hibákat kapja, tekintse meg [itt a hiba leírása és megoldása](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)című témakört.

## <a name="check-for-the-latest-drivers"></a>A legújabb illesztők keresése

### <a name="software"></a>Szoftverek

Győződjön meg arról, hogy a legújabb eszközöket használja az SQL-készlethez való kapcsolódáshoz:

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Illesztőprogramok

Győződjön meg arról, hogy a legújabb illesztőprogram-verziókat használja.Az illesztőprogramok egy régebbi verziója váratlan viselkedést eredményezhet, mivel a régebbi illesztőprogramok nem támogatják az új szolgáltatásokat.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>A kapcsolati sztring ellenőrzése

Ellenőrizze, hogy a kapcsolati sztring megfelelően van-e beállítva.  Az alábbiakban néhány példa látható.  [Itt talál további információt a kapcsolati sztringekről](sql-data-warehouse-connection-strings.md).

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

## <a name="intermittent-connection-issues"></a>Időszakos kapcsolódási problémák

Ellenőrizze, hogy nagy terhelés alatt áll-e a kiszolgáló, sok várólistára helyezett kéréssel. Előfordulhat, hogy további erőforrásokhoz is szükség van az SQL-készlet vertikális felskálázására.

## <a name="common-error-messages"></a>Gyakori hibaüzenetek

A 40914-es és a 40615-es hibákért itt tekintse meg a [hiba leírását és megoldását](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Továbbra is csatlakozási problémák léptek fel?

Hozzon létre egy [támogatási jegyet](sql-data-warehouse-get-started-create-support-ticket.md) , hogy a mérnöki csapat támogassa Önt.
