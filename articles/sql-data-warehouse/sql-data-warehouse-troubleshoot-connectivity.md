---
title: Hibaelhárítás az Azure SQL Data warehouse-bA |} A Microsoft Docs
description: Hibaelhárítás az Azure SQL Data warehouse-bA.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 03/27/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 5115ffbc3568c87c37bae4a3e65c37f8504f1fb8
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541128"
---
# <a name="troubleshooting-connectivity-issues"></a>Csatlakozási problémák elhárítása

Ez a cikk a gyakori hibaelhárítási eljárásokat körül csatlakozik az SQL Data Warehouse sorolja fel.
- [Szolgáltatás elérhetőségének ellenőrzése](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Felfüggesztett vagy skálázási művelet keresése](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Ellenőrizze a tűzfalbeállításokat](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Ellenőrizze a virtuális hálózat/Service-végpont beállításait](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [A legújabb illesztőprogramokkal keresése](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Ellenőrizze a kapcsolati karakterlánc](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Nem állandó hálózati kapcsolat hibái](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Gyakori hibaüzenetek](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Szolgáltatás elérhetőségének ellenőrzése

Ellenőrizze, hogy a szolgáltatás elérhető-e. Az Azure Portalon nyissa meg az SQL data warehouse kapcsolódni próbál. A bal oldali Tartalomjegyzékben panelen kattintson a **diagnosztizálása és a problémák megoldásához**.

![Válassza ki a Resource health](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Az SQL data warehouse állapota megjelenik. Ha a szolgáltatás nem jelenik meg **elérhető**, ellenőrizze a további lépéseket.

![A szolgáltatás elérhető](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Ha a Resource health azt mutatja, hogy az adattárház szüneteltetve van, vagy a méretezés, kövesse az útmutatást az adattárház folytatása.

![Szolgáltatás szüneteltetve](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Resource Health szolgáltatással kapcsolatos további információkat itt található.

## <a name="check-for-paused-or-scaling-operation"></a>Felfüggesztett vagy skálázási művelet keresése

Ellenőrizze a portálon megtekintheti, ha az SQL-adattárház szüneteltetve van vagy méretezést.

![Szolgáltatás szüneteltetve](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Ha látni, hogy a szolgáltatás fel van függesztve vagy méretezés, ellenőrizze, hogy a karbantartási ütemezéshez során nem. A portál, az SQL data warehouse *áttekintése*, láthatja, hogy a kijelölt karbantartási ütemezés.

![Karbantartási ütemezés – áttekintés](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Ellenkező esetben ellenőrizze a rendszergazdát, hogy ellenőrizze, hogy a karbantartás nem ütemezett esemény. Újból használni szeretné az SQL data warehouse, kövesse a lépéseket követve [Itt](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Ellenőrizze a tűzfalbeállításokat

Az SQL Data Warehouse az 1433-as portot használja a kommunikációhoz.   Csatlakozás a vállalati hálózaton belülről próbál, ha a hálózati tűzfal előfordulhat, hogy nem engedélyezett a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem lehet csatlakoztatni az Azure SQL Database-kiszolgálóhoz, ha az informatikai részleg megnyitja az 1433-as porton. További információ a tűzfal-konfigurációk található [Itt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json#manage-ip-firewall-rules-using-the-azure-portal).

## <a name="check-your-vnetservice-endpoint-settings"></a>Ellenőrizze a virtuális hálózat/Service-végpont beállításait

Ha hibákat 40914 és 40615 azért küldtük, [hiba leírása és a megoldás Itt](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>A legújabb illesztőprogramokkal keresése

### <a name="software"></a>Szoftver

Ellenőrizze, hogy a legújabb eszközök használatával csatlakozhat az SQL data warehouse:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Illesztőprogramok

Ellenőrizze, hogy a legújabb illesztőprogramot használja.  Az illesztőprogramok egy régebbi verzióját használja azt eredményezheti, hogy nem várt működést, a régebbi illesztőprogramok nem támogatja új funkciókat.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Ellenőrizze a kapcsolati karakterlánc

Ellenőrizze, hogy a kapcsolati karakterláncok megfelelően vannak-e beállítva.  Az alábbiakban néhány-minta kipróbálásával.  További információkat talál [kapcsolati karakterláncok, itt](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

ADO.NET kapcsolati sztring

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC kapcsolati karakterlánc

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

A PHP kapcsolati karakterlánc

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC-kapcsolati sztring

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Nem állandó hálózati kapcsolat hibái

Ellenőrizze, hogy ha problémákat tapasztal a kiszolgálón a várólistára került kérelmeket nagy mennyiségű nagy terhelés. Szükség lehet, vertikális felskálázása további erőforrások az adattárház.

## <a name="common-error-messages"></a>Gyakori hibaüzenetek

Hibák 40914 és 40615, tekintse meg a [hiba leírása és a megoldás Itt](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Továbbra is kapcsolódási problémái vannak?
Hozzon létre egy [támogatási jegyet](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) , a mérnöki csapathoz képesek segítséget nyújtani.
