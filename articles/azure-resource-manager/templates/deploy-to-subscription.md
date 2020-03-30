---
title: Erőforrások üzembe helyezése az előfizetésbe
description: Ez a témakör azt ismerteti, hogy miként hozhat létre erőforráscsoportot egy Azure Resource Manager-sablonban. Azt is bemutatja, hogyan helyezhet üzembe erőforrásokat az Azure-előfizetés hatókörében.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 65cc220d32d1e1149b7026fc438f5e34262511dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131958"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Erőforráscsoportok és -erőforrások létrehozása előfizetési szinten

Az Azure-előfizetés erőforrásainak egyszerűbb kezelése érdekében [szabályzatokat](../../governance/policy/overview.md) vagy [szerepköralapú hozzáférés-vezérléseket](../../role-based-access-control/overview.md) határozhat meg és rendelhet hozzá az előfizetésben. Az előfizetési szintű sablonok, deklaratív módon alkalmazza a szabályzatokat, és szerepkörök hozzárendelése az előfizetésben. Erőforráscsoportokat is létrehozhat, és erőforrásokat is telepíthet.

Sablonok üzembe helyezéséhez az előfizetés szintjén, használja az Azure CLI, PowerShell vagy REST API.To deploy templates at the subscription level, use Azure CLI, PowerShell vagy REST API. Az Azure Portal nem támogatja az előfizetési szinten történő üzembe helyezést.

## <a name="supported-resources"></a>Támogatott erőforrások

A következő erőforrástípusokat telepítheti az előfizetés szintjén:

