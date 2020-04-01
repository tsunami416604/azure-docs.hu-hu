---
title: Azure Resource Manager-sablonok
description: Az Azure Resource Manager-sablonok használatával létrehozhatja és konfigurálhatja az Azure SQL Database-t.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: b254621cc414fb9b2b76263957adc80da6e9c22d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79214003"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Azure Resource Manager-sablonok az Azure SQL Database-hez

Az Azure Resource Manager-sablonok lehetővé teszik az infrastruktúra kódként történő definiálását és megoldásainak üzembe helyezését az Azure-felhőben.

## <a name="single-database--elastic-pool"></a>[Egyetlen adatbázis & rugalmas készlet](#tab/single-database)

Az alábbi táblázat az Azure Resource Manager-sablonok ra mutató hivatkozásokat tartalmaz az Azure SQL Database-hez.

| |  |
|---|---|
| [Önálló adatbázis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Ez az Azure Resource Manager-sablon egyetlen Azure SQL-adatbázist hoz létre logikai kiszolgálóval, és konfigurálja a tűzfalszabályokat. |
| [Logikai kiszolgáló](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Ez az Azure Resource Manager-sablon logikai kiszolgálót hoz létre az Azure SQL Database számára. |
| [Rugalmas medence](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Ez a sablon lehetővé teszi egy új rugalmas készlet üzembe helyezését az új társított SQL Server és új SQL-adatbázisok hozzárendelésével. |
| [Feladatátvételi csoportok](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Ez a sablon két Azure SQL-logikai kiszolgálót, egy SQL-adatbázist és egy feladatátvételi csoportot hoz létre.|
| [Fenyegetések észlelése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Ez a sablon lehetővé teszi egy Azure SQL-logikai kiszolgáló és az Azure SQL-adatbázisok készletének üzembe helyezését, amelyen engedélyezve van a fenyegetésészlelés, és minden egyes adatbázishoz egy e-mail-címet. A fenyegetésészlelés az SQL Komplex veszélyforrások elleni védelem (ATP) ajánlat része, és olyan biztonsági réteget biztosít, amely válaszol az SQL-kiszolgálókon és adatbázisokon keresztül isztött idákon keresztüli potenciális fenyegetésekre.|
| [Naplózás az Azure Blob Storage szolgáltatásba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Ez a sablon lehetővé teszi, hogy egy Azure SQL logikai kiszolgáló üzembe helyezése naplózási engedélyezve van a naplónaplók írása egy blob storage. Az Azure SQL Database naplózása nyomon követi az adatbázis-eseményeket, és egy naplóba írja őket, amely elhelyezhető az Azure storage-fiókjában, az OMS-munkaterületen vagy az Event Hubs-ban.|
| [Naplózás az Azure Event Hub szolgáltatásba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Ez a sablon lehetővé teszi, hogy egy Azure SQL-kiszolgáló üzembe helyezése naplózási engedélyezve van a naplónaplók írása egy kilépő Event Hub. A naplózási események Eseményközpontba való küldéséhez `Enabled` `State` állítsa `IsAzureMonitorTargetEnabled` `true`be a naplózási beállításokat a és a beállításhoz. Is, konfigurálja `SQLSecurityAuditEvents` diagnosztikai beállítások `master` naplókategóriával az adatbázisban (a kiszolgáló szintű naplózás). Az Azure SQL Database és az SQL Data Warehouse naplózása nyomon követi az adatbázis-eseményeket, és egy olyan naplóba írja őket, amely elhelyezhető az Azure storage-fiókjában, az OMS-munkaterületen vagy az Event Hubs-ban.|
| [Azure Web App SQL-adatbázissal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Ez a minta egy ingyenes Azure Web App és SQL-adatbázist hoz létre az "Alapszintű" szolgáltatás szintjén.|
| [Azure Web App és Redis-gyorsítótár SQL-adatbázissal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Ez a sablon egy webalkalmazást, redis cache-t és SQL-adatbázist hoz létre ugyanabban az erőforráscsoportban, és két kapcsolati karakterláncot hoz létre a Web Appban az SQL Database és a Redis cache számára.|
| [Adatok importálása blobtárolóból az ADF V2 használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Ez az Azure Resource Manager-sablon létrehozza az Azure Data Factory V2-t, amely adatokat másol az Azure Blob Storage-ból az SQL Database-be.|
| [HDInsight-fürt SQL-adatbázissal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Ez a sablon lehetővé teszi egy HDInsight-fürt, egy SQL Database-kiszolgáló, egy SQL-adatbázis és két tábla létrehozását. Ezt a sablont a [Sqoop használata a Hadoop-hoz a HDInsight-cikkben használja.](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Az SQL tárolt eljárást ütemezett SQL-alapú eljárást futtató Azure Logic Alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Ez a sablon lehetővé teszi, hogy hozzon létre egy logikai alkalmazást, amely futtatja az SQL tárolt eljárás ütemezés szerint. Az eljárás bármely argumentuma a sablon törzsszakaszában helyezhető el.|

## <a name="managed-instance"></a>[Felügyelt példány](#tab/managed-instance)

Az alábbi táblázat az Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz az Azure SQL Database – Felügyelt példány számára.

| |  |
|---|---|
| [Felügyelt példány egy új virtuális hálózatban](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Ez az Azure Resource Manager-sablon létrehoz egy új konfigurált Azure virtuális hálózatot és felügyelt példányt a virtuális hálózatban. |
| [Felügyelt példány hálózati környezete](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Ez a központi telepítés létrehoz egy konfigurált Azure virtuális hálózat két alhálózattal - az egyik, hogy lesz a felügyelt példányok, és egy másik, ahol más erőforrásokat (például virtuális gépek, App Service-környezetek, stb.). Ez a sablon egy megfelelően konfigurált hálózati környezetet hoz létre, ahol felügyelt példányokat telepíthet. |
| [Felügyelt példány P2S-kapcsolattal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Ez a központi telepítés két alhálózattal `ManagedInstance` `GatewaySubnet`és . A felügyelt példány a ManagedInstance alhálózatban lesz telepítve. A virtuális hálózati átjáró `GatewaySubnet` alhálózatban jön létre, és a pont-hely VPN-kapcsolathoz lesz konfigurálva. |
| [Felügyelt példány virtuális géppel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Ez a központi telepítés két alhálózattal `ManagedInstance` `Management`és . A felügyelt példány az `ManagedInstance` alhálózatban lesz telepítve. Az SQL Server Management Studio (SSMS) legújabb verziójával `Management` rendelkező virtuális gép az alhálózatban lesz telepítve. |

---
