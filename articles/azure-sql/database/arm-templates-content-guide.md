---
title: Azure Resource Manager-sablonok
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database és az Azure SQL felügyelt példányának létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 0e3688fdbeabd525e54aad1b9e36c82b9c86751f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053290"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Azure SQL Database & SQL felügyelt példányának Azure Resource Manager sablonjai
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure Resource Manager sablonok lehetővé teszik az infrastruktúra kódként való definiálását és a megoldások Azure-felhőben való üzembe helyezését a Azure SQL Database és az Azure SQL felügyelt példányai esetében.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Az alábbi táblázat a Azure SQL Database Azure Resource Manager sablonjaira mutató hivatkozásokat tartalmaz.

| |  |
|---|---|
| [SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Ez a Azure Resource Manager sablon egyetlen adatbázist hoz létre a Azure SQL Databaseban, és konfigurálja a kiszolgálói szintű IP-tűzfalszabályok beállításait. |
| [Kiszolgáló](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Ez a Azure Resource Manager-sablon létrehoz egy kiszolgálót a Azure SQL Databasehoz. |
| [Rugalmas készlet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Ez a sablon lehetővé teszi egy rugalmas készlet üzembe helyezését és az adatbázisok hozzárendelését. |
| [Feladatátvételi csoportok](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Ez a sablon két kiszolgálót, egyetlen adatbázist és egy feladatátvételi csoportot hoz létre Azure SQL Databaseban.|
| [Fenyegetések észlelése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Ez a sablon lehetővé teszi, hogy egy-kiszolgálót és egy olyan adatbázist helyezzen üzembe, amelyen engedélyezve van a fenyegetések észlelése, és e-mail-címmel kell rendelkeznie az egyes adatbázisokra vonatkozó riasztásokhoz. A veszélyforrások észlelése az SQL Advanced Threat Protection (ATP) ajánlat része, és olyan biztonsági réteget biztosít, amely a kiszolgálók és adatbázisok lehetséges fenyegetésekre reagál.|
| [Naplózás az Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Ez a sablon lehetővé teszi, hogy olyan kiszolgálót helyezzen üzembe, amelyen engedélyezve van a naplózás, hogy a naplófájlokat blob-tárolóba írja. A Azure SQL Database naplózása nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókba, OMS-munkaterületbe vagy Event Hubsba helyezhető naplófájlba írja azokat.|
| [Naplózás az Azure Event hub-ba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Ez a sablon lehetővé teszi, hogy olyan kiszolgálót helyezzen üzembe, amelyen engedélyezve van a naplózás, hogy a naplókat egy meglévő Event hubhoz írja. A naplózási események az Event hubhoz való küldéséhez állítsa be a naplózási beállításokat a `Enabled` `State` és a beállításként `IsAzureMonitorTargetEnabled` `true` . Továbbá konfigurálja a diagnosztikai beállításokat a `SQLSecurityAuditEvents` naplózási kategóriával az `master` adatbázisban (a kiszolgálás szintjének naplózásához). A naplózás nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókba, a OMS-munkaterületbe vagy a Event Hubsba helyezhető naplófájlba írja azokat.|
| [Azure-webalkalmazás és SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Ez a példa létrehoz egy ingyenes Azure-webalkalmazást és egy Azure SQL Database-adatbázist az "alapszintű" szolgáltatási szinten.|
| [Azure-webalkalmazás és Redis Cache a következővel SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Ez a sablon létrehoz egy webalkalmazást, Redis Cache és SQL Database ugyanabban az erőforráscsoportban, és két kapcsolati karakterláncot hoz létre a webalkalmazásban a SQL Database és Redis Cache.|
| [Adatok importálása a blob Storage-ból az ADF V2 használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Ez a Azure Resource Manager sablon Azure Data Factory v2-et hoz létre, amely az Blob Storage Azure-ból származó adatok SQL Databaseba való másolását végzi.|
| [HDInsight-fürt SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Ez a sablon lehetővé teszi, hogy HDInsight-fürtöt, logikai SQL Servert, adatbázist és két táblát hozzon létre. Ezt a sablont [használja a Sqoop használata a Hadoop-ben a HDInsight cikkben](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Az Azure logikai alkalmazás, amely egy ütemezett SQL tárolt eljárást futtat](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Ez a sablon lehetővé teszi, hogy olyan logikai alkalmazást hozzon létre, amely egy ütemezett SQL tárolt eljárást fog futtatni. Az eljáráshoz tartozó argumentumok a sablon törzs szakaszába helyezhetők.|

## <a name="azure-sql-managed-instance"></a>[Felügyelt Azure SQL-példány](#tab/managed-instance)

A következő táblázat hivatkozásokat tartalmaz az Azure SQL felügyelt példányának Azure Resource Manager sablonjaira.

| |  |
|---|---|
| [SQL felügyelt példány egy új VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Ez a Azure Resource Manager sablon létrehoz egy új, konfigurált Azure VNet és SQL felügyelt példányt a VNet. |
| [Az SQL felügyelt példányának hálózati környezete](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Ez az üzembe helyezés egy konfigurált Azure-Virtual Network hoz létre két alhálózattal – az egyiket, amely az SQL felügyelt példányaihoz lesz hozzárendelve, és egy másik, ahol más erőforrásokat (például virtuális gépeket, App Service környezeteket stb.) helyezhet el. Ez a sablon egy megfelelően konfigurált hálózati környezetet hoz létre, ahol telepítheti az SQL felügyelt példányait. |
| [SQL felügyelt példány P2S-kapcsolatban](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Ez az üzembe helyezés egy Azure-Virtual Network hoz létre két alhálózattal `ManagedInstance` és `GatewaySubnet` . Az SQL felügyelt példánya a ManagedInstance alhálózatban lesz telepítve. A rendszer létrehozza a virtuális hálózati átjárót az `GatewaySubnet` alhálózatban, és a pont – hely VPN-kapcsolathoz van konfigurálva. |
| [SQL felügyelt példány virtuális géppel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Ez az üzembe helyezés egy Azure-Virtual Network hoz létre két alhálózattal `ManagedInstance` és `Management` . Az SQL felügyelt példánya az `ManagedInstance` alhálózatban lesz telepítve. A SQL Server Management Studio (SSMS) legújabb verzióját tartalmazó virtuális gép az `Management` alhálózaton lesz telepítve. |

---
