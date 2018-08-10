---
title: Erőforrások üzembe helyezése az Azure-előfizetésre |} A Microsoft Docs
description: Ismerteti, hogyan hozhat létre egy Azure Resource Manager-sablon, amely üzembe helyezi az erőforrásokat az előfizetések szintjén.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: tomfitz
ms.openlocfilehash: 766534bfa02146e894916e2f9c953ef631913764
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40024993"
---
# <a name="deploy-resources-to-an-azure-subscription"></a>Erőforrások üzembe helyezése az Azure-előfizetés

Általában helyezi üzembe erőforrásokat egy erőforráscsoportba tartozó Azure-előfizetésében. Azonban néhány erőforrást is üzembe helyezhetők az Azure-előfizetés szintjén. Ezeket az erőforrásokat az előfizetésében vonatkoznak. [Házirendek](../azure-policy/azure-policy-introduction.md), [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md), és [az Azure Security Center](../security-center/security-center-intro.md) szolgáltatások, amelyek érdemes az erőforráscsoport szintjén, hanem az előfizetői szintre vonatkoznak.

Ez a cikk az Azure parancssori felület használatával a sablonok üzembe helyezése. PowerShell jelenleg nem támogatja a sablonok telepítésével előfizetésre.

## <a name="assign-policy"></a>Szabályzat hozzárendelése

Az alábbi példa egy meglévő szabályzat-definíció rendel hozzá az előfizetés. Ha a szabályzat paraméter szükséges, adja meg őket olyan objektum. Ha a szabályzat nem használ paramétereket, használja az alapértelmezett üres objektum.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="define-and-assign-policy"></a>Szabályzat hozzárendelése és megadása

Is [definiálása](../azure-policy/policy-definition.md) és ugyanazt a sablont a szabályzat hozzárendelése.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-03-01",
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
            "apiVersion": "2018-03-01",
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

## <a name="assign-role"></a>Szerepkör hozzárendelése

Az alábbi példa egy szerepkört rendel egy felhasználóhoz vagy csoporthoz.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "apiVersion": "2017-05-01",
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
principalid=$(az ad group show --group tomfitzexample --query objectId --output tsv)

az deployment create \
  -n demoRole \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

## <a name="next-steps"></a>További lépések
* Üzembe helyezése az Azure Security Center munkaterület beállításait egy példa: [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Az Azure Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [sablonok készítése](resource-group-authoring-templates.md). 
* A sablonban rendelkezésre álló függvények listája: [sablonfüggvények](resource-group-template-functions.md).

