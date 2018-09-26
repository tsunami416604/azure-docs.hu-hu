---
title: Több előfizetés és erőforrás-csoportok az Azure erőforrások üzembe helyezése |} A Microsoft Docs
description: Bemutatja, hogyan célcsoport egynél több Azure előfizetésben és erőforráscsoportban üzembe helyezés során.
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
ms.openlocfilehash: fec075a744b5f47a4be7f1b960cceedfea7b9a2c
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47090792"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Egynél több előfizetésen vagy erőforráscsoporton az Azure-erőforrások üzembe helyezése

Általában végzi az üzembe helyezést összes erőforrást a sablonhoz, amelyekkel egy [erőforráscsoport](resource-group-overview.md). Vannak azonban forgatókönyvek, ahol szeretné erőforráscsoport telepítsen együtt, de különböző erőforráscsoport vagy előfizetés helyezze el őket. Például előfordulhat, hogy számára telepíteni kívánja a biztonsági mentési virtuális gép Azure Site Recovery egy külön erőforráscsoportot és helyet. Resource Manager lehetővé teszi, hogy használjon beágyazott cél különböző előfizetésekhez és erőforráscsoportokhoz, mint az előfizetés és a fölérendelt sablon használt erőforráscsoport-sablonok.

> [!NOTE]
> Telepíthet egy központi telepítésben csak öt erőforráscsoportokhoz. Ez a korlátozás általában azt jelenti, egy erőforráscsoport megadva, a fölérendelt sablon, és legfeljebb négy erőforráscsoportok beágyazott vagy hivatkozott telepítések esetén telepítheti. Azonban ha a fölérendelt sablon csak a beágyazott vagy hivatkozott sablont tartalmaz, és nem magát nem erőforrások üzembe helyezése bármely, majd megadhatja legfeljebb öt erőforráscsoportok beágyazott vagy hivatkozott telepítések esetén.

## <a name="specify-a-subscription-and-resource-group"></a>Adjon meg egy előfizetésben és erőforráscsoportban csoportot

Egy másik erőforrás célozza meg, használjon a beágyazott vagy hivatkozott sablont. A `Microsoft.Resources/deployments` erőforrástípust biztosít paramétereinek `subscriptionId` és `resourceGroup`. Ezek a tulajdonságok lehetővé teszik a beágyazott üzemelő példány egy másik előfizetésben és erőforráscsoportban csoporthoz adja meg. Az erőforráscsoportok léteznie kell a központi telepítés futtatása előtt. Ha nincs megadva vagy az előfizetési Azonosítóját vagy erőforrás-csoport, az előfizetésben és erőforráscsoportban a szülő sablonból használatos.

A sablon telepítéséhez használt fióknak jogosultnak kell lennie a központi telepítése a megadott előfizetés-azonosítójára. Ha a megadott előfizetés már létezik egy másik Azure Active Directory-bérlőhöz, kell [vendég felhasználók hozzáadása másik címtárból](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Egy másik erőforráscsoportot és egy előfizetést, akkor adja meg:

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

Ha az erőforráscsoportok ugyanabban az előfizetésben, eltávolíthatja a **subscriptionId** értéket.

Az alábbi példa két tárfiókot – egy, a telepítés során megadott erőforráscsoportban helyezi üzembe, és a egy erőforráscsoport a megadott a `secondResourceGroup` paramétert:

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

Ha `resourceGroup` egy nem létező erőforráscsoport neve, a központi telepítés sikertelen lesz.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>A resourceGroup() és subscription() függvények használata

A tartományok közötti erőforráscsoportok üzemelő példányainak, a [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) és [subscription()](resource-group-template-functions-resource.md#subscription) funkciók megoldásához eltérően alapján adhatja meg a beágyazott sablont. 

Egy sablon belül egy másik sablon beágyazásakor a beágyazott sablonban függvények feloldani a szülő erőforráscsoportban és előfizetésben. Egy beágyazott sablont a következő formátumot használja:

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

Ha külön sablonok, a hivatkozott sablonnak az a funkciók feloldani a beágyazott erőforrás-csoport és az előfizetés. Egy hivatkozott sablonnak a következő formátumot használja:

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

Az alábbi sablonok több erőforráscsoportok üzemelő példányainak mutatják be. A sablonok üzembe helyezése szkriptek jelennek meg a táblázat után.

|Sablon  |Leírás  |
|---------|---------|
|[Adatbázisközi előfizetés sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Üzembe helyez egy erőforráscsoportot egy storage-fiókot és a egy storage-fiók egy másik erőforráscsoportba. Ha a második erőforráscsoportot egy másik előfizetésben található értéket tartalmazza az előfizetés-azonosító. |
|[Adatbázisközi sablon az erőforrás-csoport tulajdonságai](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Bemutatja, hogyan a `resourceGroup()` függvény szándékot oldja fel. Azt nem kell telepítenie minden olyan erőforrásokat. |

### <a name="powershell"></a>PowerShell

A PowerShell, a két erőforráscsoport központi telepítése két tárfiókot a **ugyanahhoz az előfizetéshez**, használja:

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

PowerShell esetén két tárfiókot való üzembe helyezéséhez **két előfizetéssel**, használja:

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

PowerShell, tesztelje a **erőforrás csoportobjektum** oldja fel a fölérendelt sablon, a beágyazott sablont és a hivatkozott sablonnak használatra:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Az előző példában mindkét **parentRG** és **inlineRG** oldja fel a **parentGroup**. **linkedRG** mutat **linkedGroup**. Az előző példából kimenete:

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

Azure CLI-hez, a két erőforráscsoport központi telepítése két tárfiókot a **ugyanahhoz az előfizetéshez**, használja:

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

Azure CLI telepítése a két tárfiókot **két előfizetéssel**, használja:

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

Azure CLI-hez, tesztelheti a **erőforrás csoportobjektum** oldja fel a fölérendelt sablon, a beágyazott sablont és a hivatkozott sablonnak használatra:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

Az előző példában mindkét **parentRG** és **inlineRG** oldja fel a **parentGroup**. **linkedRG** mutat **linkedGroup**. Az előző példából kimenete:

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

* A sablonban szereplő paraméterekkel definiálása ismertetése: [struktúra és az Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).
* Gyakori üzembehelyezési hibák elhárítása a tippek: [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](resource-manager-common-deployment-errors.md).
* SAS-token igénylő sablonok telepítésével kapcsolatos információkért lásd: [saját sablon üzembe helyezése SAS-jogkivonat használatával](resource-manager-powershell-sas-token.md).
