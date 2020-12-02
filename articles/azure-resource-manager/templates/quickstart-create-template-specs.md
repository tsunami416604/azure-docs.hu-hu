---
title: Sablon létrehozása és üzembe helyezése – spec
description: Megtudhatja, hogyan hozhat létre egy sablont az ARM-sablon alapján. Ezután telepítse a SPECT egy erőforráscsoporthoz az előfizetésében.
author: tfitzmac
ms.date: 12/01/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 03cf2013f1cec9722af5d7e72285d9f11d8a6bc1
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518957"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Gyors útmutató: sablon létrehozása és üzembe helyezése specifikáció (előzetes verzió)

Ebből a rövid útmutatóból megtudhatja, hogyan csomagolhat egy Azure Resource Manager sablont (ARM-sablont) egy [sablonba](template-specs.md) , és hogyan helyezheti üzembe a sablon specifikációját. A sablon specifikációja egy ARM-sablont tartalmaz, amely egy Storage-fiókot telepít.

## <a name="prerequisites"></a>Előfeltételek

Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> A sablonra vonatkozó specifikációk jelenleg előzetes verzióban érhetők el. Ha Azure PowerShell használatával szeretné használni, telepítenie kell a [5.0.0 vagy újabb verziót](/powershell/azure/install-az-ps). Ha az Azure CLI-vel szeretné használni, használja a [Version 2.14.2 vagy az újabb verziót](/cli/azure/install-azure-cli).

## <a name="create-template-spec"></a>Sablon létrehozása – spec

A sablon spec a **Microsoft. Resources/templateSpecs** nevű erőforrástípus. A sablon specifikációjának létrehozásához használhatja a Azure Portal, az Azure PowerShell, az Azure CLI vagy egy ARM-sablont. Minden lehetőségnél szüksége van egy ARM-sablonra, amely a sablon specifikációjának megfelelően van csomagolva.

A PowerShell és a CLI használatával az ARM-sablon a parancs paraméterként lesz átadva. A ARM-sablonnal a sablon specifikációja a sablonon belül, a spec-definícióban van beágyazva.

Ezek a beállítások alább láthatók.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A képernyő felső részén, az **erőforrások, szolgáltatások és dokumentumok keresése** lapon írja be a **sablon specifikációk** kifejezést, majd válassza a **sablon specifikációk** lehetőséget.
1. Válassza a **sablon létrehozása spec** elemet.
1. Válassza ki vagy adja meg a következő értékeket:

    - **Név**: adja meg a sablon specifikációjának nevét.  Például: **storageSpec**
    - **Előfizetés**: válassza ki a sablon specifikációjának létrehozásához használt Azure-előfizetést.
    - **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget, majd adjon meg egy új erőforráscsoport-nevet.  Például: **templateSpecRG**.
    - **Hely**: válasszon egy helyet az erőforráscsoportnak. Például:  **USA 2. nyugati** régiója.
    - **Verzió**: adja meg a sablon specifikációjának verzióját. Például: **1,0**, vagy **v 1.0**.

1. Válassza a **Tovább: sablon szerkesztése** lehetőséget.
1. Cserélje le a sablon tartalmát a következő JSON-ra:

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

    Ez az a sablon, amelyet a rendszer a sablon specifikációjában csomagol.
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget.
1. Kattintson a **Létrehozás** gombra.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Ha a PowerShell-lel hoz létre egy sablont, a helyi sablont is átadhatja. Másolja a következő sablont, és mentse helyileg egy **azuredeploy.js** nevű fájlba. Ez a rövid útmutató feltételezi, hogy mentett egy elérési útra **c:\Templates\azuredeploy.js** , de bármilyen elérési utat használhat.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Hozzon létre egy új erőforráscsoportot, amely tartalmazza a sablon specifikációját.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Ezután hozza létre a sablon specifikációját az erőforráscsoporthoz. Adja meg az új sablont a **storageSpec** név alapján.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

