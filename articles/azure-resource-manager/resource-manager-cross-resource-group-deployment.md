---
title: Rendelje hozzá Azure-erőforrások több előfizetésbe és erőforráscsoportba |} Microsoft Docs
description: Bemutatja, hogyan egynél több Azure előfizetésbe és erőforráscsoportba csoportot célzó központi telepítése során.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2018
ms.author: tomfitz
ms.openlocfilehash: 5e67c60828467cce7c3b40ba17f15f44ad045920
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735670"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Azure-erőforrások telepítése egynél több előfizetésnek vagy erőforráscsoport

Általában, központilag telepített összes erőforrást a sablonhoz, amelyekkel egyetlen [erőforráscsoport](resource-group-overview.md). Vannak azonban forgatókönyvek, ahol szeretne erőforráscsoport telepítsen együtt, de másik erőforráscsoport-sablonok és előfizetések helyezze el őket. Érdemes lehet például a biztonsági mentési virtuális gép telepítése az Azure Site Recovery egy külön erőforráscsoportot és helyet. Erőforrás-kezelő beágyazott cél különböző előfizetésekhez és erőforráscsoportokhoz, mint az előfizetés és a szülő sablon használt erőforráscsoport-sablonok használatát teszi lehetővé.

> [!NOTE]
> Egy központi telepítésnél csak öt erőforráscsoportok telepítene. Általában ez a korlátozás azt jelenti, hogy a szülő-sablon, és legfeljebb négy erőforráscsoportok beágyazott vagy csatolt telepítések megadott egy erőforráscsoportba történő telepítése. Azonban ha a szülő-sablon csak a beágyazott vagy csatolt sablont tartalmazza, és nem saját magát nem telepítheti olyan erőforrásokkal, majd felvehető legfeljebb öt erőforráscsoportok beágyazott vagy csatolt környezetekhez.

## <a name="specify-a-subscription-and-resource-group"></a>Adjon meg egy előfizetésbe és erőforráscsoportba csoportot

Ha szeretne megcélozni egy másik erőforráscsoportban, beágyazott vagy csatolt sablont használ. A `Microsoft.Resources/deployments` erőforrástípust biztosít paramétereinek `subscriptionId` és `resourceGroup`. Ezek a tulajdonságok lehetővé teszik a határozzon meg egy másik előfizetésbe és erőforráscsoportba a beágyazott üzemelő példány. Az erőforráscsoportok léteznie kell a központi telepítés futtatása előtt. Ha nem adja meg az előfizetési azonosító vagy erőforrás-csoport, az előfizetés és a szülő sablonból erőforráscsoport használja.

A sablon telepítéséhez használt fiók jogosultságok központi telepítése a megadott előfizetés-azonosító. Ha a megadott előfizetés egy másik Azure Active Directory-bérlő szerepel, akkor kell [egy másik címtárból adja hozzá a vendégfelhasználók](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Egy másik erőforráscsoportban található és az előfizetés megadásához használja:

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

Ha az erőforráscsoportok ugyanazt az előfizetést, akkor távolítsa el a **subscriptionId** érték.

A következő példában két storage-fiókok – egyet-egyet a telepítés során megadott erőforráscsoport telepíti, és egy olyan erőforráscsoport a megadott a `secondResourceGroup` paraméter:

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

Ha `resourceGroup` , amely nem található erőforráscsoport nevét, a telepítés sikertelen lesz.

A példa sablon üzembe helyezése, használja az Azure PowerShell 4.0.0 vagy később, vagy az Azure CLI 2.0.0 vagy újabb.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>A resourceGroup() és subscription() funkciók

A kereszt-erőforrás csoport telepítések esetén a [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) és [subscription()](resource-group-template-functions-resource.md#subscription) funkciók megoldásához másképp alapján hogyan határozza meg a beágyazott sablont. 

Ha egy sablon belül egy másik sablon beágyazásához a beágyazott sablonban funkciók oldja fel a szülő erőforráscsoport és az előfizetés. Egy beágyazott sablon a következő formátumot használja:

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

Ha egy külön sablonhoz, a csatolt sablonban funkciók oldja fel a beágyazott erőforráscsoport és az előfizetés. A csatolt sablon a következő formátumot használja:

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

## <a name="example-templates"></a>Példa sablonok

Az alábbi sablonok bemutatják a több erőforrás-csoport központi telepítését. Parancsprogramok üzembe helyezheti a sablonokat a táblázat után jelennek meg.

|Sablon  |Leírás  |
|---------|---------|
|[Kereszt-előfizetés sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Telepíti az egyik erőforráscsoportból egy tárfiókot és az egy tárfiókot egy másik erőforráscsoportban található. Tartalmazhat az előfizetési Azonosítóhoz tartozó értéket, ha a második erőforráscsoport van egy másik előfizetést. |
|[Kereszt-sablon az erőforrás-csoport tulajdonságai](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Bemutatja, hogyan a `resourceGroup()` oldja fel a rendszer működéséhez. Azt nem kell telepítenie minden olyan erőforrásnál. |

### <a name="powershell"></a>PowerShell

PowerShell központi telepítése a két erőforráscsoport két storage-fiókok a **ugyanahhoz az előfizetéshez**, használja:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzureRmResourceGroup -Name $firstRG -Location southcentralus
New-AzureRmResourceGroup -Name $secondRG -Location eastus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

PowerShell központi telepítése a két tárfiókok **két előfizetések**, használja:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzureRmSubscription -Subscription $secondSub
New-AzureRmResourceGroup -Name $secondRG -Location eastus

Select-AzureRmSubscription -Subscription $firstSub
New-AzureRmResourceGroup -Name $firstRG -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

PowerShell tesztelése az **erőforrás csoportobjektum** oldja fel a szülő sablon, a beágyazott sablon és a csatolt sablonhoz használható:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Az előző példában is **parentRG** és **inlineRG** tartoznia **parentGroup**. **linkedRG** feloldása egy olyan **linkedGroup**. Az előző példában a kimenete a következő:

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

### <a name="azure-cli"></a>Azure CLI

Azure parancssori felület, központi telepítése a két erőforráscsoport két storage-fiókok a **ugyanahhoz az előfizetéshez**, használja:

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

Azure parancssori felület telepítése a két tárfiókok **két előfizetések**, használja:

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

Azure parancssori felület teszteléséhez az **erőforrás csoportobjektum** oldja fel a szülő sablon, a beágyazott sablon és a csatolt sablonhoz használható:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

Az előző példában is **parentRG** és **inlineRG** tartoznia **parentGroup**. **linkedRG** feloldása egy olyan **linkedGroup**. Az előző példában a kimenete a következő:

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

## <a name="next-steps"></a>További lépések

* Szeretné megtudni, hogyan adhat meg a paramétereket a sablonban, lásd: [megérteni a felépítését és Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).
* Tippek az általános telepítési hibák feloldására, lásd: [hibaelhárítás általános az Azure-telepítés az Azure Resource Manager](resource-manager-common-deployment-errors.md).
* A sablont, amely a SAS-jogkivonat szükséges, központi telepítésével kapcsolatos információkért lásd: [telepítés titkos sablont a SAS-jogkivonat](resource-manager-powershell-sas-token.md).
