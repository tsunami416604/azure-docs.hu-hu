---
title: "Rendelje hozzá, és az Azure erőforrás-házirendek kezelése |} Microsoft Docs"
description: "Az Azure erőforrás-házirendek alkalmazása előfizetésekhez és erőforráscsoportokhoz, és erőforrás-házirendekkel megtekintéséhez módját ismerteti."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: tomfitz
ms.openlocfilehash: 64bdd6ed41e98079c8d4112e895aaeddcd629282
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="assign-and-manage-resource-policies"></a>Rendelje hozzá, és erőforrás-házirendek kezelése

A házirend megvalósítása, hajtsa végre ezeket a lépéseket:

1. Ellenőrizze a házirend-definíciók száma (beleértve az Azure által biztosított beépített házirendek) az egyik már létezik-e az előfizetés, amely megfelel a követelményeknek.
2. Ha van ilyen, annak neve olvasható.
3. Ha még nem létezik, adja meg a házirend JSON szabály, és adja hozzá az előfizetés a házirend-definíció. Ebben a lépésben elérhetővé teszi a házirend a hozzárendeléshez, de nem felel meg a szabályokat az előfizetéséhez.
4. Mindkét esetben rendelje hozzá a házirendet (például egy előfizetés vagy az erőforrás csoport) hatókörbe. A szabályok a házirend kényszerítése megtörtént.

Ez a cikk foglalkozik a házirend-definíció létrehozása, és rendelje hozzá, hogy definíció REST API-t, a PowerShell vagy az Azure CLI hatókör lépéseket. Ha inkább a portál használatával rendelje hozzá a házirendek, lásd: [hozzárendelésére és kezelésére erőforrás-házirendek használata Azure-portálon](resource-manager-policy-portal.md). Ez a cikk nem a szintaxist a házirend-definíció létrehozására összpontosít. Házirendet a szintaxissal kapcsolatos információkért lásd: [erőforrás házirendek – áttekintés](resource-manager-policy.md).

## <a name="exclusion-scopes"></a>Kizárási hatókörök

A hatókör kizárhatja a házirend hozzárendelésekor. Ez a lehetőség egyszerűbbé teszi házirend-hozzárendelést, mert az előfizetés szintjén egy házirendet, de adja meg, ahol a rendszer nem alkalmazza a házirendet. Például az előfizetéshez, akkor egy erőforráscsoport, amely a hálózati infrastruktúra számára készült. Alkalmazásokat fejlesztő csapatoknak együtt az erőforrások más erőforrás csoportjára is alkalmazhatja. Nem szeretné, hogy a csoportok létrehozása a hálózati erőforrásokhoz, amely biztonsági problémákat okozhat. Azonban a hálózati erőforráscsoportban is engedélyezni szeretné hálózati erőforrásokhoz. A házirendet, az előfizetés szintjén, de a hálózati erőforráscsoport kizárása. Megadhatja, hogy több sub hatókör.

```json
{
    "properties":{
        "policyDefinitionId":"<ID for policy definition>",
        "notScopes":[
            "/subscriptions/<subid>/resourceGroups/networkresourceGroup1"
        ]
    }
}
```

A hozzárendelés egy kizárási hatókört ad meg, ha a **2017 06-01. dátumú előnézeti** API-verzió.

## <a name="rest-api"></a>REST API

### <a name="create-policy-definition"></a>Házirend-definíció létrehozása

A házirendet hozhat létre a [REST API-t a házirend-definíciók](/rest/api/resources/policydefinitions). A REST API lehetővé teszi létrehozása és törlése a házirend-definíciók és meglévő definíciók adatainak beolvasása.

Házirend-definíció létrehozásához futtassa:

```HTTP
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Az alábbi példához hasonló egy kérelemtörzset a következők:

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="assign-policy"></a>Házirend hozzárendelése

A házirend-definíció a kívánt hatóköre keresztül is alkalmazhat a [REST API-t a házirend-hozzárendelések](/rest/api/resources/policyassignments). A REST API lehetővé teszi, hogy hozhat létre és a házirend-hozzárendelések törlése és a meglévő hozzárendelés adatainak beolvasása.

Házirend-hozzárendelés létrehozásához futtassa:

```HTTP
PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}
```

A {házirend-hozzárendelés} a házirend-hozzárendelés neve.

Az alábbi példához hasonló egy kérelemtörzset a következők:

```json
{
  "properties":{
    "displayName":"West US only policy assignment on the subscription ",
    "description":"Resources can only be provisioned in West US regions",
    "parameters": {
      "allowedLocations": { "value": ["northeurope", "westus"] }
     },
    "policyDefinitionId":"/subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}",
      "scope":"/subscriptions/{subscription-id}"
  },
}
```

### <a name="view-policy"></a>Házirend megtekintése
A házirend beszerzéséhez használja a [házirend-definíció beolvasása](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get) műveletet.

### <a name="get-aliases"></a>Aliasok beolvasása
A REST API-n keresztül aliasok le:

```HTTP
GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
```

Az alábbi példában az alias definíciójának. Ahogy látja, alias határoz meg elérési utak a különböző API-verziók, akkor is, amikor egy tulajdonság nevének módosítása. 

```json
"aliases": [
    {
      "name": "Microsoft.Storage/storageAccounts/sku.name",
      "paths": [
        {
          "path": "properties.accountType",
          "apiVersions": [
            "2015-06-15",
            "2015-05-01-preview"
          ]
        },
        {
          "path": "sku.name",
          "apiVersions": [
            "2016-01-01"
          ]
        }
      ]
    }
]
```

## <a name="powershell"></a>PowerShell

A PowerShell-példákkal a folytatás előtt győződjön meg arról, hogy [telepítve a legújabb verzió](/powershell/azure/install-azurerm-ps) az Azure PowerShell. Házirend-paraméterek 3.6.0 verzióban lettek hozzáadva. Ha egy korábbi, a példák, hibaüzenetet jelzi, hogy a paraméter nem található.

### <a name="view-policy-definitions"></a>Házirend-definíciók megtekintése
Az előfizetés az összes házirend-definíciók, használja a következő parancsot:

```powershell
Get-AzureRmPolicyDefinition
```

Összes elérhető, házirend-beállítást, többek között beépített házirendek adja vissza. Minden egyes házirend eredmény abban az esetben a következő formátumban:

```powershell
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

