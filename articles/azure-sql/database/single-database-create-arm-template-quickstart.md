---
title: 'Azure Resource Manager: egyetlen adatbázis létrehozása'
description: Hozzon létre egyetlen adatbázist a Azure SQL Database egy Azure Resource Manager sablon használatával.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: sstein
ms.date: 06/24/2020
ms.openlocfilehash: d53f854bc3513a97398a92ee16c34a0a74650687
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91614053"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-an-arm-template"></a>Rövid útmutató: önálló adatbázis létrehozása Azure SQL Database ARM-sablon használatával

[Egyetlen adatbázis](single-database-overview.md) létrehozása a leggyorsabb és legegyszerűbb lehetőség egy adatbázis létrehozásához Azure SQL Databaseban. Ez a rövid útmutató bemutatja, hogyan hozhat létre egyetlen adatbázist egy Azure Resource Manager sablonnal (ARM-sablon).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-database%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>A sablon áttekintése

Egyetlen adatbázis a számítási, a memória-, az IO-és a tárolási erőforrások meghatározott készletével rendelkezik, és a két [vásárlási modell](purchasing-models.md)egyikét használja. Egyetlen adatbázis létrehozásakor meg kell adnia egy [kiszolgálót](logical-servers.md) is a kezeléséhez, és egy adott régióban lévő [Azure-erőforráscsoporthoz](../../active-directory-b2c/overview.md) helyezheti azt.

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-sql-database/) közül származik.

:::code language="json" source="~/quickstart-templates/101-sql-database/azuredeploy.json":::

Ezek az erőforrások a sablonban vannak definiálva:

- [**Microsoft. SQL/kiszolgálók**](/azure/templates/microsoft.sql/servers)
- [**Microsoft. SQL/kiszolgálók/adatbázisok**](/azure/templates/microsoft.sql/servers/databases)

További Azure SQL Database-sablonok találhatók az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A Azure Cloud Shell megnyitásához válassza a **kipróbálás** a következő PowerShell-kódból elemet.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-database/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Az adatbázis lekérdezéséhez tekintse meg [az adatbázis lekérdezése](single-database-create-quickstart.md#query-the-database)című témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg ezt az erőforráscsoportot, kiszolgálót és önálló adatbázist, ha a [következő lépésekre](#next-steps)szeretne lépni. A következő lépések bemutatják, hogyan csatlakozhat az adatbázishoz, és hogyan kérdezheti le azokat különböző módszerekkel.

Az erőforráscsoport törlése:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>További lépések

- Hozzon létre egy kiszolgálói szintű tűzfalszabály, amely a helyszíni vagy távoli eszközökről csatlakozik az önálló adatbázishoz. További információ: [kiszolgálói szintű tűzfalszabály létrehozása](firewall-create-server-level-portal-quickstart.md).
- A kiszolgálói szintű tűzfalszabály létrehozása után több különböző eszköz és nyelv használatával kapcsolódhat az adatbázishoz, [és lekérdezheti](connect-query-content-reference-guide.md) azt.
  - [Kapcsolódás és lekérdezés az SQL Server Management Studióval](connect-query-ssms.md)
  - [Kapcsolódás és lekérdezés az Azure Data Studióval](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Ha egyetlen adatbázist szeretne létrehozni az Azure CLI használatával, tekintse meg az [Azure CLI-minták](az-cli-script-samples-content-guide.md)című témakört.
- Ha Azure PowerShell használatával szeretne önálló adatbázist létrehozni, tekintse meg a [Azure PowerShell mintákat](powershell-script-content-guide.md).
- Az ARM-sablonok létrehozásával kapcsolatos információkért lásd: [az első sablon létrehozása](../../azure-resource-manager/templates/template-tutorial-create-first-template.md).
