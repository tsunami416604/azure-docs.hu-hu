---
title: Erőforráscsoport és erőforrások létrehozásához előfizetés – Azure Resource Manager-sablon
description: Ismerteti, hogyan hozhat létre egy erőforráscsoportot az Azure Resource Manager-sablon. Azt is bemutatja, hogyan helyezhet üzembe erőforrásokat az Azure-előfizetési hatókörben.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2018
ms.author: tomfitz
ms.openlocfilehash: 5b8247533a8bf51017767aac3a04e47ce6348a60
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435293"
---
# <a name="create-resource-groups-and-resources-for-an-azure-subscription"></a>Erőforráscsoportokat és erőforrásokat az Azure-előfizetés létrehozása

Általában helyezi üzembe erőforrásokat egy erőforráscsoportba tartozó Azure-előfizetésében. Azonban az előfizetés-szintű üzemelő példányok használatával hozzon létre az erőforráscsoport és erőforrások, amelyek a alkalmazni az előfizetésében.

Hozzon létre egy erőforráscsoportot az Azure Resource Manager-sablon, adjon meg egy **Microsoft.Resources/resourceGroups** erőforrás nevét és az erőforráscsoport helyét. Hozzon létre egy erőforráscsoportot, és az adott erőforráscsoporton ugyanazt a sablont az erőforrások üzembe helyezése.

[Házirendek](../azure-policy/azure-policy-introduction.md), [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md), és [az Azure Security Center](../security-center/security-center-intro.md) szolgáltatások, amelyek érdemes az erőforráscsoport szintjén, hanem az előfizetői szintre vonatkoznak.

Ez a cikk bemutatja, hogyan erőforráscsoportokat létrehozni, és hogyan hozhat létre erőforrásokat, amelyek a alkalmazni az előfizetésen. Azure CLI és PowerShell használatával a sablonok üzembe helyezése. A sablonok üzembe helyezése, mert felületén telepíti az erőforráscsoport, a nem Azure-előfizetést a portálon nem használható.

## <a name="schema-and-commands"></a>Séma- és parancsok

Erőforráscsoportok üzemelő példányainak a séma- és előfizetés-szintű telepítések használt parancsok eltérnek. 

A sémát, használja a `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`.

Az Azure CLI telepítési parancsának használata [az üzembe helyezés létrehozása](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create).

A PowerShell telepítési parancsának használata [New-AzureRmDeployment](/powershell/module/azurerm.resources/new-azurermdeployment).

## <a name="name-and-location"></a>Név és hely

Az előfizetéshez telepítésekor meg kell adnia egy helyet a központi telepítés. Az üzemelő példány nevét is megadhatja. Ha nem adja meg a központi telepítés nevét, a sablon nevét az üzemelő példány neve lesz. Például üzembe a sablont **azuredeploy.json** létrehoz egy alapértelmezett telepítési nevével **azuredeploy**.

Előfizetés-szintű központi helye nem módosítható. Központi telepítés nem hozható létre egyetlen helyen azonos nevű meglévő telepítés esetén, de a másik helyet. Ha a hiba kódja `InvalidDeploymentLocation`, használjon egy másik nevet vagy ugyanazon a helyen, a korábbi központi telepítés név.

## <a name="using-template-functions"></a>Sablonokban használható függvények használatával

Előfizetési szintű üzemelő példánya esetében van néhány fontos szempontot sablonokban használható függvények használata esetén:

