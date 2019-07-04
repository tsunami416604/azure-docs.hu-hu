---
title: 'Az Azure Resource Manager: Hozzon létre egy önálló adatbázis – Azure SQL Database |} A Microsoft Docs'
description: Önálló adatbázis létrehozása az Azure SQL Database az Azure Resource Manager-sablon használatával.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
manager: craigg
ms.date: 06/28/2019
ms.openlocfilehash: 4ef0f9ff6f8620109f2ef6f6bd5f549281b4de54
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472147"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Gyors útmutató: Önálló adatbázis létrehozása az Azure SQL Database az Azure Resource Manager-sablon használatával

Létrehozás egy [önálló adatbázis](sql-database-single-database.md) a leggyorsabb és legegyszerűbb üzembehelyezési megoldás az Azure SQL Database-adatbázis létrehozása. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy önálló adatbázis az Azure Resource Manager-sablon használatával. További információkért lásd: [Azure Resource Manager dokumentációjában](/azure/azure-resource-manager/).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Önálló adatbázis létrehozása

Önálló adatbázis az egyik két számítási, memória, IO és tárolási erőforrások egy meghatározott készletével rendelkezik [vásárlási modellek](sql-database-purchase-models.md). Ha létrehoz egy adatbázist, akkor is definiálni egy [SQL Database-kiszolgáló](sql-database-servers.md) felügyelni, és helyezze belül [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) egy meghatározott régióban.

A következő JSON-fájlt az ebben a cikkben használt sablon. A sablon tárolva van [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/SQLServerAndDatabase/azuredeploy.json). Több Azure SQL database sablonminták található [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

[!code-json[create-azure-sql-database-server-and-database](~/resourcemanager-templates/SQLServerAndDatabase/azuredeploy.json)]

1. Válassza ki **kipróbálás** az az alábbi PowerShell-kódblokkot az Azure Cloud Shell megnyitásához.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
    $adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
    $adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

    $resourceGroupName = "${projectName}rg"


    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" -projectName $projectName -adminUser $adminUser -adminPassword $adminPassword

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

1. Válassza ki **másolási** a PowerShell-parancsfájl másolása a vágólapra.
1. Kattintson a jobb gombbal a rendszerhéj ablaktáblán, és válassza **beillesztési**.

    Az adatbázis-kiszolgáló és az adatbázis létrehozása néhány percet vesz igénybe.

## <a name="query-the-database"></a>Az adatbázis lekérdezése

Az adatbázis lekérdezése, lásd: [az adatbázis lekérdezése](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg az erőforráscsoportot, adatbázis-kiszolgáló és önálló adatbázist, ha szeretne belépni a [további lépések](#next-steps). A következő lépések bemutatják, hogyan történő csatlakozásról és lekérdezésről különböző módszereket használ az adatbázis.

Azure PowerShell az erőforráscsoport törléséhez:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>További lépések

- Hozzon létre egy kiszolgálószintű tűzfalszabályt, amely az egyetlen adatbázis csatlakozhat a helyszíni vagy távoli eszközök. További információkért lásd: [hozzon létre egy kiszolgálószintű tűzfalszabályt](sql-database-server-level-firewall-rule.md).
- Miután létrehozott egy kiszolgálószintű tűzfalszabályt [csatlakozásról és lekérdezésről](sql-database-connect-query.md) számos különböző eszközöket és nyelveket használ az adatbázis.
  - [Kapcsolódás és lekérdezés az SQL Server Management Studióval](sql-database-connect-query-ssms.md)
  - [Kapcsolódás és lekérdezés az Azure Data Studióval](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Hozzon létre egy önálló adatbázis, Azure CLI-vel, tekintse meg [Azure CLI-minták](sql-database-cli-samples.md).
- Hozzon létre egy önálló adatbázis, Azure PowerShell-lel, tekintse meg [Azure PowerShell-minták](sql-database-powershell-samples.md).