Mielőtt továbblép a házirend-definíció létrehozása, tekintse meg a beépített házirendek. Ha egy beépített házirendet kell korlátok érvényes, kihagyhatja a házirend-definíció létrehozása. Ehelyett a beépített házirend hozzárendelése a kívánt hatóköre.

### <a name="create-policy-definition"></a>Házirend-definíció létrehozása
A szabályzat definíciója használatával hozhat létre a `New-AzureRmPolicyDefinition` parancsmag.

Házirend-definíció létrehozása fájlból, adja át az elérési út a fájlhoz. Egy külső fájlt használja:

```powershell
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Helyi fájl esetén használja:

```powershell
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Házirend-definíció létrehozásához egy beágyazott szabállyal használja:

```powershell
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```            

A kimeneti tárolja egy `$definition` objektum, amely házirend-hozzárendelés során használatos. 

Az alábbi példakód létrehozza a házirend-definíció paramétereket tartalmaz:

```powershell
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}' 

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters 
```

### <a name="assign-policy"></a>Házirend hozzárendelése

A szabályzatot a kívánt hatókörben futtatásával kell alkalmazni a `New-AzureRmPolicyAssignment` parancsmag. A következő példa egy erőforráscsoportot a szabályzat rendeli.

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
New-AzureRMPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Rendelje hozzá egy házirendet, mely paraméterek szükségesek, hozzon létre, és ezen értékekkel rendelkező objektum. Az alábbi példa lekérdezi a beépített házirend, és átadja a paraméterek értékeit:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
$array = @("West US", "West US 2")
$param = @{"listOfAllowedLocations"=$array}
New-AzureRMPolicyAssignment -Name locationAssignment -Scope $rg.ResourceId -PolicyDefinition $definition -PolicyParameterObject $param
```

### <a name="view-policy-assignment"></a>Házirend-hozzárendelés megtekintése

Egy adott házirend-hozzárendelés használatához:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId
```

Házirend-definíció a házirend-szabálya megtekintéséhez használja:

```powershell
(Get-AzureRmPolicyDefinition -Name coolAccessTier).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>Távolítsa el a házirend-hozzárendelés 

Házirend-hozzárendelés eltávolításához használja:

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli"></a>Azure CLI

### <a name="view-policy-definitions"></a>Házirend-definíciók megtekintése
Az előfizetés az összes házirend-definíciók, használja a következő parancsot:

```azurecli
az policy definition list
```

Összes elérhető, házirend-beállítást, többek között beépített házirendek adja vissza. Minden egyes házirend eredmény abban az esetben a következő formátumban:

```azurecli
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

Mielőtt továbblép a házirend-definíció létrehozása, tekintse meg a beépített házirendek. Ha egy beépített házirendet kell korlátok érvényes, kihagyhatja a házirend-definíció létrehozása. Ehelyett a beépített házirend hozzárendelése a kívánt hatóköre.

### <a name="create-policy-definition"></a>Házirend-definíció létrehozása

A házirend-definíció a házirend-definíció parancs az Azure parancssori felület használatával hozhat létre.

Házirend-definíció létrehozásához egy beágyazott szabállyal használja:

```azurecli
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'    
```

### <a name="assign-policy"></a>Házirend hozzárendelése

A házirend-hozzárendelés paranccsal alkalmazhatja a szabályzatot a kívánt hatókörbe. A következő példa egy házirend rendel egy erőforráscsoportot.

```azurecli
az policy assignment create --name coolAccessTierAssignment --policy coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>Házirend-hozzárendelés megtekintése

Házirend-hozzárendelés megtekinteni, adja meg a házirend-hozzárendelés neve és a hatókör:

```azurecli
az policy assignment show --name coolAccessTierAssignment --scope "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}"
```

### <a name="remove-policy-assignment"></a>Távolítsa el a házirend-hozzárendelés 

Házirend-hozzárendelés eltávolításához használja:

```azurecli
az policy assignment delete --name coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>Következő lépések
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