* A [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) függvény **nem** támogatott.
* A [resourceId()](resource-group-template-functions-resource.md#resourceid) függvény használata támogatott. Használhatja az erőforrás-azonosítója, előfizetés-szintű telepítések használt erőforrások lekérése. Az erőforrás-azonosítója például a szabályzat-definíció lekérése `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* A [reference()](resource-group-template-functions-resource.md#reference) és [list()](resource-group-template-functions-resource.md#list) függvények támogatottak.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

A következő példában létrehozunk egy üres erőforráscsoportot.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Ez a sablon Azure CLI-vel üzembe helyezéséhez használja:

```azurecli-interactive
az deployment create \
  -n demoEmptyRG \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoRG rgLocation=northcentralus
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name demoEmptyRG `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demogroup `
  -rgLocation northcentralus
```

## <a name="create-several-resource-groups"></a>Számos erőforrás-csoportok létrehozása

Használja a [másolási eleme](resource-group-create-multiple.md) erőforráscsoportokkal több erőforráscsoport létrehozásához. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgNamePrefix": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "instanceCount": {
            "type": "int"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
            "copy": {
                "name": "rgCopy",
                "count": "[parameters('instanceCount')]"
            },
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Telepítse ezt a sablont az Azure CLI-vel, és három erőforráscsoportokat létrehozni, használja:

```azurecli-interactive
az deployment create \
  -n demoCopyRG \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/copyRG.json \
  --parameters rgNamePrefix=demoRG rgLocation=northcentralus instanceCount=3
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name demoCopyRG `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/copyRG.json `
  -rgNamePrefix demogroup `
  -rgLocation northcentralus `
  -instanceCount 3
```

## <a name="create-resource-group-and-deploy-resource"></a>Erőforráscsoport létrehozása és üzembe helyezése erőforrás

Hozhat létre az erőforráscsoport és erőforrások történő üzembe helyezéséhez használjon a beágyazott sablont. A beágyazott sablont határozza meg, az erőforráscsoport üzembe helyezendő erőforrásokat. A beágyazott sablont állítja az erőforráscsoport, hogy az erőforráscsoport létezik az erőforrások üzembe helyezése előtt függ.

Az alábbi példa létrehoz egy erőforráscsoportot, és a egy storage-fiók telepíti az erőforráscsoport.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
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
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

Ez a sablon Azure CLI-vel üzembe helyezéséhez használja:

```azurecli-interactive
az deployment create \
  -n demoRGStorage \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/newRGWithStorage.json \
  --parameters rgName=rgStorage rgLocation=northcentralus storagePrefix=storage
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name demoRGStorage `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/newRGWithStorage.json `
  -rgName rgStorage `
  -rgLocation northcentralus `
  -storagePrefix storage
```

## <a name="assign-policy"></a>Szabályzat hozzárendelése

Az alábbi példa egy meglévő szabályzat-definíció rendel hozzá az előfizetés. Ha a szabályzat paraméter szükséges, adja meg őket olyan objektum. Ha a szabályzat nem használ paramétereket, használja az alapértelmezett üres objektum.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "policyDefinitionID": {
            "type": "string"
        },
        "policyName": {
            "type": "string"
        },
        "policyParameters": {
            "type": "object",
            "defaultValue": {}
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```

Beépített szabályzatot alkalmazza az Azure-előfizetésében, használja a következő Azure CLI-parancsokat. Ebben a példában a szabályzat nem rendelkezik paraméterekkel

```azurecli-interactive
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit resource location matches resource group location' }

New-AzureRmDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName auditRGLocation
```

Beépített szabályzatot alkalmazza az Azure-előfizetésében, használja a következő Azure CLI-parancsokat. Ebben a példában a szabályzat paraméterrel rendelkezik.

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzureRmDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

## <a name="define-and-assign-policy"></a>Szabályzat hozzárendelése és megadása

Is [definiálása](../azure-policy/policy-definition.md) és ugyanazt a sablont a szabályzat hozzárendelése.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
            "properties": {
                "policyType": "Custom",
                "parameters": {},
                "policyRule": {
                    "if": {
                        "field": "location",
                        "equals": "northeurope"
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2018-05-01",
            "dependsOn": [
                "locationpolicy"
            ],
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
            }
        }
    ]
}
```

A szabályzat-definíció létrehozása az előfizetésében, és alkalmazza azt az előfizetést, használja a következő CLI-parancsot.

```azurecli-interactive
az deployment create \
  -n definePolicy \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name definePolicy `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="assign-role"></a>Szerepkör hozzárendelése

Az alábbi példa egy szerepkört rendel egy felhasználóhoz vagy csoporthoz.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "[guid(parameters('principalId'), deployment().name)]",
            "apiVersion": "2017-09-01",
            "properties": {
                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Active Directory csoport hozzárendelése az előfizetéshez tartozó szerepkörhöz, használja a következő Azure CLI-parancsokat.

```azurecli-interactive
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  -n demoRole \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
$role = Get-AzureRmRoleDefinition -Name Contributor

$adgroup = Get-AzureRmADGroup -DisplayName demogroup

New-AzureRmDeployment `
  -Name demoRole `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id
```

## <a name="next-steps"></a>További lépések
* Üzembe helyezése az Azure Security Center munkaterület beállításait egy példa: [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Az Azure Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [sablonok készítése](resource-group-authoring-templates.md). 
* A sablonban rendelkezésre álló függvények listája: [sablonfüggvények](resource-group-template-functions.md).
