---
title: TagsByResource FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Common. TagsByResource felhasználói felületi elemének ismertetése. A használatával címkéket alkalmazhat az erőforrásokra az üzembe helyezés során.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652202"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Microsoft. Common. TagsByResource felhasználói felületi elem

A [címkék](../management/tag-resources.md) egy központi telepítésben lévő erőforrásokkal való társítására szolgáló vezérlő.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>Példa kimenet

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>Megjegyzések

- A `resources` tömb legalább egy elemét meg kell adni.
- A `resources` minden elemének teljesen minősített erőforrástípus kell lennie. Ezek az elemek az **erőforrás** legördülő menüben jelennek meg, és a felhasználó taggable.
- A vezérlő kimenete úgy van formázva, hogy könnyen lehessen hozzárendelni a címke értékeit egy Azure Resource Manager sablonban. Ha a vezérlő kimenetét szeretné fogadni egy sablonban, adjon meg egy paramétert a sablonban az alábbi példában látható módon:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Minden megcímkézhető erőforráshoz rendelje hozzá a címkék tulajdonságot az erőforrástípus paraméterének értékéhez:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Használja az [IF](../templates/template-functions-logical.md#if) függvényt a tagsByResource paraméter elérésekor. Lehetővé teszi, hogy üres objektumot rendeljen hozzá, ha nem rendel hozzá címkéket a megadott erőforrás-típushoz.

## <a name="next-steps"></a>További lépések

- A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
- A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
