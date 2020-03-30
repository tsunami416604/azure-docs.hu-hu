---
title: TagsByResource felhasználói felületi elem
description: A cikk a Microsoft.Common.TagsByResource UI elemet ismerteti az Azure Portalon. Címkék alkalmazása egy erőforrásra a telepítés során.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652202"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Microsoft.Common.TagsByResource felhasználói felületi elem

A [címkék](../management/tag-resources.md) és a központi telepítés erőforrásainak társításának vezérlője.

## <a name="ui-sample"></a>Felhasználói felület minta

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

- A tömblegalább egy `resources` elemét meg kell adni.
- Minden elemnek `resources` teljesen minősített erőforrástípusnak kell lennie. Ezek az elemek megjelennek az **Erőforrás** legördülő menüben, és a felhasználó címkézhető.
- A vezérlő kimenete formázott egy Azure Resource Manager-sabloncímke-értékek egyszerű hozzárendelése érdekében. A vezérlő kimenetének sablonban való fogadásához adjon meg egy paramétert a sablonban az alábbi példában látható módon:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Minden címkézhető erőforráshoz rendelje hozzá a címkék tulajdonságot az adott erőforrástípus paraméterértékéhez:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- A tagsByResource paraméter elérésekor használja az [if](../templates/template-functions-logical.md#if) függvényt. Lehetővé teszi egy üres objektum hozzárendelését, ha nincs címke az adott erőforrástípushoz.

## <a name="next-steps"></a>További lépések

- A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
- A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
