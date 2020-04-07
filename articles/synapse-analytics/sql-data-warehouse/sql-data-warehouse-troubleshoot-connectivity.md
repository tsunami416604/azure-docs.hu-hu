---
title: Kapcsolódási hibák elhárítása
description: A synapse-i SQL-készlet kapcsolódásának elhárítása.
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
ms.openlocfilehash: 8aaca40961b1294336b236305ecdffcc92c5a4d9
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742574"
---
# <a name="troubleshooting-connectivity-issues"></a>Kapcsolati problémák hibaelhárítása

Ez a cikk az SQL Analytics-adatbázishoz való csatlakozás gyakori hibaelhárítási technikáit sorolja fel.

- [A szolgáltatás elérhetőségének ellenőrzése](sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Szüneteltetett vagy skálázási művelet keresése](sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [A tűzfalbeállítások ellenőrzése](sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [A Vnet/szolgáltatásvégpont beállításainak ellenőrzése](sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [A legújabb illesztők keresése](sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [A kapcsolati sztring ellenőrzése](sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Időszakos kapcsolódási problémák](sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Gyakori hibaüzenetek](sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>A szolgáltatás elérhetőségének ellenőrzése

Ellenőrizze, hogy a szolgáltatás elérhető-e. Az Azure Portalon nyissa meg a csatlakozni kívánt Synapse SQL-készletet. A bal toc panelen kattintson **a Problémák diagnosztizálása és megoldása gombra.**

![Erőforrás állapotának kiválasztása](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

A Synapse SQL-készlet állapota itt jelenik meg. Ha a szolgáltatás nem érhető **el,** ellenőrizze a további lépéseket.

![Elérhető szolgáltatás](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Ha az erőforrás állapota azt mutatja, hogy a szinapszis SQL-készlet példánya szüneteltetve vagy méretezés, kövesse az útmutatást a példány folytatásához.

![Szolgáltatás szüneteltetve](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) További információ az erőforrás-állapotról itt található.

## <a name="check-for-paused-or-scaling-operation"></a>Szüneteltetett vagy skálázási művelet keresése

Ellenőrizze a portálon, hogy a szinapszis SQL-készletpéldány szünetelt-e vagy méretezés.

![A szolgáltatás szünetel](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Ha azt látja, hogy a szolgáltatás szünetelt vagy méretezés, ellenőrizze, hogy nem a karbantartási ütemezés során. A synapse SQL-készlet *áttekintése*portálon láthatja a választott karbantartási ütemezést.

![Áttekintő karbantartási ütemezés](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Ellenkező esetben kérdezze meg a rendszergazdát, hogy ez a karbantartás nem ütemezett esemény-e. Az SQL Analytics-példány folytatásához kövesse az [alábbi lépéseket.](pause-and-resume-compute-portal.md)

## <a name="check-your-firewall-settings"></a>A tűzfalbeállítások ellenőrzése

Az SQL Analytics adatbázis a 1433-as porton keresztül kommunikál.Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL-adatbáziskiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 1433-as portot. A tűzfalkonfigurációkról itt talál további [információt.](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules)

## <a name="check-your-vnetservice-endpoint-settings"></a>A Vnet/szolgáltatásvégpont beállításainak ellenőrzése

Ha a 40914-es és 40615-ös hibaüzenetet kapja, olvassa el [a hiba leírását és megoldását](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)itt.

## <a name="check-for-the-latest-drivers"></a>A legújabb illesztők keresése

### <a name="software"></a>Szoftverek

Ellenőrizze, hogy a legújabb eszközöket használja-e a Synapse SQL-készlethez való csatlakozáshoz:

- SSMS
- Azure Data Studio
- SQL Server adateszközök (Visual Studio)

### <a name="drivers"></a>Illesztőprogramok

Ellenőrizze, hogy a legújabb illesztőprogram-verziókat használja-e.Az illesztőprogramok régebbi verziójának használata váratlan viselkedést eredményezhet, mivel előfordulhat, hogy a régebbi illesztőprogramok nem támogatják az új funkciókat.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>A kapcsolati sztring ellenőrzése

Ellenőrizze, hogy a kapcsolati sztring megfelelően van-e beállítva.  Az alábbiakban néhány mintát.  [Itt talál további információt a kapcsolati sztringekről](sql-data-warehouse-connection-strings.md).

ADO.NET kapcsolati sztring

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC kapcsolati karakterlánc

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP kapcsolati karakterlánc

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC-kapcsolati sztring

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Időszakos kapcsolódási problémák

Ellenőrizze, hogy nagy terhelés alatt áll-e a kiszolgáló, sok várólistára helyezett kéréssel. Előfordulhat, hogy további erőforrásokért fel kell skáláznia a szinapszis SQL-készletét.

## <a name="common-error-messages"></a>Gyakori hibaüzenetek

Hibák 40914 és 40615, lásd a [hiba leírását és megoldását itt](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Továbbra is vannak kapcsolódási problémák?

Hozzon létre egy [támogatási jegyet,](sql-data-warehouse-get-started-create-support-ticket.md) hogy a mérnöki csapat támogathassa Önt.
