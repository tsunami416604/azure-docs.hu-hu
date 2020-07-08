---
title: Több erőforráspéldány létrehozása
description: Ismerje meg, hogyan hozhat létre több Azure-erőforráspéldány létrehozására alkalmas Azure Resource Manager-sablont.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0a6f9e723fb6374f5ac72241501f49a94c2c2fe2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86101953"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>Oktatóanyag: több erőforrás-példány létrehozása ARM-sablonokkal

Megtudhatja, hogyan lehet megismételni a Azure Resource Manager (ARM-) sablonban egy Azure-erőforrás több példányának létrehozását. Ebben az oktatóanyagban egy sablon módosításával három tárfiókpéldányt hoz létre.

![Azure Resource Manager több példány diagramot hoz létre](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon szerkesztése
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio CodeResource Manager Tools bővítménnyel. Tekintse meg a rövid útmutató [: Azure Resource Manager sablonok létrehozása Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)-ban című témakört.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az [Azure Gyorsindítás sablonjai](https://azure.microsoft.com/resources/templates/) az ARM-sablonok tárháza. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. Az ebben a rövid útmutatóban használt sablon neve a következő: [Standard szintű tárfiók létrehozása](https://azure.microsoft.com/resources/templates/101-storage-account-create/). A sablon egy Azure Storage-fiókhoz tartozó erőforrást határoz meg.

1. A Visual Studio Code-ból válassza a **fájl** > **megnyitott**fájl elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. A sablonban definiálva van egy Microsoft.Storage/storageAccounts nevű erőforrás. Hasonlítsa össze a sablont a [sablonreferenciával](/azure/templates/Microsoft.Storage/storageAccounts). Érdemes megismerkedni a sablon alapvető működésével, mielőtt megkezdi annak testreszabását.
5. Válassza a **fájl** > **Mentés másként** lehetőséget, hogy a fájlt **azuredeploy.jsa** helyi számítógépre mentse.

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

További információ több példány létrehozásáról: [erőforrás vagy tulajdonság több példányának üzembe helyezése ARM-sablonokban](./copy-resources.md)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Jelentkezzen be a [Azure Cloud Shellba](https://shell.azure.com)

1. Válassza ki a kívánt környezetet a **PowerShell** vagy a **bash** (a CLI esetében) lehetőség kiválasztásával a bal felső sarokban.  A váltáskor a felületet újra kell indítani.

    ![Azure Portal Cloud Shell fájl feltöltése](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Kattintson a **Fájlok feltöltése/letöltése**, majd a **Feltöltés** elemre. Lásd az előző képernyőképet. Válassza ki a korábbi szakaszban mentett fájlt. A fájl feltöltése után az **ls** parancs és a **Cat** parancs használatával ellenőrizheti, hogy a fájl feltöltése sikeresen megtörtént-e.

1. A Cloud Shell futtassa a következő parancsokat. Válassza ki a megfelelő lapot a PowerShell-kód vagy a parancssori felület kód megjelenítéséhez.

    # <a name="cli"></a>[Parancssori felület](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

Ha mindhárom tárfiókot listázni szeretné, hagyja ki a --name paramétert:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"

Get-AzStorageAccount -ResourceGroupName $resourceGroupName
Write-Host "Press [ENTER] to continue ..."
```

---

Hasonlítsa össze a tárfiókok neveit a sablon névdefiníciójával.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Az erőforráscsoport összesen három erőforrást fog látni.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtudhatta, hogyan hozhat létre több tárfiókpéldány.  A következő oktatóanyagban egy több erőforrást és több erőforrástípust tartalmazó sablont fog fejleszteni. Egyes erőforrások függő erőforrásokkal rendelkeznek.

> [!div class="nextstepaction"]
> [Függő erőforrások létrehozása](./template-tutorial-create-templates-with-dependent-resources.md)
