---
title: Rövid útmutató – Azure Cosmos DB és tároló létrehozása Azure Resource Manager sablon használatával
description: Útmutató az Azure Cosmos-adatbázisok és-tárolók Azure Resource Manager sablon használatával történő használatáról
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 02/27/2020
ms.openlocfilehash: 708fc030d953dd1f32986c600305e513a156b12f
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227425"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-azure-resource-manager-template"></a>Gyors útmutató: Azure Cosmos DB és tároló létrehozása Azure Resource Manager sablon használatával

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. A Azure Cosmos DB a kulcs/érték adatbázisok, dokumentum-adatbázisok és Graph-adatbázisok gyors létrehozásához és lekérdezéséhez használható. Ez a rövid útmutató egy Resource Manager-sablon üzembe helyezésének folyamatát, amely egy Azure Cosmos-adatbázis létrehozásához és az adatbázison belüli tárolóhoz szükséges. A tárolóban később tárolhatók az adattárolók.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Azure-előfizetés vagy ingyenes Azure Cosmos DB próbaverziós fiók

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-an-azure-cosmos-account-database-container"></a>Azure Cosmos-fiók, adatbázis, tároló létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-cosmosdb-create/)származik.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-create/azuredeploy.json":::

Három Azure-erőforrás van definiálva a sablonban:

* [Microsoft. DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): hozzon létre egy Azure Cosmos-fiókot.

* [Microsoft. DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): hozzon létre egy Azure Cosmos-adatbázist.

* [Microsoft. DocumentDB/databaseAccounts/sqlDatabases/containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): hozzon létre egy Azure Cosmos-tárolót.

További Azure Cosmos DB-sablonok a gyorsindítási [sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb)katalógusában találhatók.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Azure Cosmos-fiókot, egy adatbázist és egy tárolót.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Válassza ki vagy adja meg a következő értékeket.

   ![Resource Manager-sablon, Azure Cosmos DB integráció, portál üzembe helyezése](./media/quick-create-template/create-cosmosdb-using-template-portal.png)

    Ha meg van adva, használja az alapértelmezett értékeket az Azure Cosmos-erőforrások létrehozásához.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **új létrehozása**lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK**gombra.
    * **Hely**: válasszon ki egy helyet.  Például az **USA középső**régiója.
    * **Fiók neve**: adja meg az Azure Cosmos-fiók nevét. Globálisan egyedinek kell lennie.
    * **Hely**: adjon meg egy helyet, ahol létre szeretné hozni az Azure Cosmos-fiókot. Az Azure Cosmos-fiók ugyanazon a helyen lehet, mint az erőforráscsoport.
    * **Elsődleges régió**: az Azure Cosmos-fiók elsődleges replika régiója.
    * **Másodlagos régió**: az Azure Cosmos-fiók másodlagos replika régiója.
    * **Adatbázis neve**: az Azure Cosmos-adatbázis neve.
    * **Tároló neve**: az Azure Cosmos-tároló neve.
    * **Átviteli sebesség**: a tároló átviteli sebessége, a minimális átviteli sebesség 400 ru/s.
    * **Elfogadom a fenti feltételeket és kikötéseket**: Válassza ki.

3. Válassza a **Beszerzés** lehetőséget. Miután sikeresen telepítette az Azure Cosmos-fiókot, értesítést kap:

   ![Resource Manager-sablon, Cosmos DB integráció, portál-értesítés üzembe helyezése](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

A Azure Portal a sablon üzembe helyezéséhez használható. A Azure Portalon kívül használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A Azure Portal használatával megtekintheti az Azure Cosmos-fiókot, az adatbázist és a tárolót, vagy a következő Azure CLI-vagy Azure PowerShell-szkripttel listázhatja a létrehozott titkos kulcsot.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy az ezt követő és oktatóanyagokkal folytatja a munkát, érdemes lehet ezeket az erőforrásokat helyben hagyni.
Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az Azure Cosmos-fiókot és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése az Azure CLI vagy az Azure PowerShell használatával:

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure Cosmos-fiókot, egy adatbázist és egy tárolót egy Azure Resource Manager sablon használatával, és ellenőrizte a telepítést. Ha többet szeretne megtudni a Azure Cosmos DB és a Azure Resource Managerról, folytassa az alábbi cikkekkel.

- [A Azure Cosmos db áttekintése](introduction.md)
- További információ a [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Más [Azure Cosmos db Resource Manager-sablonok](resource-manager-samples.md) beszerzése