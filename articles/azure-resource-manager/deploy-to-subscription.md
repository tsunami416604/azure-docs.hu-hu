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
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: d86a1591c81c6343ec376c080945b4bf1f97638a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471776"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Erőforráscsoport és erőforrások létrehozásához az előfizetés szintjén

Általában Azure-erőforrások számára telepít egy erőforráscsoportot az Azure-előfizetésében. Azonban Ön is Azure-erőforráscsoportok létrehozása, és az Azure-erőforrások létrehozása az előfizetés szintjén. Az előfizetés szintjén sablonok üzembe helyezése, Azure CLI-vel és az Azure PowerShell használatához. Az Azure portal nem támogatja a központi telepítés az előfizetési csomagnak.

Hozzon létre egy erőforráscsoportot az Azure Resource Manager-sablon, adjon meg egy [ **Microsoft.Resources/resourceGroups** ](/azure/templates/microsoft.resources/allversions.md) erőforrás nevét és az erőforráscsoport helyét. Hozzon létre egy erőforráscsoportot, és az adott erőforráscsoporton ugyanazt a sablont az erőforrások üzembe helyezése. Az erőforrások az előfizetési szinten üzembe helyezhető a következők: [Házirendek](../azure-policy/azure-policy-introduction.md), és [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md).

## <a name="deployment-considerations"></a>Telepítési szempontok

Előfizetés-szintű telepítés eltér erőforráscsoport üzembe helyezése az a következő szempontokat:

### <a name="schema-and-commands"></a>Séma- és parancsok

Erőforráscsoportok üzemelő példányainak a séma- és előfizetés-szintű telepítések használt parancsok eltérnek. 

A sémát, használja a `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`.

Az Azure CLI telepítési parancsának használata [az üzembe helyezés létrehozása](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). Például a következő CLI-paranccsal hozzon létre egy erőforráscsoportot a sablon helyezi üzembe:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

A PowerShell telepítési parancsának használata [New-AzDeployment](/powershell/module/az.resources/new-azdeployment). Például a következő PowerShell-paranccsal hozzon létre egy erőforráscsoportot a sablon helyezi üzembe:

