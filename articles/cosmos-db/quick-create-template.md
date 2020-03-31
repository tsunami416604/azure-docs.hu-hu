---
title: Rövid útmutató – Hozzon létre egy Azure Cosmos DB-t és egy tárolót az Azure Resource Manager-sablon használatával
description: Gyorsútmutató, amely bemutatja, hogyan lehet egy Azure Cosmos-adatbázist és egy tárolót az Azure Resource Manager-sablon használatával
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 02/27/2020
ms.custom: subject-armqs
ms.openlocfilehash: 7c02cdf772b06f7070071aa7ba35c59b019187cc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78301726"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-azure-resource-manager-template"></a>Rövid útmutató: Hozzon létre egy Azure Cosmos DB-t és egy tárolót az Azure Resource Manager-sablon használatával

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Az Azure Cosmos DB segítségével gyorsan hozhat létre és kérdezhet kulcs-érték adatbázisokat, dokumentum-adatbázisokat és gráfadatbázisokat. Ez a rövid útmutató a Resource Manager-sablon üzembe helyezésének folyamatára összpontosít egy Azure Cosmos-adatbázis és egy tároló létrehozása az adatbázison belül. Ebben a tárolóban később is tárolhat adatokat.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Azure-előfizetés vagy ingyenes Azure Cosmos DB próbafiók

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-an-azure-cosmos-account-database-container"></a>Hozzon létre egy Azure Cosmos-fiókot, adatbázist, tárolót

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik.](https://azure.microsoft.com/resources/templates/101-cosmosdb-create/)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-create/azuredeploy.json":::

A sablon három Azure-erőforrást határoz meg:

* [Microsoft.DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): Hozzon létre egy Azure Cosmos-fiókot.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): Hozzon létre egy Azure Cosmos-adatbázist.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers:](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers)Hozzon létre egy Azure Cosmos-tárolót.

További Azure Cosmos DB-sablonminták a [rövid útmutató sablongyűjteményében](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb)találhatók.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Azure Cosmos-fiókot, egy adatbázist és egy tárolót.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Válassza ki vagy adja meg a következő értékeket.

   ![Resource Manager-sablon, Az Azure Cosmos DB integrációja, portál üzembe helyezése](./media/quick-create-template/create-cosmosdb-using-template-portal.png)

    Ha nincs megadva, használja az alapértelmezett értékeket az Azure Cosmos-erőforrások létrehozásához.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: Válassza **az Új létrehozása**lehetőséget, adja meg az erőforráscsoport egyedi nevét, majd kattintson az **OK**gombra.
    * **Hely**: válasszon ki egy helyet.  Például **az USA középső régiója**.
    * **Fióknév:** adjon meg egy nevet az Azure Cosmos-fióknak. Globálisan egyedinek kell lennie.
    * **Hely:** adja meg azt a helyet, ahol létre szeretné hozni az Azure Cosmos-fiókot. Az Azure Cosmos-fiók lehet ugyanazon a helyen, mint az erőforráscsoport.
    * **Elsődleges régió:** Az Azure Cosmos-fiók elsődleges replikarégió.
    * **Másodlagos régió:** Az Azure Cosmos-fiók másodlagos replikarégiója.
    * **Adatbázis neve:** Az Azure Cosmos-adatbázis neve.
    * **Tároló neve:** Az Azure Cosmos-tároló neve.
    * **Átviteli:** A tároló átviteli értéke, a minimális átviteli érték 400 RU/s.
    * **Elfogadom a fenti feltételeket és kikötéseket**: Válassza ki.

3. Válassza a **Beszerzés** lehetőséget. Az Azure Cosmos-fiók sikeres üzembe helyezése után értesítést kap:

   ![Erőforrás-kezelő sablon, Cosmos DB-integráció, portálértesítés üzembe helyezése](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Az Azure Portal a sablon üzembe helyezéséhez használható. Az Azure Portalon kívül használhatja az Azure PowerShellt, az Azure CLI-t és a REST API-t is. További telepítési módszerekről a [Sablonok telepítése ..](../azure-resource-manager/templates/deploy-powershell.md)

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Az Azure Portalon ellenőrizheti az Azure Cosmos-fiókot, az adatbázist és a tárolót, vagy használhatja a következő Azure CLI- vagy Azure PowerShell-parancsfájlt a létrehozott titkos kulcs listázásához.

# <a name="cli"></a>[parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[Powershell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy továbbra is dolgozni a későbbi és oktató, érdemes hagyni ezeket az erőforrásokat a helyén.
Ha már nincs szükség, törölje az erőforráscsoportot, amely törli az Azure Cosmos-fiókot és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése az Azure CLI vagy az Azure Powershell használatával:

# <a name="cli"></a>[parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[Powershell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Cosmos-fiókot, egy adatbázist és egy tárolót egy Azure Resource Manager-sablon használatával, és érvényesítette a központi telepítést. Ha többet szeretne megtudni az Azure Cosmos DB-ről és az Azure Resource Managerről, folytassa az alábbi cikkekkel.

- Az [Azure Cosmos DB áttekintésének elolvasása](introduction.md)
- További információ az [Azure Resource Managerről](../azure-resource-manager/management/overview.md)
- Más [Azure Cosmos DB Resource Manager-sablonok beszereznie](resource-manager-samples.md)