---
title: Sablon létrehozása a csatolt sablonokkal
description: Útmutató sablon létrehozásához csatolt sablonokkal.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: c2648cb8a71be709406f314d02a226ed097be6f0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096683"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Oktatóanyag: spec sablon létrehozása csatolt sablonokkal (előzetes verzió)

Megtudhatja, hogyan hozhat létre egy [sablon-specifikációt](template-specs.md) [csatolt sablonnal](linked-templates.md#linked-template). A sablon specifikációi segítségével megoszthatja az ARM-sablonokat a szervezet más felhasználóival. Ebből a cikkből megtudhatja, hogyan hozhat létre egy sablon-specifikációt a fősablon és a hozzá társított sablonok a `relativePath` [telepítési erőforrás](/azure/templates/microsoft.resources/deployments)új tulajdonságának használatával történő előkészítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> A sablonra vonatkozó specifikációk jelenleg előzetes verzióban érhetők el. A használatához regisztrálnia kell [az előzetes](https://aka.ms/templateSpecOnboarding)verzióra.

## <a name="create-linked-templates"></a>Hivatkozott sablonok létrehozása

Hozza létre a fő sablont és a csatolt sablont.

Sablon csatolásához vegyen fel egy [központi telepítési erőforrást](/azure/templates/microsoft.resources/deployments) a fő sablonba. A `templateLink` tulajdonságban a fölérendelt sablon elérési útjának megfelelően határozza meg a csatolt sablon relatív elérési útját.

A csatolt sablon neve **linkedTemplate.json**, és a fő sablon tárolási útvonalán található **összetevők nevű** almappában tárolódik.  A következő értékek egyikét használhatja a relativePath:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

Ha a (z) és a (z) linkedTemplate.json és a linkedTemplate2.json egy másik linkedTemplate2.jstalálható, akkor a (z) linkedTemplate.json megadott relativePath **linkedTemplate2.js**.

1. Hozza létre a fő sablont a következő JSON-val. Mentse a fősablont **azuredeploy.jsa** helyi számítógépre. Ez az oktatóanyag feltételezi, hogy mentett egy elérési útra **c:\Templates\linkedTS\azuredeploy.js** , de bármilyen elérési utat használhat.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
          }
        }
      },
      "variables": {
        "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Web/serverfarms",
          "apiVersion": "2016-09-01",
          "name": "[variables('appServicePlanName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "B1",
            "tier": "Basic",
            "size": "B1",
            "family": "B",
            "capacity": 1
          },
          "kind": "linux",
          "properties": {
            "perSiteScaling": false,
            "reserved": true,
            "targetWorkerCount": 0,
            "targetWorkerSizeId": 0
          }
        },
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > A apiVersion `Microsoft.Resources/deployments` 2020-06-01-es vagy újabb verziójúnak kell lennie.

1. Hozzon létre egy **összetevők nevű könyvtárat** abban a mappában, amelyben a fő sablont mentette.
1. Hozza létre a csatolt sablont a következő JSON-val:

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
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. Mentse a sablont **linkedTemplate.jsként** az **összetevők mappában.**

## <a name="create-template-spec"></a>Sablon létrehozása – spec

A sablonok specifikációi az erőforráscsoportok szerint vannak tárolva.  Hozzon létre egy erőforráscsoportot, majd hozzon létre egy sablont a következő parancsfájllal. A sablon specifikációjának neve **Webspect**.

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -ResourceGroupName templateSpecRG `
  -Name webSpec `
  -Version "1.0.0.0" `
  -Location westus2 `
  -TemplateJsonFile "c:\Templates\linkedTS\azuredeploy.json"
```

Ha elkészült, megtekintheti a sablon specifikációját a Azure Portal vagy a következő parancsmag használatával:

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

## <a name="deploy-template-spec"></a>Sablon üzembe helyezése – spec

Most már üzembe helyezheti a sablon specifikációját. a sablon specifikációjának központi telepítése ugyanúgy történik, mint a benne található sablon üzembe helyezése, kivéve, ha a sablonhoz tartozó specifikáció erőforrás-AZONOSÍTÓját adja meg. Ugyanazokat az üzembe helyezési parancsokat használja, és szükség esetén adja át a paraméter értékét a sablon specifikációjának.

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

## <a name="next-steps"></a>További lépések

Ha szeretne többet megtudni a sablon specifikációjának csatolt sablonként való üzembe helyezéséről, tekintse meg a következő [oktatóanyagot: a sablon specifikációjának telepítése csatolt sablonként](template-specs-deploy-linked-template.md).
