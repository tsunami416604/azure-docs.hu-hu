---
title: Erőforrások telepítése több előfizetés & erőforráscsoporthoz
description: Bemutatja, hogyan célozhat meg több Azure-előfizetést és erőforráscsoportot az üzembe helyezés során.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 8f5fbd51456003059f6a32fc32b32194a936434a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154210"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Azure-erőforrások üzembe helyezése több előfizetéshez vagy erőforráscsoporthoz

A sablonban lévő összes erőforrást általában egyetlen [erőforráscsoporthoz](../management/overview.md)kell telepíteni. Vannak azonban olyan forgatókönyvek, amelyekben különböző erőforrás-készleteket kíván üzembe helyezni, de más erőforráscsoportokbe vagy előfizetésbe helyezi őket. Előfordulhat például, hogy a Azure Site Recovery biztonsági mentési virtuális gépet külön erőforráscsoport és hely számára szeretné telepíteni. A Resource Manager lehetővé teszi beágyazott sablonok használatát több előfizetés és erőforráscsoport megcélzásához.

> [!NOTE]
> Egyetlen üzemelő példányban csak öt erőforráscsoport helyezhető üzembe. Ez a korlátozás általában azt jelenti, hogy egy, a szülő sablonhoz megadott erőforráscsoport, valamint a beágyazott vagy csatolt központi telepítések legfeljebb négy erőforráscsoport számára telepíthető. Ha azonban a fölérendelt sablon csak beágyazott vagy csatolt sablonokat tartalmaz, és nem helyezi üzembe az erőforrásokat, akkor akár öt erőforráscsoportot is tartalmazhat beágyazott vagy csatolt központi telepítések esetén.

## <a name="specify-subscription-and-resource-group"></a>Előfizetés és erőforráscsoport meghatározása

Ha másik erőforráscsoportot vagy előfizetést szeretne megcélozni, használjon [beágyazott vagy csatolt sablont](linked-templates.md). A `Microsoft.Resources/deployments` erőforrástípus `subscriptionId` és `resourceGroup`paramétereket tartalmaz, amelyek lehetővé teszik az előfizetés és az erőforráscsoport megadását a beágyazott telepítéshez. Ha nem határozza meg az előfizetés-azonosítót vagy az erőforráscsoportot, a rendszer az előfizetést és az erőforráscsoportot használja a fölérendelt sablonból. Az összes erőforráscsoport léteznie kell az üzemelő példány futtatása előtt.

