---
title: CredentialsCombo felhasználói felület eleme
description: A cikk a Microsoft.Compute.CredentialsCombo UI elem az Azure Portalon.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: baee83e08505ddabb4680b5bbb4101479010ea47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652137"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsKombinált felhasználói felületi elem

Vezérlők csoportja beépített érvényesítéssel windowsos és Linux os jelszavakhoz és SSH nyilvános kulcsokhoz.

## <a name="ui-sample"></a>Felhasználói felület minta

Windows esetén a felhasználók a következőket látják:

![Microsoft.Compute.CredentialsKombinált Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Linux esetén a kiválasztott jelszó, a felhasználók lásd:

![Microsoft.Compute.CredentialsKombinált Linux-jelszó](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Linux esetén az SSH nyilvános kulcs van kiválasztva, a felhasználók lásd:

![Microsoft.Compute.CredentialsCombo Linux kulcs](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Séma

Windows rendszerben használja a következő sémát:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

**Linux esetén**használja a következő sémát:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="sample-output"></a>Példa kimenet

Ha `osPlatform` **Windows**a Windows `osPlatform` , vagy **a Linux,** és a felhasználó megadott egy jelszót, hanem egy SSH nyilvános kulcs, a vezérlő a következő kimenetet adja vissza:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Ha `osPlatform` **Linux,** és a felhasználó megadott egy SSH nyilvános kulcsot, a vezérlő a következő kimenetet adja vissza:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="remarks"></a>Megjegyzések

- `osPlatform`meg kell adni, és lehet **Windows** vagy **Linux**.
- Ha `constraints.required` értéke **igaz,** akkor a jelszónak vagy az SSH nyilvános kulcsának szövegmezőinek értékekkel kell rendelkezniük a sikeres ellenőrzéshez. Az alapértelmezett érték **igaz**.
- Ha `options.hideConfirmation` értéke **igaz,** akkor a felhasználó jelszavának megerősítésére szolgáló második szövegdoboz rejtett. Az alapértelmezett érték: **hamis**.
- Ha `options.hidePassword` értéke **igaz,** akkor a jelszó-hitelesítés használata rejtett. Ezt csak akkor `osPlatform` lehet használni, ha a **Linux**. Az alapértelmezett érték: **hamis**.
- Az engedélyezett jelszavaktovábbi megkötései a `customPasswordRegex` tulajdonság használatával valósíthatók meg. A karakterlánc `customValidationMessage` akkor jelenik meg, ha egy jelszó nem felel meg az egyéni érvényesítésnek. Mindkét tulajdonság alapértelmezett értéke **null**.

## <a name="next-steps"></a>További lépések

* A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
* A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
