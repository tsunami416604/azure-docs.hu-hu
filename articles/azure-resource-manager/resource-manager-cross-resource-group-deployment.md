---
title: "Rendelje hozzá Azure-erőforrások több erőforrás |} Microsoft Docs"
description: "Bemutatja, hogyan egynél több Azure-erőforrás-csoportot célzó központi telepítése során."
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
ms.date: 06/15/2017
ms.author: tomfitz
ms.openlocfilehash: d8b041213b269775175a810e585103d3c538557f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-azure-resources-to-more-than-one-resource-group"></a>Azure-erőforrások telepítése egynél több erőforrás-csoportban

Általában központi telepítését az erőforrásokat a sablonhoz, amelyekkel egyetlen erőforráscsoportként működnek. Vannak azonban forgatókönyvek erőforráscsoport telepítsen együtt, de helyezze el őket a különböző erőforráscsoportokra helyét. Érdemes lehet például a biztonsági mentési virtuális gép telepítése az Azure Site Recovery egy külön erőforráscsoportot és helyet. Erőforrás-kezelő lehetővé teszi, amelyekre a különböző erőforráscsoportokra, mint a szülő sablon használt erőforráscsoport beágyazott sablonok használatával.

Az erőforráscsoport egy életciklus tárolójának az alkalmazás és az erőforrások gyűjteményét. Az erőforráscsoport a sablon kívül létrehozása, és adja meg az erőforráscsoportot, hogy a szabályzat üzembe helyezése során. Megismerkedhet az erőforráscsoportok, lásd: [Azure Resource Manager áttekintése](resource-group-overview.md).

## <a name="example-template"></a>Példa sablon

Célozza meg egy másik erőforráscsoportban, egy beágyazott vagy csatolt sablon üzembe helyezése során kell használnia. A `Microsoft.Resources/deployments` erőforrástípust biztosít egy `resourceGroup` paraméter, amely lehetővé teszi, hogy adja meg a beágyazott üzemelő példány egy másik erőforráscsoportban található. Az erőforráscsoportok léteznie kell a központi telepítés futtatása előtt. A következő példa telepíti a két storage-fiókok – egyet-egyet a telepítés során megadott erőforráscsoport és egy-egy nevű erőforráscsoport `crossResourceGroupDeployment`:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "StorageAccountName1": {
            "type": "string"
        },
        "StorageAccountName2": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "crossResourceGroupDeployment",
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
                            "name": "[parameters('StorageAccountName2')]",
                            "apiVersion": "2015-06-15",
                            "location": "West US",
                            "properties": {
                                "accountType": "Standard_LRS"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('StorageAccountName1')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
                "accountType": "Standard_LRS"
            }
        }
    ]
}
```

Ha `resourceGroup` , amely nem található erőforráscsoport nevét, a telepítés sikertelen lesz. Ha nem ad meg értéket `resourceGroup`, erőforrás-kezelő a szülő erőforrás csoportot használja.  

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A példa sablon üzembe helyezése, a portál, Azure PowerShell vagy az Azure parancssori felület is használhat. Az Azure PowerShell vagy Azure CLI-t előfordulhat, hogy 2017, vagy később kiadási kell használnia. A példák azt feltételezik, hogy mentett a sablon helyileg nevű fájl **crossrgdeployment.json**.

A PowerShell környezethez:

```powershell
Login-AzureRmAccount

New-AzureRmResourceGroup -Name mainResourceGroup -Location "South Central US"
New-AzureRmResourceGroup -Name crossResourceGroupDeployment -Location "Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName mainResourceGroup `
  -TemplateFile c:\MyTemplates\crossrgdeployment.json
```

Az Azure parancssori felület:

```azurecli
az login

az group create --name mainResourceGroup --location "South Central US"
az group create --name crossResourceGroupDeployment --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group mainResourceGroup \
    --template-file crossrgdeployment.json
```

Központi telepítés befejezése után két erőforráscsoport láthatja. Mindegyik erőforráscsoport egy tárfiókot.

## <a name="use-resourcegroup-function"></a>ResourceGroup() funkcióval

A kereszt-erőforrás csoport telepítések esetén a [resouceGroup() függvény](resource-group-template-functions-resource.md#resourcegroup) oldja fel a rendszer eltérően a gyűjteményekkel adhatja meg a beágyazott sablon alapján. 

Egy sablon belül egy másik sablon beágyazásakor a beágyazott sablonban resouceGroup() oldja fel a szülőcsoport erőforrás. Egy beágyazott sablon a következő formátumot használja:

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

Ha egy külön sablonhoz, a csatolt sablonban resouceGroup() oldja fel a beágyazott erőforráscsoportot. A csatolt sablon a következő formátumot használja:

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

## <a name="next-steps"></a>Következő lépések

* Szeretné megtudni, hogyan adhat meg a paramétereket a sablonban, lásd: [megérteni a felépítését és Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).
* Tippek az általános telepítési hibák feloldására, lásd: [hibaelhárítás általános az Azure-telepítés az Azure Resource Manager](resource-manager-common-deployment-errors.md).
* A sablont, amely a SAS-jogkivonat szükséges, központi telepítésével kapcsolatos információkért lásd: [telepítés titkos sablont a SAS-jogkivonat](resource-manager-powershell-sas-token.md).