A sablon telepítéséhez használt fióknak engedéllyel kell rendelkeznie a megadott előfizetés-AZONOSÍTÓhoz való központi telepítéshez. Ha a megadott előfizetés egy másik Azure Active Directory-bérlőn létezik, a [vendég felhasználókat hozzá kell adnia egy másik címtárból](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Másik erőforráscsoport és előfizetés megadásához használja a következőt:

```json
"resources": [
  {
    "apiVersion": "2017-05-10",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
    "resourceGroup": "[parameters('secondResourceGroup')]",
    "subscriptionId": "[parameters('secondSubscriptionID')]",
    ...
  }
]
```

Ha az erőforráscsoportok ugyanahhoz az előfizetéshez tartoznak, akkor eltávolíthatja a **subscriptionId** értéket.

A következő példa két Storage-fiókot telepít. Az első Storage-fiókot a rendszer az üzembe helyezés során megadott erőforráscsoporthoz telepíti. A második Storage-fiók a `secondResourceGroup` és `secondSubscriptionID` paraméterekben megadott erőforráscsoporthoz van telepítve:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    },
    "secondResourceGroup": {
      "type": "string"
    },
    "secondSubscriptionID": {
      "type": "string",
      "defaultValue": ""
    },
    "secondStorageLocation": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
    "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2017-06-01",
      "name": "[variables('firstStorageName')]",
      "location": "[resourceGroup().location]",
      "sku":{
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "nestedTemplate",
      "resourceGroup": "[parameters('secondResourceGroup')]",
      "subscriptionId": "[parameters('secondSubscriptionID')]",
      "properties": {
      "mode": "Incremental",
      "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2017-06-01",
            "name": "[variables('secondStorageName')]",
            "location": "[parameters('secondStorageLocation')]",
            "sku":{
              "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
          }
          ]
      },
      "parameters": {}
      }
    }
  ]
}
```

Ha a `resourceGroup`t egy nem létező erőforráscsoport nevére állítja be, akkor a telepítés sikertelen lesz.

Az előző sablon teszteléséhez és az eredmények megtekintéséhez használja a PowerShell vagy az Azure CLI-t.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Ha két tárolási fiókot kíván üzembe helyezni két erőforráscsoporthoz ugyanabban az **előfizetésben**, használja a következőt:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Két Storage-fiók **két előfizetésben**való üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha két tárolási fiókot kíván üzembe helyezni két erőforráscsoporthoz ugyanabban az **előfizetésben**, használja a következőt:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Két Storage-fiók **két előfizetésben**való üzembe helyezéséhez használja a következőt:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Függvények használata

A [resourceGroup ()](template-functions-resource.md#resourcegroup) és az [előfizetés ()](template-functions-resource.md#subscription) függvények a sablon megadásának módjától függően eltérő módon oldhatók fel. Ha külső sablonra hivatkozik, a függvények mindig a sablon hatókörére lesznek feloldva. Ha egy sablonon belül ágyaz be egy sablont, a `expressionEvaluationOptions` tulajdonsággal adhatja meg, hogy a függvények feloldhatók-e az erőforráscsoporthoz és a fölérendelt sablonra vagy a beágyazott sablonra vonatkozó előfizetésre. Állítsa a tulajdonságot `inner`re a beágyazott sablon hatókörének feloldásához. Állítsa a tulajdonságot úgy, hogy `outer`, hogy feloldja a fölérendelt sablon hatókörét.

Az alábbi táblázat azt mutatja, hogy a függvények feloldhatók-e a szülő vagy a beágyazott erőforráscsoport és előfizetés esetében.

| Sablon típusa | Hatókör | Felbontás |
| ------------- | ----- | ---------- |
| beágyazott        | külső (alapértelmezett) | Szülő erőforráscsoport |
| beágyazott        | belső | Alerőforrás-csoport |
| csatolt        | –   | Alerőforrás-csoport |

A következő [példában a sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) látható:

* beágyazott sablon alapértelmezett (külső) hatókörrel
* beágyazott sablon belső hatókörrel
* Csatolt sablon

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "defaultScopeTemplate",
      "resourceGroup": "inlineGroup",
      "properties": {
      "mode": "Incremental",
      "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
          ],
          "outputs": {
          "resourceGroupOutput": {
            "type": "string",
            "value": "[resourceGroup().name]"
          }
          }
      },
      "parameters": {}
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "innerScopeTemplate",
      "resourceGroup": "inlineGroup",
      "properties": {
      "expressionEvaluationOptions": {
          "scope": "inner"
      },
      "mode": "Incremental",
      "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
          ],
          "outputs": {
          "resourceGroupOutput": {
            "type": "string",
            "value": "[resourceGroup().name]"
          }
          }
      },
      "parameters": {}
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "resourceGroup": "linkedGroup",
      "properties": {
      "mode": "Incremental",
      "templateLink": {
          "contentVersion": "1.0.0.0",
          "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/resourceGroupName.json"
      },
      "parameters": {}
      }
    }
  ],
  "outputs": {
    "parentRG": {
      "type": "string",
      "value": "[concat('Parent resource group is ', resourceGroup().name)]"
    },
    "defaultScopeRG": {
      "type": "string",
      "value": "[concat('Default scope resource group is ', reference('defaultScopeTemplate').outputs.resourceGroupOutput.value)]"
    },
    "innerScopeRG": {
      "type": "string",
      "value": "[concat('Inner scope resource group is ', reference('innerScopeTemplate').outputs.resourceGroupOutput.value)]"
    },
    "linkedRG": {
      "type": "string",
      "value": "[concat('Linked resource group is ', reference('linkedTemplate').outputs.resourceGroupOutput.value)]"
    }
  }
}
```

Az előző sablon teszteléséhez és az eredmények megtekintéséhez használja a PowerShell vagy az Azure CLI-t.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Az előző példa kimenete a következő:

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Az előző példa kimenete a következő:

```azurecli
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Következő lépések

* Ha szeretné megtudni, hogyan határozhat meg paramétereket a sablonban, olvassa el [a Azure Resource Manager sablonok struktúrájának és szintaxisának megismerését](template-syntax.md)ismertető témakört.
* A gyakori telepítési hibák megoldásával kapcsolatos tippekért lásd: [gyakori Azure-telepítési hibák elhárítása Azure Resource Managerokkal](common-deployment-errors.md).
* A SAS-tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [privát sablon üzembe helyezése sas-tokenrel](secure-template-with-sas-token.md).
