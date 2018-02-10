---
title: "Rendelje hozzá Azure-erőforrások több előfizetésbe és erőforráscsoportba |} Microsoft Docs"
description: "Bemutatja, hogyan egynél több Azure előfizetésbe és erőforráscsoportba csoportot célzó központi telepítése során."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2018
ms.author: tomfitz
ms.openlocfilehash: 40b2d04fe829c51a58fb3bec1519a590a12cfdb8
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Azure-erőforrások telepítése egynél több előfizetésnek vagy erőforráscsoport

Általában, központilag telepített összes erőforrást a sablonhoz, amelyekkel egyetlen [erőforráscsoport](resource-group-overview.md). Vannak azonban forgatókönyvek, ahol szeretne erőforráscsoport telepítsen együtt, de másik erőforráscsoport-sablonok és előfizetések helyezze el őket. Érdemes lehet például a biztonsági mentési virtuális gép telepítése az Azure Site Recovery egy külön erőforráscsoportot és helyet. Erőforrás-kezelő beágyazott cél különböző előfizetésekhez és erőforráscsoportokhoz, mint az előfizetés és a szülő sablon használt erőforráscsoport-sablonok használatát teszi lehetővé.

> [!NOTE]
> Egy központi telepítésnél csak öt erőforráscsoportok telepítene.

## <a name="specify-a-subscription-and-resource-group"></a>Adjon meg egy előfizetésbe és erőforráscsoportba csoportot

Ha szeretne megcélozni egy másik erőforráscsoportban, beágyazott vagy csatolt sablont használ. A `Microsoft.Resources/deployments` erőforrástípust biztosít paramétereinek `subscriptionId` és `resourceGroup`. Ezek a tulajdonságok lehetővé teszik a határozzon meg egy másik előfizetésbe és erőforráscsoportba a beágyazott üzemelő példány. Az erőforráscsoportok léteznie kell a központi telepítés futtatása előtt. Ha nem adja meg az előfizetési azonosító vagy erőforrás-csoport, az előfizetés és a szülő sablonból erőforráscsoport használja.

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

## <a name="use-the-resourcegroup-function"></a>A resourceGroup() funkcióval

A kereszt-erőforrás csoport telepítések esetén a [resourceGroup() függvény](resource-group-template-functions-resource.md#resourcegroup) oldja fel a rendszer eltérően a gyűjteményekkel adhatja meg a beágyazott sablon alapján. 

Egy sablon belül egy másik sablon beágyazásakor a beágyazott sablonban resourceGroup() oldja fel a szülőcsoport erőforrás. Egy beágyazott sablon a következő formátumot használja:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

Ha egy külön sablonhoz, a csatolt sablonban resourceGroup() oldja fel a beágyazott erőforráscsoportot. A csatolt sablon a következő formátumot használja:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
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

```powershell
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

```powershell
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

```powershell
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
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

## <a name="next-steps"></a>További lépések

* Szeretné megtudni, hogyan adhat meg a paramétereket a sablonban, lásd: [megérteni a felépítését és Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).
* Tippek az általános telepítési hibák feloldására, lásd: [hibaelhárítás általános az Azure-telepítés az Azure Resource Manager](resource-manager-common-deployment-errors.md).
* A sablont, amely a SAS-jogkivonat szükséges, központi telepítésével kapcsolatos információkért lásd: [telepítés titkos sablont a SAS-jogkivonat](resource-manager-powershell-sas-token.md).
