---
title: Erőforráscsoport és erőforrások létrehozása az előfizetés-Azure Resource Manager sablonban
description: Leírja, hogyan lehet erőforráscsoportot létrehozni egy Azure Resource Manager sablonban. Azt is bemutatja, hogyan helyezhet üzembe erőforrásokat az Azure-előfizetési hatókörben.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: tomfitz
ms.openlocfilehash: 37f2b04a62d94cce42b095540380460c38bc5b79
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772940"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Erőforráscsoportok és erőforrások létrehozása az előfizetési szinten

Az Azure-erőforrásokat általában az Azure-előfizetésében lévő erőforráscsoporthoz helyezheti üzembe. Létrehozhat azonban Azure-erőforráscsoportokat is, és Azure-erőforrásokat is létrehozhat az előfizetési szinten. A sablonok előfizetési szinten való üzembe helyezéséhez használja az Azure CLI-t és a Azure PowerShell. A Azure Portal nem támogatja az előfizetés szintjén történő telepítést.

Ha egy Azure Resource Manager sablonban szeretne létrehozni egy erőforráscsoportot, Definiáljon egy [Microsoft. Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) -erőforrást az erőforráscsoport nevével és helyével. Létrehozhat egy erőforráscsoportot, és erőforrásokat helyezhet üzembe az adott erőforráscsoporthoz ugyanabban a sablonban. Az előfizetési szinten üzembe helyezhető erőforrások a következők: [Házirendek](../governance/policy/overview.md)és [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md).

## <a name="deployment-considerations"></a>Telepítési szempontok

Az előfizetési szint központi telepítése eltér az erőforráscsoport-telepítéstől a következő szempontok alapján:

### <a name="schema-and-commands"></a>Séma és parancsok

Az előfizetési szintű központi telepítésekhez használt séma és parancsok eltérnek az erőforráscsoport-példányok. 

A séma esetében használja `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`a következőt:.

Az Azure CLI üzembe helyezési parancsához használja az [az Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)parancsot. A következő CLI-parancs például központilag telepít egy sablont az erőforráscsoport létrehozásához:

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

A PowerShell üzembe helyezési parancsához használja a [New-AzDeployment](/powershell/module/az.resources/new-azdeployment)parancsot. A következő PowerShell-parancs például központilag telepít egy sablont az erőforráscsoport létrehozásához:

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

### <a name="deployment-name-and-location"></a>Központi telepítés neve és helye

Az előfizetéshez való üzembe helyezéskor meg kell adnia egy helyet a központi telepítéshez. Megadhatja a központi telepítés nevét is. Ha nem adja meg a központi telepítés nevét, a rendszer a sablon nevét használja a központi telepítés neveként. Egy **azuredeploy. JSON** nevű sablon üzembe helyezése például létrehoz egy alapértelmezett központi telepítési nevet a **azuredeploy**.

Az előfizetési szintű központi telepítések helye nem módosítható. Nem hozhat létre központi telepítést egy helyen, ha már van ilyen nevű, de eltérő helyen üzemelő példány. Ha a hibakódot `InvalidDeploymentLocation`kapja, használjon más nevet vagy ugyanazt a helyet, mint az adott név előző üzembe helyezését.

### <a name="use-template-functions"></a>A Template functions használata

Az előfizetési szintű központi telepítések esetében néhány fontos szempontot figyelembe kell venni a sablon funkcióinak használatakor:

* A [resourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) függvény **nem** támogatott.
* A [resourceId ()](resource-group-template-functions-resource.md#resourceid) függvény támogatott. Ezzel az eszközzel lekérheti az előfizetés szintjén üzemelő példányokon használt erőforrások erőforrás-AZONOSÍTÓját. Például egy szabályzat-definíció erőforrás-AZONOSÍTÓjának beolvasása a következővel:`resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* A [Reference ()](resource-group-template-functions-resource.md#reference) és a [List ()](resource-group-template-functions-resource.md#list) függvények támogatottak.

## <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

A következő sablon egy üres erőforráscsoportot hoz létre.

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

A sablon sémája [itt](/azure/templates/microsoft.resources/allversions)található. Hasonló sablonok találhatók a [githubon](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).

## <a name="create-multiple-resource-groups"></a>Több erőforráscsoport létrehozása

Több erőforráscsoport létrehozásához használja a [Másolás elemet](resource-group-create-multiple.md) az erőforráscsoportok használatával. 

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

Az erőforrás-iterációval kapcsolatos információkért lásd: [egy erőforrás vagy tulajdonság több példányának telepítése Azure Resource Manager-sablonokban](./resource-group-create-multiple.md)és [oktatóanyag: Hozzon létre több erőforrás-példányt Resource](./resource-manager-tutorial-create-multiple-instances.md)Manager-sablonokkal.

## <a name="create-resource-group-and-deploy-resources"></a>Erőforráscsoport létrehozása és erőforrások telepítése

Az erőforráscsoport létrehozásához és az erőforrások üzembe helyezéséhez használjon egy beágyazott sablont. A beágyazott sablon meghatározza az erőforráscsoporthoz telepítendő erőforrásokat. Állítsa be a beágyazott sablont az erőforráscsoport függőként, hogy az erőforrás-csoport az erőforrások telepítése előtt is elérhető legyen.

A következő példában létrehozunk egy erőforráscsoportot, és üzembe helyezünk egy Storage-fiókot az erőforráscsoporthoz.

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

Az alábbi példa egy meglévő szabályzat-definíciót rendel hozzá az előfizetéshez. Ha a házirend paramétereket fogad, adja meg őket objektumként. Ha a házirend nem fogad paramétereket, használja az alapértelmezett üres objektumot.

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

A sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

A sablon PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
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

### <a name="define-and-assign-policy"></a>Házirend meghatározása és hozzárendelése

[Megadhatja és hozzárendelhet](../governance/policy/concepts/definition-structure.md) egy szabályzatot ugyanabban a sablonban.

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

A házirend-definíció létrehozásához az előfizetésében, majd az előfizetésre való alkalmazásához használja az alábbi CLI-parancsot:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

A sablon PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="next-steps"></a>További lépések

* A szerepkörök hozzárendelésével kapcsolatos további tudnivalókért lásd: [Az Azure-erőforrásokhoz való hozzáférés kezelése RBAC és Azure Resource Manager sablonok használatával](../role-based-access-control/role-assignments-template.md).
* A Azure Security Center munkaterület-beállításainak üzembe helyezésére példát a következő témakörben talál: [deployASCwithWorkspaceSettings. JSON](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Azure Resource Manager sablonok létrehozásával kapcsolatos további tudnivalókért lásd: [sablonok készítése](resource-group-authoring-templates.md). 
* A sablonban elérhető függvények listáját itt tekintheti meg: [sablon függvények](resource-group-template-functions.md).