* [Költségvetések](/azure/templates/microsoft.consumption/budgets)
* [központi telepítések](/azure/templates/microsoft.resources/deployments) – az erőforráscsoportokra telepített beágyazott sablonokhoz.
* [eventElőfizetések](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns között](/azure/templates/microsoft.peering/peerasns)
* [házirend-hozzárendelések](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions (policyDefinitions)](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions (policySetDefinitions)](/azure/templates/microsoft.authorization/policysetdefinitions)
* [kármentesítések](/azure/templates/microsoft.policyinsights/remediations)
* [erőforráscsoportok](/azure/templates/microsoft.resources/resourcegroups)
* [szerepkör-hozzárendelések](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions (szerepkördefiníciók)](/azure/templates/microsoft.authorization/roledefinitions)
* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [címkét](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>Séma

Az előfizetésszintű központi telepítésekhez használt séma eltér az erőforráscsoport-telepítések sémájátétól.

Sablonok esetén használja a következőket:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

A paraméterfájl sémája minden telepítési hatóköresetében megegyezik. Paraméterfájlok esetén használja a következőket:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Telepítési parancsok

Az előfizetési szintű központi telepítések parancsai eltérnek az erőforráscsoport-telepítések parancsaitól.

Az Azure CLI esetén használja [az az deployment sub create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create). A következő példa egy sablont telepít egy erőforráscsoport létrehozásához:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

A PowerShell központi telepítési parancsához használja a [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) vagy **a New-AzSubscriptionDeployment parancsot.** A következő példa egy sablont telepít egy erőforráscsoport létrehozásához:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

REST API-hoz használja [a Központi telepítések – Létrehozás az előfizetési hatókörön.](/rest/api/resources/deployments/createorupdateatsubscriptionscope)

## <a name="deployment-location-and-name"></a>A telepítés helye és neve

Az előfizetési szintű telepítések esetén meg kell adnia a központi telepítés helyét. A központi telepítés helye nem áll meg a telepített erőforrások helyétől. A központi telepítési hely határozza meg, hogy hol kell tárolni a telepítési adatokat.

Megadhat nevet a központi telepítésnek, vagy használhatja az alapértelmezett központi telepítés nevét. Az alapértelmezett név a sablonfájl neve. Például egy **azuredeploy.json** nevű sablon üzembe helyezése létrehoz egy alapértelmezett üzembe helyezési nevet az **azuredeploy.**

Az egyes központi telepítés neve immutalitása nem módosítható. Nem hozhat létre központi telepítést egy helyen, ha egy másik helyen azonos nevű meglévő központi telepítés van. Ha a hibakódot `InvalidDeploymentLocation`kapja, használjon másik nevet vagy ugyanazon a helyet, mint az előző központi telepítésaz adott névhez.

## <a name="use-template-functions"></a>Sablonfüggvények használata

Az előfizetési szintű telepítések esetében a sablonfüggvények használata során néhány fontos szempont ot figyelembe kell venni:

* A [resourceGroup()](template-functions-resource.md#resourcegroup) függvény **nem** támogatott.
* [A(z) reference()](template-functions-resource.md#reference) és [a list()](template-functions-resource.md#list) függvények támogatottak.
* Az [subscriptionResourceId()](template-functions-resource.md#subscriptionresourceid) függvény használatával lekéri az erőforrás-azonosítót az előfizetési szinten üzembe helyezett erőforrásokhoz.

  Ha például egy házirend-definíció erőforrásazonosítóját szeretné lekérni, használja a következőket:
  
  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  A visszaadott erőforrás-azonosító formátuma a következő:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

Ha egy Azure Resource Manager-sablonban erőforráscsoportot szeretne létrehozni, definiáljon egy [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) erőforrást az erőforráscsoport nevével és helyével. Létrehozhat egy erőforráscsoportot, és ugyanabban a sablonban helyezhet üzembe erőforrásokat az adott erőforráscsoportba.

A következő sablon létrehoz egy üres erőforráscsoportot.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Az erőforráscsoportokkal rendelkező [másolási elem](copy-resources.md) segítségével több erőforráscsoportot hozhat létre.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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

Az erőforrás-iterációról további információt az [Erőforrás-kezelő sablonokban egy erőforrás több példányának telepítése](./copy-resources.md)és [oktatóanyag: Több erőforráspéldány létrehozása erőforrás-kezelősablonokkal című](./template-tutorial-create-multiple-instances.md)témakörben talál.

## <a name="resource-group-and-resources"></a>Erőforráscsoport és erőforrások

Az erőforráscsoport létrehozásához és az erőforrások üzembe helyezéséhez használjon beágyazott sablont. A beágyazott sablon határozza meg az erőforráscsoportba telepítandó erőforrásokat. Állítsa be a beágyazott sablont az erőforráscsoporttól függőként, és győződjön meg arról, hogy az erőforráscsoport létezik az erőforrások üzembe helyezése előtt.

A következő példa létrehoz egy erőforráscsoportot, és egy tárfiókot telepít az erőforráscsoportba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
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

### <a name="assign-policy"></a>Házirend hozzárendelése

A következő példa egy meglévő szabályzat-definíciót rendel az előfizetéshez. Ha a házirend paramétereket vesz igénybe, adja meg azokat objektumként. Ha a házirend nem vesz igénybe paramétereket, használja az alapértelmezett üres objektumot.

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
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

A sablon azure CLI-vel történő üzembe helyezéséhez használja a következőket:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

A sablon PowerShell használatával történő üzembe helyezéséhez használja a következőket:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Házirend definiálása és hozzárendelése

A [házirendet](../../governance/policy/concepts/definition-structure.md) ugyanabban a sablonban határozhatja meg és rendelheti hozzá.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
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
      "apiVersion": "2018-05-01",
      "name": "location-lock",
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

Ha létre szeretné hozni a szabályzatdefiníciót az előfizetésében, és alkalmazni szeretné az előfizetésre, használja a következő CLI parancsot:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

A sablon PowerShell használatával történő üzembe helyezéséhez használja a következőket:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="template-samples"></a>Sablonminták

* [Hozzon létre egy erőforráscsoportot, zárolja és engedélyeket adjon neki.](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)
* [Hozzon létre egy erőforráscsoportot, egy házirendet és egy házirend-hozzárendelést.](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json)

## <a name="next-steps"></a>További lépések

* A szerepkörök hozzárendeléséről az [Azure-erőforrásokhoz való hozzáférés kezelése RBAC és Azure Resource Manager-sablonok használatával](../../role-based-access-control/role-assignments-template.md)című témakörben olvashat.
* Az Azure Security Center munkaterületi beállításainak üzembe helyezését lásd: [deployASCwithWorkspaceSettings.json.](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)
* Mintasablonok találhatók a [GitHubon.](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments)
* Sablonokat felügyeleti csoport [és bérlői](deploy-to-management-group.md) [szinten](deploy-to-tenant.md)is telepíthet.
