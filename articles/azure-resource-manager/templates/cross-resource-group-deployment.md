---
title: Erőforrások üzembe helyezése előfizetések közötti & erőforráscsoportban
description: Bemutatja, hogyan célozhatja meg egynél több Azure-előfizetés t és erőforráscsoportot a telepítés során.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 70868f5a3598c26ffff81f0ad3536a6c5c0a7e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460347"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Azure-erőforrások üzembe helyezése több előfizetésre vagy erőforráscsoportra

A sablonban lévő összes erőforrást általában egyetlen [erőforráscsoportra](../management/overview.md)telepíti. Vannak azonban olyan esetek, amikor szeretné üzembe helyezni egy erőforrás-készletet együtt, de elhelyezni őket a különböző erőforráscsoportok vagy előfizetések. Például érdemes lehet telepíteni a biztonsági mentési virtuális gép az Azure Site Recovery egy külön erőforráscsoport és a hely. Az Erőforrás-kezelő lehetővé teszi, hogy beágyazott sablonok használatával egynél több előfizetést és erőforráscsoportot célozzon meg.

> [!NOTE]
> Egyetlen központi telepítésben csak öt erőforráscsoportra telepíthető. Ez a korlátozás általában azt jelenti, hogy a szülősablonhoz megadott egyetlen erőforráscsoportra és legfeljebb négy erőforráscsoportra telepíthető beágyazott vagy csatolt központi telepítésekben. Ha azonban a szülősablon csak beágyazott vagy csatolt sablonokat tartalmaz, és maga nem telepít erőforrásokat, akkor legfeljebb öt erőforráscsoportot vehet fel beágyazott vagy csatolt központi telepítésekbe.

## <a name="specify-subscription-and-resource-group"></a>Előfizetés és erőforráscsoport megadása

Ha másik erőforráscsoportot vagy előfizetést szeretne megcélozni, [használjon beágyazott vagy csatolt sablont.](linked-templates.md) Az `Microsoft.Resources/deployments` erőforrástípus paramétereket `subscriptionId` `resourceGroup`biztosít a és a alkalmazáshoz, amelyek lehetővé teszik a beágyazott központi telepítés előfizetésének és erőforráscsoportjának megadását. Ha nem adja meg az előfizetés-azonosítót vagy az erőforráscsoportot, a szülősablon előfizetési és erőforráscsoportját használja a program. A központi telepítés futtatása előtt minden erőforráscsoportnak léteznie kell.

A sablon központi telepítéséhez használt fióknak engedéllyel kell rendelkeznie a megadott előfizetés-azonosítóra való telepítéshez. Ha a megadott előfizetés létezik egy másik Azure Active Directory-bérlőben, hozzá kell [adnia vendégfelhasználókat egy másik könyvtárból.](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)

Másik erőforráscsoport és előfizetés megadásához használja a következőket:

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

Ha az erőforráscsoportok ugyanabban az előfizetésben vannak, eltávolíthatja az **előfizetési** értéket.

A következő példa két tárfiókot telepít. Az első tárfiók üzembe helyezése az üzembe helyezés során megadott erőforráscsoportba történik. A második tárfiók a és `secondResourceGroup` `secondSubscriptionID` a paraméterekben megadott erőforráscsoportra van telepítve:

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

Ha egy `resourceGroup` nem létező erőforráscsoport nevére állítabe, a központi telepítés sikertelen lesz.

Az előző sablon teszteléséhez és az eredmények megtekintéséhez használja a PowerShell vagy az Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Ha két tárfiókot szeretne telepíteni két erőforráscsoportra **ugyanabban**az előfizetésben, használja a következőket:

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

Ha két tárfiókot szeretne telepíteni két előfizetésre, használja a **következőket:**

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha két tárfiókot szeretne telepíteni két erőforráscsoportra **ugyanabban**az előfizetésben, használja a következőket:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Ha két tárfiókot szeretne telepíteni két előfizetésre, használja a **következőket:**

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Függvények használata

A [resourceGroup()](template-functions-resource.md#resourcegroup) és [az subscription()](template-functions-resource.md#subscription) függvények a sablon megadásának módjától függően eltérő módon oldódnak fel. Ha külső sablonra hivatkozik, a függvények mindig az adott sablon hatókörére oldódnak fel. Amikor egy szülősablonba beágyaz `expressionEvaluationOptions` egy sablont, a tulajdonság segítségével adja meg, hogy a függvények feloldódjanak-e az erőforráscsoportra és a szülősablon vagy a beágyazott sablon előfizetésére. Állítsa be `inner` a tulajdonságot a beágyazott sablon hatókörére való feloldáshoz. Állítsa be `outer` a tulajdonságot a szülősablon hatókörének feloldására.

Az alábbi táblázat bemutatja, hogy a függvények feloldódnak-e a szülő vagy beágyazott erőforráscsoport és előfizetés között.

| Sablon típusa | Hatókör | Megoldás: |
| ------------- | ----- | ---------- |
| Beágyazott        | külső (alapértelmezett) | Fölérendelt erőforráscsoport |
| Beágyazott        | Belső | Alerőforráscsoport |
| Kapcsolódó        | N/A   | Alerőforráscsoport |

A következő [példasablon a következőket](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) mutatja be:

* beágyazott sablon alapértelmezett (külső) hatókörrel
* beágyazott sablon belső hatókörrel
* csatolt sablon

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

Az előző sablon teszteléséhez és az eredmények megtekintéséhez használja a PowerShell vagy az Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Az előző példa kimenete a következő:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Az előző példa kimenete a következő:

```output
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

## <a name="next-steps"></a>További lépések

* Ha tudni szeretné, hogyan definiálhatja a paramétereket a sablonban, [olvassa el az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakört.](template-syntax.md)
* A gyakori telepítési hibák elhárításával kapcsolatos tippek az [Azure Resource Manager gyakori Azure-telepítési hibáinak elhárítása című témakörben](common-deployment-errors.md)olvashat.
* A SAS-jogkivonatot igénylő sablonok üzembe helyezéséről a [Privát sablon telepítése SAS-jogkivonattal](secure-template-with-sas-token.md)című témakörben olvashat.