```azurepowershell
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

### <a name="deployment-name-and-location"></a>Központi telepítés nevét és helyét

Az előfizetéshez telepítésekor meg kell adnia egy helyet a központi telepítés. Az üzemelő példány nevét is megadhatja. Ha nem adja meg a központi telepítés nevét, a sablon nevét az üzemelő példány neve lesz. Például üzembe a sablont **azuredeploy.json** létrehoz egy alapértelmezett telepítési nevével **azuredeploy**.

Előfizetés-szintű központi helye nem módosítható. Központi telepítés nem hozható létre egyetlen helyen azonos nevű meglévő telepítés esetén, de a másik helyet. Ha a hiba kódja `InvalidDeploymentLocation`, használjon egy másik nevet vagy ugyanazon a helyen, a korábbi központi telepítés név.

### <a name="use-template-functions"></a>Sablonokban használható függvények használata

Előfizetés-szintű üzemelő példánya esetében van néhány fontos szempontot sablonokban használható függvények használata esetén:

* A [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) függvény **nem** támogatott.
* A [resourceId()](resource-group-template-functions-resource.md#resourceid) függvény használata támogatott. Használhatja az erőforrás-azonosítója, előfizetés-szintű telepítések használt erőforrások lekérése. Az erőforrás-azonosítója például a szabályzat-definíció lekérése `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* A [reference()](resource-group-template-functions-resource.md#reference) és [list()](resource-group-template-functions-resource.md#list) függvények támogatottak.

## <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

Az alábbi sablont hoz létre egy üres erőforráscsoportot.

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

A sablon séma található [Itt](/azure/templates/microsoft.resources/allversions.md). Hasonló sablonok található [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).

## <a name="create-multiple-resource-groups"></a>Több erőforrás-csoport létrehozása

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

Erőforrás iteráció kapcsolatos információkért lásd: [egynél több példányát egy erőforrást vagy tulajdonság frissítése az Azure Resource Manager-sablonok üzembe helyezése](./resource-group-create-multiple.md), és [oktatóanyag: Több erőforráspéldány létrehozásával létrehozása a Resource Manager-sablonok](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="create-resource-group-and-deploy-resources"></a>Hozzon létre erőforráscsoportot, és erőforrások üzembe helyezése

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

## <a name="create-policies"></a>Szabályzatok létrehozása

### <a name="assign-policy"></a>Szabályzat hozzárendelése

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

Beépített szabályzatot alkalmazza az Azure-előfizetésében, használja a következő Azure CLI-parancsokat:

```azurecli
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit resource location matches resource group location' }

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName auditRGLocation
```

Beépített szabályzatot alkalmazza az Azure-előfizetésében, használja a következő Azure CLI-parancsokat:

```azurecli
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Szabályzat hozzárendelése és megadása

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

A szabályzat-definíció létrehozása az előfizetésében, és alkalmazza azt az előfizetést, használja a következő CLI-parancsot:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="create-roles"></a>Szerepkörök létrehozása

### <a name="assign-role-at-subscription"></a>Előfizetés szerepkör hozzárendelése

Az alábbi példa egy szerepkört rendel egy felhasználóhoz vagy csoporthoz, az előfizetés. Ebben a példában nem adja meg a hozzárendelési hatókör, mert a hatókör értéke automatikusan ahhoz az előfizetéshez.

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

Active Directory csoport hozzárendelése egy szerepkört az előfizetés, használja a következő Azure CLI-parancsokat:

```azurecli
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell
$role = Get-AzRoleDefinition -Name Contributor

$adgroup = Get-AzADGroup -DisplayName demogroup

New-AzDeployment `
  -Name demoRole `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id
```

### <a name="assign-role-at-scope"></a>Hatókörben szerepkör hozzárendelése

A következő előfizetés-szintű sablon egy szerepkört rendel egy felhasználót vagy csoportot, ami egy erőforráscsoportot az előfizetésen belül. A hatókört kell lennie, vagy az alatti üzembe helyezési szintjét. Előfizetés üzembe, és adjon meg egy szerepkör-hozzárendelés hatóköre egy adott előfizetésen belüli erőforráscsoportot. Azonban nem üzembe helyezése egy erőforráscsoportot, és adjon meg egy szerepkör-hozzárendelés hatóköre az előfizetéshez.

Rendelje hozzá a szerepkört egy hatókörben, egy beágyazott üzemelő példány használja. Figyelje meg, hogy az erőforráscsoport nevét a telepítési erőforrás tulajdonságai között, és a szerepkör-hozzárendelés hatóköre tulajdonságában van-e megadva.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        },
        "rgName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "assignRole",
            "resourceGroup": "[parameters('rgName')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Authorization/roleAssignments",
                            "name": "[guid(parameters('principalId'), deployment().name)]",
                            "apiVersion": "2017-09-01",
                            "properties": {
                                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                                "principalId": "[parameters('principalId')]",
                                "scope": "[concat(subscription().id, '/resourceGroups/', parameters('rgName'))]"
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

Active Directory csoport hozzárendelése egy szerepkört az előfizetés, használja a következő Azure CLI-parancsokat:

```azurecli
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json \
  --parameters principalId=$principalid roleDefinitionId=$role rgName demoRg
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell
$role = Get-AzRoleDefinition -Name Contributor

$adgroup = Get-AzADGroup -DisplayName demogroup

New-AzDeployment `
  -Name demoRole `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id `
  -rgName demoRg
```

## <a name="next-steps"></a>További lépések

* Üzembe helyezése az Azure Security Center munkaterület beállításait egy példa: [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Az Azure Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [sablonok készítése](resource-group-authoring-templates.md). 
* A sablonban rendelkezésre álló függvények listája: [sablonfüggvények](resource-group-template-functions.md).
