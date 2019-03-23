---
title: 'Az Azure Resource Manager: Hozzon létre egy önálló adatbázis – Azure SQL Database |} A Microsoft Docs'
description: Önálló adatbázis létrehozása az Azure SQL Database az Azure Resource Manager-sablon használatával.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: 01e14f86b16db6d998d60e74211ae5ad77381461
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373020"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Gyors útmutató: Önálló adatbázis létrehozása az Azure SQL Database az Azure Resource Manager-sablon használatával

Létrehozás egy [önálló adatbázis](sql-database-single-database.md) a leggyorsabb és legegyszerűbb üzembehelyezési megoldás az Azure SQL Database-adatbázis létrehozása. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy önálló adatbázis az Azure Resource Manager-sablon használatával. További információkért lásd: [Azure Resource Manager dokumentációjában](/azure/azure-resource-manager/).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Önálló adatbázis létrehozása

Önálló adatbázis az egyik két számítási, memória, IO és tárolási erőforrások egy meghatározott készletével rendelkezik [vásárlási modellek](sql-database-purchase-models.md). Ha létrehoz egy adatbázist, akkor is definiálni egy [SQL Database-kiszolgáló](sql-database-servers.md) felügyelni, és helyezze belül [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) egy meghatározott régióban.

A rendszer a rövid útmutatóban használt sablon [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/201-sql-threat-detection-server-policy-optional-db/). A következő JSON-fájlt az ebben a cikkben használt sablon. Több Azure SQL database sablonminták található [Itt](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

[!code-json[create-azure-sql-database](~/quickstart-templates/201-sql-threat-detection-server-policy-optional-db/azuredeploy.json)]

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-threat-detection-server-policy-optional-db%2Fazuredeploy.json"><img src="./media/sql-database-single-database-get-started-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Válassza ki vagy adja meg a következő értékeket.  

    ![Resource Manager-sablon létrehozása az azure sql database](./media/sql-database-single-database-get-started-template/create-azure-sql-database-resource-manager-template.png)

    Ha a célgyűjtemény meg van adva, használja az alapértelmezett értékeket.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válasszon **új létrehozása**, adjon meg egy egyedi nevet az erőforráscsoport, és kattintson **OK**. 
    * **Hely**: válasszon ki egy helyet.  Ha például **USA középső RÉGIÓJA**.
    * **Rendszergazdai felhasználó**: Adjon meg egy SQL database server rendszergazdai jogosultságú felhasználónevet.
    * **Rendszergazdai jelszó**: Adjon meg egy rendszergazdai jelszót. 
    * **Elfogadom a fenti feltételek és kikötések állapot**: Kiválasztás.
3. Válassza a **Beszerzés** lehetőséget.

## <a name="query-the-database"></a>Az adatbázis lekérdezése

Az adatbázis lekérdezése, lásd: [az adatbázis lekérdezése](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg az erőforráscsoportot, adatbázis-kiszolgáló és önálló adatbázist, ha szeretne belépni a [további lépések](#next-steps). A következő lépések bemutatják, hogyan történő csatlakozásról és lekérdezésről különböző módszereket használ az adatbázis.

Az erőforráscsoport törlése az Azure parancssori felület vagy az Azure Powershell használatával:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>További lépések

- Hozzon létre egy kiszolgálószintű tűzfalszabályt, amely az egyetlen adatbázis csatlakozhat a helyszíni vagy távoli eszközök. További információkért lásd: [hozzon létre egy kiszolgálószintű tűzfalszabályt](sql-database-server-level-firewall-rule.md).
- Miután létrehozott egy kiszolgálószintű tűzfalszabályt [csatlakozásról és lekérdezésről](sql-database-connect-query.md) számos különböző eszközöket és nyelveket használ az adatbázis.
  - [Kapcsolódás és lekérdezés az SQL Server Management Studióval](sql-database-connect-query-ssms.md)
  - [Kapcsolódás és lekérdezés az Azure Data Studióval](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Azure CLI-vel egy önálló adatbázisok létrehozásához lásd: [Azure CLI-minták](sql-database-cli-samples.md).
- Azure PowerShell-lel egy önálló adatbázisok létrehozásához lásd: [Azure PowerShell-minták](sql-database-powershell-samples.md).
