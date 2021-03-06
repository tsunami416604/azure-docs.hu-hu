---
title: Szövegmező felhasználói felületének eleme
description: Ismerteti a Microsoft. Common. szövegmező felhasználói felületének a Azure Portalhoz tartozó elemét. Formázatlan szöveg hozzáadására használható.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c89bc434d9d67144a95b5c2f23e7664078fe7825
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87474308"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft. Common. szövegdoboz felhasználói felületének eleme

Formázatlan szöveg szerkesztésére szolgáló vezérlőelem.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textbox.png)

## <a name="schema"></a>Séma

```json
{
    "name": "nameInstance",
    "type": "Microsoft.Common.TextBox",
    "label": "Name",
    "defaultValue": "contoso123",
    "toolTip": "Use only allowed characters",
    "placeholder": "",
    "constraints": {
        "required": true,
        "validations": [
            {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
            },
            {
                "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
                "message": "Must start with 'contoso'."
            }
        ]
    },
    "visible": true
}
```

## <a name="sample-output"></a>Példakimenet

```json
"contoso123"
```

## <a name="remarks"></a>Megjegyzések

- Ha a `constraints.required` értéke **true (igaz**), akkor a szövegmezőnek rendelkeznie kell egy, a sikeres ellenőrzéshez szükséges értékkel. Az alapértelmezett érték: **hamis**.
- A `validations` tulajdonság egy olyan tömb, amelyben a szövegmezőben megadott érték ellenőrzéséhez feltételeket adhat hozzá.
- A `regex` tulajdonság egy JavaScript reguláris kifejezési minta. Ha meg van adva, akkor a szövegmező értékének meg kell egyeznie a sikeres ellenőrzéshez szükséges mintázattal. Az alapértelmezett érték **Null**.
- A `isValid` tulajdonság olyan kifejezést tartalmaz, amely igaz vagy hamis értéket ad vissza. A kifejezésen belül meg kell határoznia azt a feltételt, amely meghatározza, hogy a szövegmező érvényes-e.
- A `message` tulajdonság egy karakterlánc, amely akkor jeleníthető meg, ha a szövegmező értéke sikertelen az ellenőrzés során.
- Ha a értéke `regex` `required` false ( **hamis**), megadható az érték. Ebben az esetben nincs szükség értékre a szövegmező sikeres ellenőrzéséhez. Ha meg van adva, meg kell egyeznie a reguláris kifejezési mintával.
- A `placeholder` tulajdonság egy olyan súgószöveg, amely eltűnik, amikor a felhasználó megkezdi a szerkesztést. Ha a `placeholder` és `defaultValue` mindkettő is meg van adva, a `defaultValue` veszi elsőbbséget, és megjelenik.

## <a name="example"></a>Példa

Az alábbi példa egy szövegmezőt használ a [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md) vezérlővel az erőforrás-név rendelkezésre állásának ellenőrzéséhez.

```json
"basics": [
    {
        "name": "nameApi",
        "type": "Microsoft.Solutions.ArmApiControl",
        "request": {
            "method": "POST",
            "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
            "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
        }
    },
    {
        "name": "txtStorageName",
        "type": "Microsoft.Common.TextBox",
        "label": "Storage account name",
        "constraints": {
            "validations": [
                {
                    "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
                    "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
                }
            ]
        }
    }
]
```

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
