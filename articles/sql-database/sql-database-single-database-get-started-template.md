---
title: 'Azure Resource Manager: egyetlen adatbázis létrehozása'
description: Hozzon létre egyetlen adatbázist Azure SQL Database a Azure Resource Manager sablonnal.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: b7888215a2d463c8007168e215179ace898ca1cc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821025"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Gyors útmutató: önálló adatbázis létrehozása Azure SQL Database a Azure Resource Manager sablon használatával

[Egyetlen adatbázis](sql-database-single-database.md) létrehozása a leggyorsabb és legegyszerűbb üzembe helyezési lehetőség az adatbázisok Azure SQL Database-ben való létrehozásához. Ez a rövid útmutató bemutatja, hogyan hozhat létre egyetlen adatbázist a Azure Resource Manager sablonnal. További információ: [Azure Resource Manager dokumentáció](/azure/azure-resource-manager/).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Önálló adatbázis létrehozása

Egyetlen adatbázis a számítási, a memória-, az IO-és a tárolási erőforrások meghatározott készletével rendelkezik, és a két [vásárlási modell](sql-database-purchase-models.md)egyikét használja. Egyetlen adatbázis létrehozásakor egy [SQL Database-kiszolgálót](sql-database-servers.md) is meg kell adnia a kezeléséhez, és egy adott régióban lévő [Azure-erőforráscsoporthoz](../azure-resource-manager/resource-group-overview.md) helyezheti azt.

A következő JSON-fájl az ebben a cikkben használt sablon. A sablont a [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/SQLServerAndDatabase/azuredeploy.json)tárolja. További Azure SQL Database-sablonok az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)találhatók.

[!code-json[create-azure-sql-database-server-and-database](~/resourcemanager-templates/SQLServerAndDatabase/azuredeploy.json)]

1. A Azure Cloud Shell megnyitásához válassza a **kipróbálás** a következő PowerShell-kódból elemet.

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

1. A **Másolás** gombra kattintva másolja a PowerShell-szkriptet a vágólapra.
1. Kattintson a jobb gombbal a rendszerhéj ablaktáblára, majd válassza a **Beillesztés**lehetőséget.

    Az adatbázis-kiszolgáló és az adatbázis létrehozása néhány percet vesz igénybe.

## <a name="query-the-database"></a>Az adatbázis lekérdezése

Az adatbázis lekérdezéséhez tekintse meg [az adatbázis lekérdezése](./sql-database-single-database-get-started.md#query-the-database)című témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg ezt az erőforráscsoportot, az adatbázis-kiszolgálót és az önálló adatbázist, ha a [következő lépésekre](#next-steps)szeretne lépni. A következő lépések bemutatják, hogyan csatlakozhat az adatbázishoz, és hogyan kérdezheti le azokat különböző módszerekkel.

Az erőforráscsoport törlése az Azure PowerShell-lel:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>További lépések

- Hozzon létre egy kiszolgálói szintű tűzfalszabály, amely a helyszíni vagy távoli eszközökről csatlakozik az önálló adatbázishoz. További információ: [kiszolgálói szintű tűzfalszabály létrehozása](sql-database-server-level-firewall-rule.md).
- A kiszolgálói szintű tűzfalszabály létrehozása után több különböző eszköz és nyelv használatával kapcsolódhat az adatbázishoz, [és lekérdezheti](sql-database-connect-query.md) azt.
  - [Kapcsolódás és lekérdezés az SQL Server Management Studióval](sql-database-connect-query-ssms.md)
  - [Kapcsolódás és lekérdezés az Azure Data Studióval](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Ha egyetlen adatbázist szeretne létrehozni az Azure CLI használatával, tekintse meg az [Azure CLI-minták](sql-database-cli-samples.md)című témakört.
- Ha Azure PowerShell használatával szeretne önálló adatbázist létrehozni, tekintse meg a [Azure PowerShell mintákat](sql-database-powershell-samples.md).
