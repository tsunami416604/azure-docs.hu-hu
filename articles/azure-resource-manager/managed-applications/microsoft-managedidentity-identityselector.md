---
title: IdentitySelector felhasználói felület i
description: A Microsoft.ManagedIdentity.IdentitySelector UI elem ismertetése az Azure Portalon. Felügyelt identitások hozzárendelése egy erőforráshoz.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087543"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft.ManagedIdentity.IdentitySelector felhasználói felület i elem

Felügyelt identitások hozzárendelésének [vezérlőja](../../active-directory/managed-identities-azure-resources/overview.md) egy központi telepítésben lévő erőforráshoz.

## <a name="ui-sample"></a>Felhasználói felület minta

A vezérlő a következő elemekből áll:

![Microsoft.ManagedIdentity.IdentitySelector első lépés](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

Amikor a felhasználó a **Hozzáadás**lehetőséget választja, megnyílik a következő űrlap. A felhasználó kiválaszthat egy vagy több felhasználó által hozzárendelt identitást az erőforráshoz.

![Microsoft.ManagedIdentity.IdentitySelector második lépés](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

A kijelölt identitások megjelennek a táblában. A felhasználó elemeket vehet fel vagy törölhet ebből a táblából.

![Microsoft.ManagedIdentity.IdentitySelector harmadik lépés](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

## <a name="schema"></a>Séma

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Példa kimenet

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Megjegyzések

- A **defaultValue.systemAssignedIdentity** használatával állítsa be a rendszer hozzárendelt identitásbeállításainak vezérlőjének kezdeti értékét. Az alapértelmezett érték **Ki**. A következő értékek megengedettek:
  - **Be** – A rendszer hozzárendelt identitás van rendelve az erőforráshoz.
  - **Ki** – a rendszer hozzárendelt identitás nincs hozzárendelve az erőforráshoz.
  - **OnOnly** – Az erőforráshoz rendszerhez rendelt identitás van hozzárendelve. A felhasználók nem szerkeszthetik ezt az értéket a telepítés során.
  - **OffOnly** – a rendszer hozzárendelt identitás nincs hozzárendelve az erőforráshoz. A felhasználók nem szerkeszthetik ezt az értéket a telepítés során.

- Ha **az options.hideSystemAssignedIdentity** értéke **igaz,** a rendszer hozzárendelt identitásának konfigurálásához szükséges felhasználói felület nem jelenik meg. A beállítás alapértelmezett értéke **hamis.**
- Ha **az options.hideUserAssignedIdentity** értéke **igaz,** a felhasználó által hozzárendelt identitás konfigurálásához szükséges felhasználói felület nem jelenik meg. Az erőforráshoz nincs hozzárendelve felhasználóhoz rendelt identitás. A beállítás alapértelmezett értéke **hamis.**

## <a name="next-steps"></a>További lépések

- A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
- A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.