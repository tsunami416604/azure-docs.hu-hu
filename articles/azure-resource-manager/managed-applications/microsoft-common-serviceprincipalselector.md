---
title: ServicePrincipalSelector FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Common. ServicePrincipalSelector felhasználói felületi elemének ismertetése. Legördülő lista segítségével kiválaszthatja az alkalmazás azonosítóját és a szövegmezőt a jelszó vagy a tanúsítvány ujjlenyomatának beviteléhez.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: tomfitz
ms.openlocfilehash: 73b242754bfae53b6df5abd9c2c8dee33b973dad
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575996"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Microsoft. Common. ServicePrincipalSelector felhasználói felületi elem

Olyan vezérlő, amely lehetővé teszi, hogy a felhasználók kiválasszon egy meglévő szolgáltatásnevet, vagy regisztráljanak egy újat. Ha az **új létrehozása**lehetőséget választja, egy új alkalmazás regisztrálásának lépésein végezheti el. Egy meglévő alkalmazás kiválasztásakor a vezérlő szövegmezőt biztosít a jelszó vagy a tanúsítvány ujjlenyomatának beviteléhez.

## <a name="ui-sample"></a>Felhasználói felület mintája

Az alapértelmezett nézetet a tulajdonság értékei határozzák meg `defaultValue` . Ha a `principalId` tulajdonság érvényes globálisan egyedi azonosítót (GUID) tartalmaz, a vezérlő megkeresi az alkalmazás objektum-azonosítóját. Az alapértelmezett érték akkor érvényes, ha a felhasználó nem végez kijelölést a legördülő listából.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-initial.png" alt-text="Microsoft. Common. ServicePrincipalSelector kezdeti nézet":::

Ha az **új létrehozása** vagy egy meglévő alkalmazás-azonosító kijelölése a legördülő listából lehetőséget választja, a **Hitelesítés típusa** megjelenik a jelszó vagy a tanúsítvány ujjlenyomatának megadásához a szövegmezőben.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-selection.png" alt-text="Microsoft. Common. ServicePrincipalSelector kezdeti nézet":::

## <a name="schema"></a>Séma

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Megjegyzések

- A szükséges tulajdonságok a következők:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Az alapértelmezett és a értéket adja meg `principalId` `name` .

- A választható tulajdonságok a következők:
  - `toolTip`: Elemleírást csatol `infoBalloon` az egyes címkékhez.
  - `visible`: A vezérlő elrejtése vagy megjelenítése.
  - `options`: Megadja, hogy elérhető legyen-e a Tanúsítvány ujjlenyomata beállítás.
  - `constraints`: A jelszó-ellenőrzés regex-megkötései.

## <a name="example"></a>Példa

Az alábbi példa a `Microsoft.Common.ServicePrincipalSelector` vezérlőt szemlélteti. A `defaultValue` tulajdonság az `principalId` `<default guid>` alapértelmezett alkalmazásspecifikus azonosító GUID azonosítójának helyőrzőként való beállítására szolgál.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Példa kimenetre

A a `appId` kiválasztott vagy létrehozott alkalmazás-regisztráció azonosítója. A a `objectId` kiválasztott objectIds konfigurált egyszerű szolgáltatások tömbje.

Ha a legördülő listából nem készül kijelölés, a `newOrExisting` tulajdonság értéke **új**:

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

Az **új létrehozásakor** vagy egy meglévő alkalmazás azonosítójának kiválasztásakor a `newOrExisting` tulajdonság értéke **meglévő**:

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>További lépések

- A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
- A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