1. Ha a CLI-vel hoz létre egy sablont, akkor átadhat egy helyi sablont. Másolja a következő sablont, és mentse helyileg egy **azuredeploy.js** nevű fájlba. Ez a rövid útmutató feltételezi, hogy mentett egy elérési útra **c:\Templates\azuredeploy.js** , de bármilyen elérési utat használhat.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Hozzon létre egy új erőforráscsoportot, amely tartalmazza a sablon specifikációját.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Ezután hozza létre a sablon specifikációját az erőforráscsoporthoz. Adja meg az új sablont a **storageSpec** név alapján.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="arm-template"></a>[ARM-sablon](#tab/azure-resource-manager)

1. Amikor ARM-sablont használ a sablon specifikációjának létrehozásához, a sablon be van ágyazva az erőforrás-definícióba. Másolja a következő sablont, és mentse helyileg **azuredeploy.jsként**. Ez a rövid útmutató feltételezi, hogy mentett egy elérési útra **c:\Templates\azuredeploy.js** , de bármilyen elérési utat használhat.

    > [!NOTE]
    > A beágyazott sablonban az összes [sablon kifejezését](template-expressions.md) egy második bal oldali szögletes zárójeltel kell megszökni. `"[[`A helyett használja `"[` . A JSON-tömbök továbbra is egyetlen bal oldali szögletes zárójelet használnak.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
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
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Hozzon létre egy új erőforráscsoportot az Azure CLI vagy a PowerShell használatával.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. A sablon üzembe helyezése az Azure CLI-vel vagy a PowerShell-lel.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>Sablon üzembe helyezése – spec

Most már üzembe helyezheti a sablon specifikációját. A sablon specifikációjának telepítése ugyanúgy történik, mint a benne található sablon üzembe helyezése, azzal a kivétellel, hogy az Azure PowerShell vagy az Azure CLI-ben a sablonhoz tartozó specifikáció erőforrás-AZONOSÍTÓját adja meg. Ugyanazokat az üzembe helyezési parancsokat használja, és szükség esetén adja át a paraméter értékét a sablon specifikációjának.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure Portal nyissa meg az utolsó eljárásban létrehozott erőforráscsoportot.  Például **templateSpecRG**.
1. Válassza ki a létrehozott specifikációt. Például: **storageSpec**.
1. Válassza az **Üzembe helyezés** lehetőséget.
1. Válassza ki vagy adja meg a következő értékeket:

    - **Előfizetés**: válasszon ki egy Azure-előfizetést az erőforrás létrehozásához.
    - **Erőforráscsoport**: válassza az **új létrehozása** elemet, majd adja meg a **storageRG**.
    - **Storage-fiók típusa**: válassza a **Standard_GRS** lehetőséget.

    Hozzon létre egy új erőforráscsoportot, és telepítse a sablont a sablon specifikációjában az új erőforráscsoporthoz.

1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Hozzon létre egy erőforráscsoportot, amely tartalmazza az új Storage-fiókot.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Szerezze be a sablonhoz tartozó specifikáció erőforrás-AZONOSÍTÓját.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. Telepítse a sablon specifikációját.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. A paramétereket pontosan úgy adja meg, mint egy ARM-sablonhoz. Telepítse újra a sablon specifikációját a Storage-fiók típusának megfelelő paraméterrel.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

1. Hozzon létre egy erőforráscsoportot, amely tartalmazza az új Storage-fiókot.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Szerezze be a sablonhoz tartozó specifikáció erőforrás-AZONOSÍTÓját.

    ```azurecli
    id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > Ismert probléma a sablon spec AZONOSÍTÓjának beszerzése és a Windows PowerShellben lévő változóhoz rendelése.

1. Telepítse a sablon specifikációját.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. A paramétereket pontosan úgy adja meg, mint egy ARM-sablonhoz. Telepítse újra a sablon specifikációját a Storage-fiók típusának megfelelő paraméterrel.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="arm-template"></a>[ARM-sablon](#tab/azure-resource-manager)

1. Másolja a következő sablont, és mentse helyileg egy **storage.js** nevű fájlba.

    ```json
       {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Hozzon létre egy új erőforráscsoportot a Storage-fiókhoz az Azure CLI vagy a PowerShell használatával.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. A sablon üzembe helyezése az Azure CLI-vel vagy a PowerShell-lel.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>Hozzáférés biztosítása

Ha szeretné, hogy a szervezet többi felhasználója is telepítse a sablon specifikációját, olvasási hozzáférést kell adnia nekik. Az olvasó szerepkört hozzárendelheti egy Azure AD-csoporthoz azon erőforráscsoport esetében, amely a megosztani kívánt sablon-specifikációkat tartalmazza. További információ: [oktatóanyag: csoport hozzáférésének biztosítása az Azure-erőforrásokhoz Azure PowerShell használatával](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a rövid útmutatóban üzembe helyezett erőforrás törléséhez törölje mindkét létrehozott erőforráscsoportot.

1. Az Azure Portalon válassza az Erőforráscsoport lehetőséget a bal oldali menüben.

1. Adja meg az erőforráscsoport nevét (templateSpecRG és storageRG) a szűrés név szerint mezőben.

1. Válassza ki az erőforráscsoport nevét.

1. A felső menüben válassza az Erőforráscsoport törlése lehetőséget.

## <a name="next-steps"></a>További lépések

Ha szeretne többet megtudni arról, hogyan hozható létre csatolt sablonokat tartalmazó sablon-specifikáció, tekintse meg a [csatolt sablon specifikációjának létrehozása](template-specs-create-linked.md)című témakört.
