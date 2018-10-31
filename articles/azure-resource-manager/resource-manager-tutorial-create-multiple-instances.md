---
title: Több erőforráspéldány létrehozása az Azure Resource Manager használatával | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre több Azure-erőforráspéldány létrehozására alkalmas Azure Resource Manager-sablont.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/18/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1cfccdf644b1748a96f7638e574c66eace8d113a
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456660"
---
# <a name="tutorial-create-multiple-resource-instances-using-resource-manager-templates"></a>Oktatóanyag: Több erőforráspéldány létrehozása Resource Manager-sablonok használatával

Ismerje meg, hogyan hozhat létre több Azure-erőforráspéldányt az Azure Resource Manager-sablonban való iterálással. Az előző oktatóanyagban egy meglévő sablon módosításával létrehozott egy titkosított Azure Storage-fiókot. Ebben az oktatóanyagban ugyanazon sablon módosításával három tárfiókpéldányt hozhat létre.

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon szerkesztése
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/) [Resource Manager Tools bővítménnyel](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az ebben a rövid útmutatóban használt sablon [standard szintű tárfiók létrehozása](https://azure.microsoft.com/resources/templates/101-storage-account-create/) néven található meg. A sablon egy Azure Storage-fiókhoz tartozó erőforrást határoz meg.

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. A **File** (Fájl) > **Save as** (Mentés másként) lehetőség kiválasztásával mentheti a fájlt a helyi számítógépre, **azuredeploy.json** néven.

## <a name="edit-the-template"></a>A sablon szerkesztése

Az oktatóanyag célja, hogy erőforrás-iterálással létrehozzon három tárfiókot.  A mintasablon csak egy tárfiókot hoz létre. 

A Visual Studio Code-ban végezze el az alábbi négy módosítást:

![Azure Resource Manager – több példány létrehozása](./media/resource-manager-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Adjon hozzá egy `copy` elemet a tárfiók erőforrás-definíciójához. A másolási elemben adja meg a ciklus nevét és iterációinak számát. Az értéknek egy 801-nél kisebb pozitív egész számnak kell lennie.
2. A `copyIndex()` függvény a ciklus aktuális iterációját adja vissza. A `copyIndex()` nulla alapú. Az indexérték eltolásához megadhat egy értéket a copyIndex() függvényben. Például *copyIndex(1)*.
3. Törölje a **változók** elemet, mivel már nincs rá szükség.
4. Törölje a **kimenetek** elemet.

A befejezett sablon ehhez hasonlóan néz ki:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

Több példány létrehozásával kapcsolatban további információt az [Erőforrás vagy tulajdonság több példányának üzembe helyezése az Azure Resource Manager sablonjaival](./resource-group-create-multiple.md) című témakörben találhat

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A Visual Studio Code üzembehelyezési eljárásról szóló rövid útmutatójában tekintse meg [A sablon üzembe helyezése](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) című szakaszt.

Ha mindhárom tárfiókot listázni szeretné, hagyja ki a --name paramétert:

# <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)
```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName
```

---

Hasonlítsa össze a tárfiókok neveit a sablon névdefiníciójával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan hozhat létre több tárfiókpéldány. Eddig egy tárfiókot vagy több tárfiókpéldányt hozott létre. A következő oktatóanyagban egy több erőforrást és több erőforrástípust tartalmazó sablont fog fejleszteni. Egyes erőforrások függő erőforrásokkal rendelkeznek.

> [!div class="nextstepaction"]
> [Függő erőforrások létrehozása](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
