---
title: Legördülő KEZELŐFELÜLETi elem
description: A cikk a Azure Portal Microsoft. Common. DropDown felhasználói felületi elemét ismerteti. A használatával választható lehetőségek közül választhat a felügyelt alkalmazások telepítésekor.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: f5eac1d331bd439ad4066d1dea1b9aa950fcce60
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004456"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft. Common. DropDown felhasználói felületi elem

Egy legördülő listával rendelkező kiválasztási vezérlő. Csak egyetlen vagy több elem kijelölését engedélyezheti. Igény szerint az elemekhez leírást is megadhat.

## <a name="ui-sample"></a>Felhasználói felület mintája

A legördülő elem különböző lehetőségeket tartalmaz, amelyek meghatározzák a megjelenést a portálon.

Ha csak egyetlen elem van engedélyezve a kijelöléshez, a vezérlő a következőképpen jelenik meg:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Microsoft. Common. DropDown egyetlen kijelölés":::

Ha a leírások szerepelnek, a vezérlő a következőképpen jelenik meg:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Microsoft. Common. DropDown egyetlen kijelölés a leírásokkal":::

Ha a többszörös kijelölés engedélyezve van, a vezérlő hozzáadja az **összes kijelölése** lehetőséget és a jelölőnégyzeteket több elem kiválasztásához:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Microsoft. Common. DropDown többszörös kijelölés":::

A többszörös kijelölés engedélyezése esetén a leírások is szerepelhetnek.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Microsoft. Common. DropDown többszörös kijelölés a leírásokkal":::

Ha a Szűrés engedélyezve van, a vezérlő tartalmaz egy szövegmezőt a szűrési érték hozzáadásához.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Microsoft. Common. DropDown többszörös kijelölés a leírásokkal":::

## <a name="schema"></a>Séma

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
                "value": "two"
            }
        ],
        "required": true
    },
    "visible": true
}
```

## <a name="sample-output"></a>Példa kimenet

```json
"two"
```

## <a name="remarks"></a>Megjegyzések

- Ezzel `multiselect` a beállítással adhatja meg, hogy a felhasználók kijelölhetnek-e egynél több elemet.
- Alapértelmezés szerint `selectAll` a `true` többszörös kijelölés engedélyezve van.
- A `filter` tulajdonság lehetővé teszi a felhasználók számára, hogy a lehetőségek hosszú listáján belül keressenek.
- A címkéje `constraints.allowedValues` az elem megjelenített szövege, a értéke pedig az elem kimeneti értéke, ha ki van választva.
- Ha meg van adva, az alapértelmezett értéknek szerepelnie kell a címkében `constraints.allowedValues` . Ha nincs megadva, az első elem `constraints.allowedValues` van kiválasztva. Az alapértelmezett érték **Null**.
- `constraints.allowedValues`legalább egy elemmel kell rendelkeznie.
- Ahhoz, hogy egy értéket ne lehessen emulálni, adjon hozzá egy címkével ellátott és `""` (üres karakterlánc) értéket a következőhöz: `constraints.allowedValues` .
- A `defaultDescription` tulajdonság olyan elemekhez használatos, amelyek nem rendelkeznek leírással.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
