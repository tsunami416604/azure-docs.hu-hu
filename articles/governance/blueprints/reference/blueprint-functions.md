---
title: Tervezetek az Azure functions
description: Ismerteti a functions Azure tervezetek definíciókat és hozzárendeléseket való használatra.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dc72113a8f5ed978d64d35c43e94dc9e19e4cdb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209412"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Functions Azure tervezetek való használatra

Azure tervezetek így egy tervezetdefiníció több dinamikus funkciókat biztosít. Ezek a függvények tervezetdefiníciók való használatra, és összetevők tervezetet. Egy Resource Manager-sablon összetevő támogatja a teljes erőforrás-kezelő funkciók mellett egy tervezetparaméter keresztül egy dinamikus érték beolvasása.

Az alábbi funkciókat támogatja:

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscription](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

Visszaadja, hogy tervezetösszetevők kimenetek egy objektumának tulajdonságait feltöltve.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| artifactName |Igen |string |A tervezet-összetevő neve. |

### <a name="return-value"></a>Vrácená hodnota

Kimeneti tulajdonságok objektum. A **kimenete** a tulajdonságok akkor függ a tervezet lehívandó összetevő hivatkozik rá. Minden típus hajtsa végre a formátum:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>A házirend-hozzárendelési összetevője

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Resource Manager-sablon összetevő

A **kimenete** a visszaadott objektum tulajdonságainak belül a Resource Manager-sablont, és a központi telepítés által visszaadott.

#### <a name="role-assignment-artifact"></a>Szerepkör-hozzárendelési összetevője

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Példa

A Resource Manager-sablon összetevő azonosítójú _myTemplateArtifact_ tulajdonság a következő mintát tartalmazó kimeneti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Néhány példa az adatok beolvasása a _myTemplateArtifact_ minta vannak:

| kifejezés | Típus | Érték |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Tömb | \["first", "másodperc"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "first" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "a karakterlánc-érték" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | {"myproperty": "saját érték", "anotherProperty": true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | a "value" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True (Igaz) |

## <a name="concat"></a>Concat

`concat(string1, string2, string3, ...)`

Több karakterlánc-értékek egyesíti, és a összefűzött karakterláncot ad vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| string1 |Igen |string |Összefűzés első értéke. |
| További argumentumok |Nem |string |További értékek összefűzésével sorrendben |

### <a name="return-value"></a>Vrácená hodnota

Értékek összefűzött karakterláncot.

### <a name="remarks"></a>Megjegyzések

Az Azure tervezet függvény az Azure Resource Manager-sablon függvény különbözik, hogy csak a karakterláncok működik.

### <a name="example"></a>Példa

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

A tervezet paraméter értékét adja vissza. A megadott paraméternév definiálni kell a tervezetdefiníciót vagy tervezetösszetevők.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| parameterName |Igen |string |A visszaadandó paraméter neve. |

### <a name="return-value"></a>Vrácená hodnota

A megadott tervezet vagy összetevő tervezetparaméter értéke.

### <a name="remarks"></a>Megjegyzések

A tervezet Azure-függvényt az Azure Resource Manager-sablon függvény különbözik, hogy csak a tervezet paraméterekkel működik.

### <a name="example"></a>Példa

Adja meg a paraméter _principalIds_ a tervezet-definícióban:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Ezután _principalIds_ argumentumként `parameters()` a tervezet-összetevőben:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Az aktuális erőforráscsoport képviselő objektumot adja vissza.

### <a name="return-value"></a>Vrácená hodnota

A visszaadott objektum a következő formátumban kell megadni:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Megjegyzések

A tervezet Azure-függvényt az Azure Resource Manager-sablon függvény eltér. A `resourceGroup()` függvény nem használható egy előfizetés-szintű összetevő vagy a tervezetdefiníciót. Csak használható egy erőforrás-csoport összetevő részét képező tervezetösszetevők a.

Egyik gyakori felhasználási a `resourceGroup()` funkciója, hogy erőforrásokat hozzon létre az erőforrás-csoport összetevő ugyanazon a helyen.

### <a name="example"></a>Példa

Az erőforráscsoport helyét, akár állítsa be a tervezetdefiníciót, vagy egy másik szerelvényösszetevő helyeként a hozzárendelés során deklarálja a tervezetdefiníciót egy erőforrás-csoport helyőrző objektumot. Ebben a példában _NetworkingPlaceholder_ az erőforrás-csoport helyőrző neve.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Ezután a `resourceGroup()` függvény, amely egy erőforrás helyőrző objektum van-e állítva a tervezet összetevő kontextusában. Ebben a példában a sablon összetevő helyezi üzembe a _NetworkingPlaceholder_ erőforráscsoportot és a paraméter biztosít _resourceLocation_ dinamikusan ki lesz töltve a  _NetworkingPlaceholder_ erőforráscsoport helye a sablonhoz. A helyét a _NetworkingPlaceholder_ erőforráscsoport sikerült a tervezetdefiníció statikusan meghatározott vagy dinamikusan definiálva a hozzárendelés során. Mindkét esetben a sablon összetevő ezt az információt a paraméterként megadott, és használja az erőforrásokat üzembe kívánja a megfelelő helyre.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

A megadott erőforrás-csoport összetevő képviselő objektumot adja vissza. Ellentétben `resourceGroup()`, ami megköveteli, hogy a lehívandó összetevő környezetben, ezt a funkciót használja, ha nem az erőforráscsoport kontextusában adott erőforrás csoport helyőrző tulajdonságainak beolvasása.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| placeholderName |Igen |string |Az erőforrás csoport lehívandó összetevő visszaadása a helyőrző neve. |

### <a name="return-value"></a>Vrácená hodnota

A visszaadott objektum a következő formátumban kell megadni:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Példa

Az erőforráscsoport helyét, akár állítsa be a tervezetdefiníciót, vagy egy másik szerelvényösszetevő helyeként a hozzárendelés során deklarálja a tervezetdefiníciót egy erőforrás-csoport helyőrző objektumot. Ebben a példában _NetworkingPlaceholder_ az erőforrás-csoport helyőrző neve.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Ezután a `resourceGroups()` függvény bármely tervezet lehívandó összetevő az erőforrás-csoport helyőrző objektumra mutató hivatkozás beszerzése a környezetből. Ebben a példában a sablon összetevő kívül üzemel a _NetworkingPlaceholder_ erőforráscsoportot és a paraméter biztosít _artifactLocation_ dinamikusan ki lesz töltve a  _NetworkingPlaceholder_ erőforráscsoport helye a sablonhoz. A helyét a _NetworkingPlaceholder_ erőforráscsoport sikerült a tervezetdefiníció statikusan meghatározott vagy dinamikusan definiálva a hozzárendelés során. Mindkét esetben a sablon összetevő ezt az információt a paraméterként megadott, és használja az erőforrásokat üzembe kívánja a megfelelő helyre.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>subscription

`subscription()`

Az aktuális tervezet-hozzárendelés az előfizetés részleteit adja vissza.

### <a name="return-value"></a>Vrácená hodnota

A visszaadott objektum a következő formátumban kell megadni:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Példa

Az előfizetéshez tartozó megjelenített nevet, és a `concat()` függvény létrehozása a paraméterként elnevezési _resourceName_ az a sablon összetevőkben.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).