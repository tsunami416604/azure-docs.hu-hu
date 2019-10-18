---
title: Azure-erőforrások, több előfizetés & erőforráscsoport üzembe helyezése
description: Bemutatja, hogyan célozhat meg több Azure-előfizetést és erőforráscsoportot az üzembe helyezés során.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/02/2018
ms.author: tomfitz
ms.openlocfilehash: c90096043f54eb8db5834fbe83ed1d6ae710d371
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528327"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Azure-erőforrások üzembe helyezése több előfizetéshez vagy erőforráscsoporthoz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A sablonban lévő összes erőforrást általában egyetlen [erőforráscsoporthoz](resource-group-overview.md)kell telepíteni. Vannak azonban olyan forgatókönyvek, amelyekben különböző erőforrás-készleteket kíván üzembe helyezni, de más erőforráscsoportokbe vagy előfizetésbe helyezi őket. Előfordulhat például, hogy a Azure Site Recovery biztonsági mentési virtuális gépet külön erőforráscsoport és hely számára szeretné telepíteni. A Resource Manager lehetővé teszi, hogy a beágyazott sablonok használatával különböző előfizetéseket és erőforráscsoportokat célozjon meg, mint a fölérendelt sablonhoz használt előfizetés és erőforráscsoport.

> [!NOTE]
> Egyetlen üzemelő példányban csak öt erőforráscsoport helyezhető üzembe. Ez a korlátozás általában azt jelenti, hogy egy, a szülő sablonhoz megadott erőforráscsoport, valamint a beágyazott vagy csatolt központi telepítések legfeljebb négy erőforráscsoport számára telepíthető. Ha azonban a fölérendelt sablon csak beágyazott vagy csatolt sablonokat tartalmaz, és nem helyezi üzembe az erőforrásokat, akkor akár öt erőforráscsoportot is tartalmazhat beágyazott vagy csatolt központi telepítések esetén.

## <a name="specify-a-subscription-and-resource-group"></a>Előfizetés és erőforráscsoport meghatározása

Egy másik erőforrás megcélzásához használjon beágyazott vagy csatolt sablont. A `Microsoft.Resources/deployments` erőforrástípus a `subscriptionId` és a `resourceGroup` paramétereit adja meg. Ezek a tulajdonságok lehetővé teszik egy másik előfizetés és erőforráscsoport megadását a beágyazott telepítéshez. Az összes erőforráscsoport léteznie kell az üzemelő példány futtatása előtt. Ha nem ad meg az előfizetés-azonosítót vagy az erőforráscsoportot, a rendszer az előfizetést és az erőforráscsoportot használja a fölérendelt sablonból.

A sablon telepítéséhez használt fióknak engedéllyel kell rendelkeznie a megadott előfizetés-AZONOSÍTÓhoz való központi telepítéshez. Ha a megadott előfizetés egy másik Azure Active Directory-bérlőn létezik, a [vendég felhasználókat hozzá kell adnia egy másik címtárból](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

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

A következő példa két Storage-fiókot telepít – egyet az üzembe helyezés során megadott erőforráscsoporthoz, és egyet a `secondResourceGroup` paraméterben megadott erőforráscsoporthoz:

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
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
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
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
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
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

Ha a `resourceGroup`t egy nem létező erőforráscsoport nevére állítja be, akkor a telepítés sikertelen lesz.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>A resourceGroup () és az előfizetés () függvények használata

A többerőforrásos csoportok telepítésekor a [resourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) és az [előfizetés ()](resource-group-template-functions-resource.md#subscription) függvények a beágyazott sablon megadása alapján eltérő módon oldhatók meg. 

Ha egy sablont ágyaz be egy másik sablonba, a beágyazott sablonban lévő függvények a szülő erőforráscsoporthoz és az előfizetésre is feloldhatók. A beágyazott sablon a következő formátumot használja:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() and subscription() refer to parent resource group/subscription
    }
}
```

Ha egy különálló sablonra hivatkozik, a társított sablonban lévő függvények a beágyazott erőforráscsoporthoz és előfizetésre is feloldhatók. A csatolt sablonok a következő formátumot használják:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() and subscription() in linked template refer to linked resource group/subscription
    }
}
```

## <a name="example-templates"></a>Példák sablonokra

A következő sablonok több erőforráscsoport-telepítést mutatnak be. A sablonok üzembe helyezéséhez szükséges parancsfájlok a tábla után jelennek meg.

|Sablon  |Leírás  |
|---------|---------|
|[Több előfizetési sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Egyetlen Storage-fiókot telepít egy erőforráscsoporthoz és egy Storage-fiókot egy második erőforráscsoporthoz. Adja meg az előfizetés-azonosító értékét, ha a második erőforráscsoport egy másik előfizetésben található. |
|[Több erőforráscsoport tulajdonságai sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Bemutatja, hogyan oldja meg a `resourceGroup()` függvény. Nem helyez üzembe semmilyen erőforrást. |

### <a name="powershell"></a>PowerShell

Ahhoz, hogy a PowerShell két Storage-fiókot helyezzen üzembe két **ugyanabba az előfizetésbe**tartozó erőforráscsoporthoz, használja a következőt:

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

Ahhoz, hogy a PowerShell két Storage-fiókot helyezzen üzembe **két előfizetésben**, használja a következőt:

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

A PowerShell esetében annak teszteléséhez, hogy az **erőforráscsoport-objektum** hogyan oldja fel a fölérendelt sablont, a beágyazott sablont és a csatolt sablont, használja a következőt:

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Az előző példában a **parentRG** és a **inlineRG** is megoldódik a **parentGroup**. a **linkedRG** feloldja a **linkedGroup**. Az előző példa kimenete a következő:

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 inlineRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 linkedRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
                                               "name": "linkedGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
```

### <a name="azure-cli"></a>Azure parancssori felület (CLI)

Az Azure CLI esetében két Storage-fiókot helyezzen üzembe két **ugyanabba az előfizetésbe**tartozó erőforráscsoporthoz, használja a következőt:

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

Az Azure CLI esetében két **Storage-fiók**üzembe helyezéséhez használja a következőt:

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

Az Azure CLI esetében annak teszteléséhez, hogy az **erőforráscsoport-objektum** hogyan oldja fel a fölérendelt sablont, a beágyazott sablont és a csatolt sablont, használja a következőt:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

Az előző példában a **parentRG** és a **inlineRG** is megoldódik a **parentGroup**. a **linkedRG** feloldja a **linkedGroup**. Az előző példa kimenete a következő:

```azurecli
...
"outputs": {
  "inlineRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "linkedRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
      "location": "southcentralus",
      "name": "linkedGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "parentRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  }
},
...
```

## <a name="next-steps"></a>Következő lépések

* Ha szeretné megtudni, hogyan határozhat meg paramétereket a sablonban, olvassa el [a Azure Resource Manager sablonok struktúrájának és szintaxisának megismerését](resource-group-authoring-templates.md)ismertető témakört.
* A gyakori telepítési hibák megoldásával kapcsolatos tippekért lásd: [gyakori Azure-telepítési hibák elhárítása Azure Resource Managerokkal](resource-manager-common-deployment-errors.md).
* A SAS-tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [privát sablon üzembe helyezése sas-tokenrel](resource-manager-powershell-sas-token.md).
