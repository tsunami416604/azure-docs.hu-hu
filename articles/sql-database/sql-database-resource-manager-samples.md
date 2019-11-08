---
title: Azure Resource Manager-sablonok
description: Azure SQL Database létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat.
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
ms.openlocfilehash: 16091286aecc6dfdbcfa833a76920ff4bc9c64de
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821131"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Azure SQL Database Azure Resource Manager sablonjai

Azure Resource Manager sablonok lehetővé teszik az infrastruktúra kódként való definiálását és a megoldások üzembe helyezését az Azure-felhőben.

## <a name="single-database--elastic-pooltabsingle-database"></a>[Önálló adatbázis & rugalmas készlet](#tab/single-database)

Az alábbi táblázat a Azure SQL Database Azure Resource Manager sablonjaira mutató hivatkozásokat tartalmaz.

| |  |
|---|---|
| [Önálló adatbázis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Ez a Azure Resource Manager sablon egyetlen Azure SQL Database hoz létre a logikai kiszolgálóval, és konfigurálja a tűzfalszabályok beállításait. |
| [Logikai kiszolgáló](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Ez a Azure Resource Manager-sablon logikai kiszolgálót hoz létre a Azure SQL Databasehoz. |
| [Rugalmas készlet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Ez a sablon lehetővé teszi egy új rugalmas készlet üzembe helyezését az új társított SQL Server és az új SQL-adatbázisok hozzárendelésével. |
| [Feladatátvételi csoportok](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Ez a sablon két Azure SQL logikai kiszolgálót, egy SQL-adatbázist és egy feladatátvételi csoportot hoz létre.|
| [Fenyegetések észlelése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Ez a sablon lehetővé teszi egy Azure SQL logikai kiszolgáló és egy olyan Azure SQL Database-adatbázis üzembe helyezését, amelyen engedélyezve van a veszélyforrások észlelése, és e-mail-címmel kell rendelkeznie az egyes adatbázisokra vonatkozó riasztásokhoz. A veszélyforrások észlelése az SQL Advanced Threat Protection (ATP) ajánlat része, és olyan biztonsági réteget biztosít, amely az SQL-kiszolgálókon és-adatbázisokon keresztül lehetséges fenyegetésekre reagál.|
| [Naplózás az Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Ez a sablon lehetővé teszi, hogy olyan Azure SQL logikai kiszolgálót helyezzen üzembe, amelyen engedélyezve van a naplózás, hogy a naplók egy blob Storage-tárolóba írjanak. A Azure SQL Database naplózása nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókba, OMS-munkaterületbe vagy Event Hubsba helyezhető naplófájlba írja azokat.|
| [Naplózás az Azure Event hub-ba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Ez a sablon lehetővé teszi, hogy egy olyan Azure SQL Servert helyezzen üzembe, amelyen engedélyezve van a naplózás, hogy naplókat írjon a kilépő Event hubhoz. A naplózási események az Event hub szolgáltatásba való küldéséhez állítsa be a naplózási beállításokat `Enabled` `State` és állítsa be a `IsAzureMonitorTargetEnabled` as `true`értékre. Továbbá konfigurálja a diagnosztikai beállításokat `SQLSecurityAuditEvents` diagnosztikai naplók kategóriával a `master` adatbázison (a kiszolgálás szintjének naplózásához). A Azure SQL Database és SQL Data Warehouse naplózása nyomon követi az adatbázis-eseményeket, és az Azure Storage-fiókba, a OMS-munkaterületbe vagy a Event Hubsba helyezhető naplófájlba írja azokat.|
| [Azure-webalkalmazás és SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Ez a példa egy ingyenes Azure-webalkalmazást hoz létre, és SQL Database az "alapszintű" szolgáltatási szinten.|
| [Azure-webalkalmazás és Redis Cache a következővel SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Ez a sablon létrehoz egy webalkalmazást, Redis Cache és SQL Database ugyanabban az erőforráscsoportban, és két kapcsolati karakterláncot hoz létre a webalkalmazásban a SQL Database és Redis Cache.|
| [Adatok importálása a blob Storage-ból az ADF V2 használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Ez a Azure Resource Manager sablon Azure Data Factory v2-et hoz létre, amely az Blob Storage Azure-ból származó adatok SQL Databaseba való másolását végzi.|
| [HDInsight-fürt SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Ez a sablon lehetővé teszi egy HDInsight-fürt, egy SQL Database-kiszolgáló, egy SQL Database és két tábla létrehozását. Ezt a sablont [használja a Sqoop használata a Hadoop-ben a HDInsight cikkben](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Az Azure logikai alkalmazás, amely egy ütemezett SQL tárolt eljárást futtat](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Ez a sablon lehetővé teszi, hogy olyan logikai alkalmazást hozzon létre, amely egy ütemezett SQL tárolt eljárást fog futtatni. Az eljáráshoz tartozó argumentumok a sablon törzs szakaszába helyezhetők.|

## <a name="managed-instancetabmanaged-instance"></a>[Felügyelt példány](#tab/managed-instance)

Az alábbi táblázat a Azure SQL Database által felügyelt példányokra Azure Resource Manager sablonokra mutató hivatkozásokat tartalmaz.

| |  |
|---|---|
| [Felügyelt példány egy új VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Ez a Azure Resource Manager sablon létrehoz egy új, konfigurált Azure VNet és felügyelt példányt a VNet. |
| [A felügyelt példány hálózati környezete](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Ez az üzembe helyezés egy konfigurált Azure-Virtual Network hoz létre két alhálózattal – az egyiket a felügyelt példányok számára, és egy másikat, ahol más erőforrásokat (például virtuális gépeket, App Service környezeteket stb.) helyezhet el. Ez a sablon egy megfelelően konfigurált hálózati környezetet hoz létre, ahol felügyelt példányokat helyezhet üzembe. |
| [Felügyelt példány P2S-kapcsolatban](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Ez az üzembe helyezés egy Azure-Virtual Network hoz létre két alhálózattal `ManagedInstance` és `GatewaySubnet`. A felügyelt példány a ManagedInstance-alhálózatban lesz telepítve. A virtuális hálózati átjáró `GatewaySubnet` alhálózatban jön létre, és pont – hely típusú VPN-kapcsolatra van konfigurálva. |
| [Felügyelt példány virtuális géppel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Ez az üzembe helyezés egy Azure-Virtual Network hoz létre két alhálózattal `ManagedInstance` és `Management`. A felügyelt példány `ManagedInstance` alhálózatban lesz telepítve. A SQL Server Management Studio (SSMS) legújabb verzióját tartalmazó virtuális gép `Management` alhálózatban lesz telepítve. |

---
