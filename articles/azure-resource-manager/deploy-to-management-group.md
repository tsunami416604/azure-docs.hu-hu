---
title: Erőforrások központi telepítése a felügyeleti csoportba
description: Ismerteti, hogyan lehet erőforrásokat telepíteni a felügyeleti csoport hatókörében egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: c8ed8ae20192599ff7cda691429230d8c2a104fc
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149904"
---
# <a name="create-resources-at-the-management-group-level"></a>Erőforrások létrehozása a felügyeleti csoport szintjén

Az Azure-erőforrásokat általában az Azure-előfizetésében lévő erőforráscsoporthoz helyezheti üzembe. A felügyeleti csoport szintjén azonban erőforrásokat is létrehozhat. A felügyeleti csoport szintjén üzemelő példányok használatával olyan műveleteket hajthat végre, amelyek az adott szinten ésszerűek, például [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) hozzárendelésével vagy [házirendek](../governance/policy/overview.md)alkalmazásával.

Jelenleg a sablonok felügyeleti csoport szintjén történő telepítéséhez a REST API kell használnia.

## <a name="supported-resources"></a>Támogatott erőforrások

A felügyeleti csoport szintjén a következő erőforrástípusok helyezhetők üzembe:

* [központi telepítések](/azure/templates/microsoft.resources/deployments)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Séma

A felügyeleti csoportok központi telepítéséhez használt séma eltér az erőforráscsoport-telepítések sémájától.

Sablonok esetén használja a következőt:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

A paraméter fájljaihoz használja a következőt:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Üzembe helyezési parancsok

A felügyeleti csoport központi telepítésére vonatkozó parancs eltér az erőforráscsoport-telepítésekhez tartozó parancstól.

REST API esetén használja a [központi telepítéseket – hozzon létre egy felügyeleti csoport hatókörét](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Központi telepítés helye és neve

Felügyeleti csoport szintű központi telepítések esetén meg kell adnia egy helyet a központi telepítéshez. A központi telepítés helye nem azonos a telepített erőforrások helyétől. A központi telepítés helye határozza meg, hogy hol tárolja a telepítési adatforrásokat.

Megadhatja a központi telepítés nevét, vagy használhatja az alapértelmezett központi telepítési nevet is. Az alapértelmezett név a sablonfájl neve. Egy **azuredeploy. JSON** nevű sablon üzembe helyezése például létrehoz egy alapértelmezett központi telepítési nevet a **azuredeploy**.

Az egyes központi telepítési nevek esetében a hely nem módosítható. A központi telepítést nem lehet az egyik helyen létrehozni, ha egy másik helyen már van ilyen nevű üzemelő példány. Ha `InvalidDeploymentLocation`hibakódot kap, vagy más nevet vagy azonos helyet használ a korábbi üzembe helyezéshez a névben.

## <a name="use-template-functions"></a>A Template functions használata

A felügyeleti csoportok központi telepítéséhez néhány fontos szempontot kell figyelembe venni a sablon funkcióinak használatakor:

* A [resourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) függvény **nem** támogatott.
* Az [előfizetés ()](resource-group-template-functions-resource.md#subscription) függvény **nem** támogatott.
* A [resourceId ()](resource-group-template-functions-resource.md#resourceid) függvény támogatott. Ezzel a beállítással lekérheti a felügyeleti csoport szintjén üzemelő példányokon használt erőforrások erőforrás-AZONOSÍTÓját. Például szerezze be a házirend-definíció erőforrás-AZONOSÍTÓját `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`. Az erőforrás-azonosítót a `/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}`formátumban adja vissza.
* A [Reference ()](resource-group-template-functions-resource.md#reference) és a [List ()](resource-group-template-functions-resource.md#list) függvények támogatottak.

## <a name="create-policies"></a>Szabályzatok létrehozása

### <a name="define-policy"></a>Házirend megadása

Az alábbi példa bemutatja, hogyan [határozhat meg](../governance/policy/concepts/definition-structure.md) házirendet a felügyeleti csoport szintjén.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
        }
    ]
}
```

### <a name="assign-policy"></a>Házirend kiosztása

A következő példa egy meglévő szabályzat-definíciót rendel hozzá a felügyeleti csoporthoz. Ha a házirend paramétereket fogad, adja meg őket objektumként. Ha a házirend nem fogad paramétereket, használja az alapértelmezett üres objektumot.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```



## <a name="next-steps"></a>További lépések

* A szerepkörök hozzárendelésével kapcsolatos további tudnivalókért lásd: [Az Azure-erőforrásokhoz való hozzáférés kezelése RBAC és Azure Resource Manager sablonok használatával](../role-based-access-control/role-assignments-template.md).
* A Azure Security Center munkaterület-beállításainak üzembe helyezésére példát a következő témakörben talál: [deployASCwithWorkspaceSettings. JSON](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Azure Resource Manager sablonok létrehozásával kapcsolatos további tudnivalókért lásd: [sablonok készítése](resource-group-authoring-templates.md). 
* A sablonban elérhető függvények listáját itt tekintheti meg: [sablon függvények](resource-group-template-functions.md).