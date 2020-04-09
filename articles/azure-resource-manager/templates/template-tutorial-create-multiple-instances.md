---
title: Több erőforráspéldány létrehozása
description: Ismerje meg, hogyan hozhat létre több Azure-erőforráspéldány létrehozására alkalmas Azure Resource Manager-sablont.
author: mumian
ms.date: 04/08/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 70c86c82cb28bf767da50cca20f7c1d052d4bf01
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982538"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>Oktatóanyag: Több erőforráspéldány létrehozása ARM-sablonokkal

Megtudhatja, hogyan iterálni az Azure Resource Manager (ARM) sablon több példányt hozhat létre egy Azure-erőforrás. Ebben az oktatóanyagban egy sablon módosításával három tárfiókpéldányt hoz létre.

![Az Azure Resource Manager több példánydiagramot hoz létre](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon szerkesztése
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio CodeResource Manager Tools bővítménnyel. Lásd: [A Visual Studio-kód használata ARM-sablonok létrehozásához.](use-vs-code-to-create-template.md)

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

[Az Azure QuickStart Templates](https://azure.microsoft.com/resources/templates/) az ARM-sablonok tárháza. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. Az ebben a rövid útmutatóban használt sablon neve a következő: [Standard szintű tárfiók létrehozása](https://azure.microsoft.com/resources/templates/101-storage-account-create/). A sablon egy Azure Storage-fiókhoz tartozó erőforrást határoz meg.

1. A Visual Studio-kódból válassza a **Fájlmegnyitása**>**fájl**lehetőséget.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. A sablonban definiálva van egy Microsoft.Storage/storageAccounts nevű erőforrás. Hasonlítsa össze a sablont a [sablonreferenciával](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). Érdemes megismerkedni a sablon alapvető működésével, mielőtt megkezdi annak testreszabását.
5. Válassza a **Fájlmentés**>**másként** lehetőséget a fájl **azuredeploy.json néven** a helyi számítógépre történő mentéséhez.

## <a name="edit-the-template"></a>A sablon szerkesztése

A meglévő sablon egy tárfiókot hoz létre. Most testreszabjuk a sablont, hogy három tárfiókot hozzon létre.

A Visual Studio Code-ban végezze el az alábbi négy módosítást:

![Az Azure Resource Manager több példányt hoz létre](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Adjon hozzá egy `copy` elemet a tárfiók erőforrás-definíciójához. A másolási elemben adja meg az iterációk számát, és egy változót a ciklushoz. Az értéknek egy 801-nél kisebb pozitív egész számnak kell lennie.
2. A `copyIndex()` függvény a ciklus aktuális iterációját adja vissza. Ezt az indexet fogja a név előtagjaként használni. A `copyIndex()` nulla alapú. Az indexérték eltolásához megadhat egy értéket a copyIndex() függvényben. Például *copyIndex(1)*.
3. Törölje a **változók** elemet, mivel már nincs rá szükség.
4. Törölje a **kimenetek** elemet. A továbbiakban nincs rá szükség.

A befejezett sablon ehhez hasonlóan néz ki:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ]
}
```

Több példány létrehozásáról további információt az [Erőforrás vagy tulajdonság több példányának telepítése ARM-sablonokban című témakörben talál.](./copy-resources.md)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A Visual Studio Code üzembehelyezési eljárásról szóló rövid útmutatójában tekintse meg [A sablon üzembe helyezése](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) című szakaszt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha mindhárom tárfiókot listázni szeretné, hagyja ki a --name paramétert:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
```

---

Hasonlítsa össze a tárfiókok neveit a sablon névdefiníciójával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan hozhat létre több tárfiókpéldány.  A következő oktatóanyagban egy több erőforrást és több erőforrástípust tartalmazó sablont fog fejleszteni. Egyes erőforrások függő erőforrásokkal rendelkeznek.

> [!div class="nextstepaction"]
> [Függő erőforrások létrehozása](./template-tutorial-create-templates-with-dependent-resources.md)
