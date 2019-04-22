---
title: Az SQL Database az Azure Resource Manager-sablonokkal |} A Microsoft Docs
description: Használja az Azure Resource Manager-sablonok létrehozása és konfigurálása az Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 1e2f55d334d432544ffc185e5c68dfe4325b2b09
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59356817"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Az Azure SQL Database az Azure Resource Manager-sablonok

Az Azure Resource Manager-sablonok lehetővé teszik határozza meg az infrastruktúra mint kód és a megoldások üzembe helyezése az Azure felhőben.

## <a name="single-database--elastic-pool"></a>Önálló adatbázis és rugalmas készlet

A következő táblázat az Azure SQL Database az Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz.

| |  |
|---|---|
| [Önálló adatbázis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Az Azure Resource Manager-sablonnal hoz létre egy Azure SQL Database logikai kiszolgáló, és konfigurálja a tűzfalszabályokat. |
| [A logikai kiszolgáló](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Az Azure Resource Manager-sablon az Azure SQL Database létrehoz egy logikai kiszolgálót. |
| [Rugalmas készlet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Ez a sablon lehetővé teszi az új társított SQL-kiszolgáló és az új SQL-adatbázisok, rendelje hozzá egy új rugalmas készlet üzembe helyezését. |
| [Feladatátvételi csoportok](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Ez a sablon létrehoz két Azure SQL logikai kiszolgálók, az SQL-adatbázis és a egy feladatátvételi csoportot.|
| [Advanced Threat Protection](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-advanced-threat-protection-server-policy) | Ez a sablon üzembe helyezése az Azure SQL logikai kiszolgáló komplex veszélyforrások elleni védelem engedélyezve van, és a egy nem kötelező az Azure SQL Database lehetővé teszi. Az SQL Advanced Threat Protection fejlett SQL-biztonsági funkciókat tartalmazó egyesített biztonsági csomag.|
| [Fenyegetések észlelése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Ez a sablon üzembe helyezése az Azure SQL logikai kiszolgáló és a Fenyegetésészlelés engedélyezve van, a riasztásokat az egyes adatbázisok egy e-mail címet az Azure SQL Database számos teszi lehetővé. Fenyegetésészlelés az SQL komplex veszélyforrások elleni védelem (ATP) ajánlat része, és reagáljon a lehetséges fenyegetések SQL Server-kiszolgálók és adatbázisok biztonsági réteget biztosít.|
| [Az Azure Blob Storage-naplózás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Ez a sablon lehetővé teszi, hogy üzembe helyezés az Azure SQL logikai kiszolgáló a naplózás engedélyezve van a vizsgálati naplók írni egy blob storage-bA. Azure SQL Database Auditing szolgáltatása nyomon követi az adatbázisok eseményeit, és az Azure storage-fiókot, OMS-munkaterület vagy az Event Hubs helyezhető auditálási naplóba írja őket.|
| [Naplózás az Azure Event Hubs segítségével](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Ez a sablon üzembe helyezése egy Azure SQL server-naplózás engedélyezve van a vizsgálati naplók írni egy létező Eseményközpont teszi lehetővé. Annak érdekében, hogy a naplózási események küldése eseményközpontba, állítsa be a naplózási beállítások a `Enabled` `State` és `IsAzureMonitorTargetEnabled` , `true`. A diagnosztikai beállítások konfigurálása is, `SQLSecurityAuditEvents` diagnosztikai naplók kategóriában található a `master` adatbázis (az szolgálnak szintű naplózás). Az Azure SQL Database és az SQL Data Warehouse naplózás nyomon követi az adatbázisok eseményeit, és az Azure storage-fiókot, OMS-munkaterület vagy az Event Hubs helyezhető auditálási naplóba írja őket.|
| [Azure-webalkalmazást az SQL Database szolgáltatással](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Ez a példa létrehoz egy ingyenes Azure-webalkalmazás és SQL Database az "Alapszintű" szolgáltatási szinten.|
| [Azure-webalkalmazás és Redis Cache az SQL Database szolgáltatással](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Ezzel a sablonnal hoz létre egy webalkalmazás, Redis Cache és SQL Database ugyanazt az erőforráscsoportot, és az SQL Database és a Redis Cache a webalkalmazás két kapcsolati karakterláncok hoz létre.|
| [Adatok importálása az ADF v2 verzió használata blob storage-ból](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Az Azure Resource Manager-sablont hoz létre Azure Data Factory V2, amely adatokat másol az Azure Blob Storage, SQL Database.|
| [HDInsight-fürt SQL Database-adatbázissal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Ez a sablon lehetővé teszi, hogy hozzon létre egy HDInsight-fürtöt, egy SQL Database-kiszolgáló, SQL-adatbázis és a két táblázat. Ez a sablon használják a [használata Sqoop, a cikk a HDInsight Hadoop-keretrendszerrel](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Az Azure olyan logikai alkalmazást, amely egy SQL tárolt eljárás ütemezés szerint fut](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Ez a sablon lehetővé teszi, hogy hozzon létre egy logikai alkalmazást, amely egy SQL tárolt eljárás fog futni, ütemezés szerint. Az eljáráshoz tartozó argumentumokat is az elhelyezhető, hogy a sablon törzsét szakaszában.|

## <a name="managed-instance"></a>Felügyelt példány

A következő táblázat az Azure SQL Database – felügyelt példány az Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz.

| |  |
|---|---|
| [Felügyelt példány egy új virtuális hálózat](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Az Azure Resource Manager-sablon a virtuális hálózatban hoz létre egy új konfigurált Azure virtuális hálózat és a felügyelt példány. |
| [Hálózati környezet a felügyelt példány](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | A központi telepítés hoz létre konfigurált Azure virtuális hálózat két alhálózattal – egy másikkal, amelyik számára a felügyelt példányok dedikált lesz, és a egy másik helyezheti el más erőforrásokat (például virtuális gépek, App Service Environment-környezetek, stb.). Ez a sablon létrehoz egy megfelelően konfigurált hálózati környezetben, ahol felügyelt példány is üzemeltethető. |
| [Felügyelt példány P2S-kapcsolattal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | A központi telepítés rendszer egy Azure virtuális hálózat létrehozása két alhálózattal `ManagedInstance` és `GatewaySubnet`. Felügyelt példány ManagedInstance alhálózat lesz telepítve. Virtuális hálózati átjáró jön létre a `GatewaySubnet` alhálózat és a pont – hely VPN-kapcsolathoz konfigurálva. |
| [A virtuális gép felügyelt példány](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | A központi telepítés rendszer egy Azure virtuális hálózat létrehozása két alhálózattal `ManagedInstance` és `Management`. Felügyelt példány lesz telepítve, `ManagedInstance` alhálózat. Virtuális gép az SQL Server Management Studio (SSMS) legújabb verziójával lesz telepítve, `Management` alhálózat. |
