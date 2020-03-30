---
title: Az Azure Blueprints függvényei
description: Ismerteti a rendelkezésre álló funkciók at blueprint összetevők az Azure Blueprints definíciók és hozzárendelések.
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280676"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Az Azure Blueprints-szel használható funkciók

Az Azure Blueprints funkciókat biztosít, amelyek dinamikusabbá teszik a tervezetdefiníciót. Ezek a függvények tervdefiníciók és tervezetösszetevők használatával használhatók. A Resource Manager sablon műtermék támogatja a teljes körű erőforrás-kezelő ifüggvények mellett a dinamikus érték egy tervezet paraméteren keresztül.

A következő funkciók támogatottak:

- [Leletek](#artifacts)
- [Concat](#concat)
- [Paraméterek](#parameters)
- [erőforrásCsoport](#resourcegroup)
- [erőforráscsoportok](#resourcegroups)
- [előfizetést](#subscription)

## <a name="artifacts"></a>Leletek

`artifacts(artifactName)`

A tervezetösszetevő-összetevők kimenetével feltöltött tulajdonságok objektumát adja vissza.

> [!NOTE]
> A `artifacts()` függvény nem használható az Erőforrás-kezelő sablonon belülről. A függvény csak a blueprint definition JSON vagy a műtermék JSON a tervezet kezelése során az Azure PowerShell vagy rest API-t a [Blueprints-as-code](https://github.com/Azure/azure-blueprints/blob/master/README.md)részeként.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| műtermékneve |Igen |sztring |Egy tervrajz-lelet neve. |

### <a name="return-value"></a>Visszatérítési érték

Kimeneti tulajdonságok objektuma. A **kimeneti** tulajdonságok a hivatkozott tervezetösszetevő típusától függenek. Minden típus a következő formátumot követi:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Házirend-hozzárendelés im2-es terméke

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Erőforrás-kezelő sablonmű

A visszaadott objektum **kimeneti** tulajdonságai az Erőforrás-kezelő sablonban vannak definiálva, és a központi telepítés adja vissza.

#### <a name="role-assignment-artifact"></a>Szerepkör-hozzárendelési műtermék

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

Erőforrás-kezelő sablonmű az azonosító _sajító desåit tartalmazó myTemplateArtifact_ azonosítóval, amely a következő minta kimeneti tulajdonságot tartalmazza:

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

Néhány példa a _myTemplateArtifact_ mintából származó adatok beolvasására:

| Kifejezés | Típus | Érték |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Tömb | \["első", "második"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Sztring | "első" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Sztring | "a karakterlánc-értékem" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Objektum | { "myproperty": "az én érték", "anotherProperty": igaz } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Sztring | "az értékem" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Logikai | True (Igaz) |

## <a name="concat"></a>Concat

`concat(string1, string2, string3, ...)`

Több karakterlánc-értéket egyesít, és az összefont karakterláncot adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| karakterlánc1 |Igen |sztring |Az összefűzés első értéke. |
| további argumentumok |Nem |sztring |További értékek egymás után egymás után az összefűzéshez |

### <a name="return-value"></a>Visszatérítési érték

Összefont értékek sorozata.

### <a name="remarks"></a>Megjegyzések

Az Azure Blueprint függvény abban különbözik az Azure Resource Manager sablonfüggvénytől, hogy csak karakterláncokkal működik.

### <a name="example"></a>Példa

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>paraméterek

`parameters(parameterName)`

Egy tervezetparaméter-értéket ad eredményül. A megadott paraméternevet meg kell határozni a tervezet definíciójában vagy a tervezet összetevőiben.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| paraméternév |Igen |sztring |A visszaadandó paraméter neve. |

### <a name="return-value"></a>Visszatérítési érték

A megadott tervrajz vagy tervezetösszetevő paraméter értéke.

### <a name="remarks"></a>Megjegyzések

Az Azure Blueprint függvény abban különbözik az Azure Resource Manager sablonfüggvénytől, hogy csak tervezetparaméterekkel működik.

### <a name="example"></a>Példa

_Paraméter-rendszerazonosítók definiálása_ a tervezet definíciójában:

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

Ezután használja _a principalIds-t_ egy tervrajz-műtárgy argumentumaként: `parameters()`

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

Az aktuális erőforráscsoportot reprezentativó objektumot adja vissza.

### <a name="return-value"></a>Visszatérítési érték

A visszaadott objektum formátuma a következő:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Megjegyzések

Az Azure Blueprint függvény eltér az Azure Resource Manager sablon függvény. A `resourceGroup()` függvény nem használható előfizetési szintű összetevővagy a tervezet definíciójában. Csak olyan tervrajzok összetevőiben használható, amelyek egy erőforráscsoport-összetevő részét képezik.

A függvény gyakori `resourceGroup()` használata, hogy erőforrásokat hozzon létre ugyanazon a helyen, mint az erőforráscsoport-összetevő.

### <a name="example"></a>Példa

Az erőforráscsoport helyének használatához, amelyet a tervezetdefinícióban vagy a hozzárendelés során egy másik összetevő helyeként állít be, deklaráljon egy erőforráscsoport helyőrző objektumát a tervezetdefinícióban. Ebben a példában _a NetworkingPlaceholder_ az erőforráscsoport helyőrzőjének neve.

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

Ezután `resourceGroup()` használja a függvényt egy tervtervezet-összetevő környezetében, amely egy erőforráscsoport helyőrző objektumát célozza. Ebben a példában a sablon összetevő telepítve van a _NetworkingPlaceholder_ erőforráscsoportba, és biztosítja a _paraméter resourceLocation_ dinamikusan feltöltve a _NetworkingPlace erőforráscsoport_ helyét a sablonhoz. A _NetworkingPlaceholder_ erőforráscsoport helyét statikusan meghatározhatták a tervezetdefinícióban, vagy dinamikusan definiálták a hozzárendelés során. Mindkét esetben a sablon összetevő paraméterként szolgál, és az erőforrások megfelelő helyre történő üzembe helyezésére használja.

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

## <a name="resourcegroups"></a>erőforráscsoportok

`resourceGroups(placeholderName)`

A megadott erőforráscsoport-összetevőt reprezentativó objektumot adja vissza. A `resourceGroup()`, amely a műtermék környezetét igényli, ez a függvény egy adott erőforráscsoport helyőrzőjének tulajdonságainak leéséhez használatos, ha nem az adott erőforráscsoport környezetében.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| helyőrzőNeve |Igen |sztring |A visszaadandó erőforráscsoport-összetevő helyőrző neve. |

### <a name="return-value"></a>Visszatérítési érték

A visszaadott objektum formátuma a következő:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Példa

Az erőforráscsoport helyének használatához, amelyet a tervezetdefinícióban vagy a hozzárendelés során egy másik összetevő helyeként állít be, deklaráljon egy erőforráscsoport helyőrző objektumát a tervezetdefinícióban. Ebben a példában _a NetworkingPlaceholder_ az erőforráscsoport helyőrzőjének neve.

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

Ezután `resourceGroups()` használja a függvényt bármely tervezet összetevő környezetében az erőforráscsoport helyőrző objektumra való hivatkozás leszámításához. Ebben a példában a sablon összetevő a _NetworkingPlaceholder_ erőforráscsoporton kívül van telepítve, és a sablonhoz dinamikusan feltöltve biztosítja a _paraméterartifactLocation_ dinamikusan feltöltve a _NetworkingPlace erőforráscsoport_ helyével. A _NetworkingPlaceholder_ erőforráscsoport helyét statikusan meghatározhatták a tervezetdefinícióban, vagy dinamikusan definiálták a hozzárendelés során. Mindkét esetben a sablon összetevő paraméterként szolgál, és az erőforrások megfelelő helyre történő üzembe helyezésére használja.

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

## <a name="subscription"></a>előfizetést

`subscription()`

Az aktuális tervezet-hozzárendelés előfizetésének részleteit adja vissza.

### <a name="return-value"></a>Visszatérítési érték

A visszaadott objektum formátuma a következő:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Példa

Használja az előfizetés megjelenítendő `concat()` nevét és a függvényt, hogy hozzon létre egy elnevezési konvenció tãing _erőforrásnév_ paraméterként a sablon műtermék.

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

- További információ a [tervterv életciklusáról.](../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../how-to/update-existing-assignments.md)
- Az [általános hibaelhárítással](../troubleshoot/general.md)kapcsolatos tervtervezet ekhozzárendelése során fellépő problémák megoldása.