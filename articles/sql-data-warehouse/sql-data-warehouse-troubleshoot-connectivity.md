---
title: Kapcsolat hibaelhárítása
description: Az SQL Analytics-kapcsolat hibaelhárítása.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 003366a6d88e018090475b6fb22d9042a97af823
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78192251"
---
# <a name="troubleshooting-connectivity-issues"></a>Kapcsolódási problémák elhárítása

Ez a cikk az SQL Analytics-adatbázishoz való csatlakozás általános hibaelhárítási módszereit sorolja fel.
- [Szolgáltatás rendelkezésre állásának keresése](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Szüneteltetett vagy skálázási művelet keresése](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [A tűzfalbeállítások ellenőrzése](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [A Vnet/szolgáltatásvégpont beállításainak ellenőrzése](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [A legújabb illesztők keresése](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [A kapcsolati sztring ellenőrzése](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Időszakos kapcsolódási problémák](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Gyakori hibaüzenetek](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Szolgáltatás rendelkezésre állásának keresése

Ellenőrizze, hogy elérhető-e a szolgáltatás. A Azure Portal nyissa meg a csatlakoztatni kívánt SQL Analytics-adatbázist. A bal oldali tartalomjegyzék panelen kattintson a **problémák diagnosztizálása és megoldása**elemre.

![Erőforrás állapotának kiválasztása](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Az SQL Analytics állapota itt jelenik meg. Ha a szolgáltatás nem **elérhetőként**jelenik meg, tekintse meg a további lépéseket.

![Elérhető szolgáltatás](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Ha az erőforrás állapota azt mutatja, hogy az SQL Analytics-példány szüneteltetve van vagy méretezést végez, kövesse az útmutatást a példány folytatásához.

a ![szolgáltatás szüneteltetve](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) további információkkal szolgál Resource Healthről.

## <a name="check-for-paused-or-scaling-operation"></a>Szüneteltetett vagy skálázási művelet keresése

A portálon ellenőrizze, hogy az SQL Analytics-példány szüneteltetve van-e vagy sem.

![Szolgáltatás szüneteltetve](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Ha úgy látja, hogy a szolgáltatás szüneteltetve van vagy méretezést végez, ellenőrizze, hogy nem a karbantartási ütemterv alatt van-e. Az SQL Analytics *– Áttekintés*portálon láthatja a választott karbantartási ütemtervet.

![Az Áttekintés karbantartási ütemterve](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Ellenkező esetben kérdezze meg a rendszergazdát, hogy a karbantartás nem ütemezett esemény-e. Az SQL Analytics-példány folytatásához kövesse az [itt](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute)ismertetett lépéseket.

## <a name="check-your-firewall-settings"></a>A tűzfal beállításainak megtekintése

Az SQL Analytics-adatbázis a 1433-as porton keresztül kommunikál.   Ha vállalati hálózaton belülről próbál csatlakozni, előfordulhat, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL-adatbáziskiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 1433-as portot. A tűzfal-konfigurációkról további információt [itt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)találhat.

## <a name="check-your-vnetservice-endpoint-settings"></a>A VNet/szolgáltatás végpontjának beállításai

Ha a 40914-es és a 40615-es hibákat kapja, tekintse meg [itt a hiba leírása és megoldása](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)című témakört.

## <a name="check-for-the-latest-drivers"></a>A legújabb illesztőprogramok keresése

### <a name="software"></a>Szoftver

Győződjön meg arról, hogy a legújabb eszközöket használja az SQL Analytics-adatbázishoz való kapcsolódáshoz:

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

Ellenőrizze, hogy a kapcsolati sztring megfelelően van-e beállítva.  Az alábbiakban néhány példa látható.  [Itt talál további információt a kapcsolati sztringekről](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

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

Ellenőrizze, hogy nagy terhelés alatt áll-e a kiszolgáló, sok várólistára helyezett kéréssel. Előfordulhat, hogy további erőforrásokhoz is szükség van az SQL Analytics-példány vertikális felskálázására.

## <a name="common-error-messages"></a>Gyakori hibaüzenetek

A 40914-es és a 40615-es hibákért itt tekintse meg a [hiba leírását és megoldását](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Továbbra is csatlakozási problémák léptek fel?
Hozzon létre egy [támogatási jegyet](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) , hogy a mérnöki csapat támogassa Önt.
