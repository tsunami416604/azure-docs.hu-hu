---
title: Az Azure CredentialsCombo felhasználói felületének eleme | Microsoft Docs
description: Ismerteti a Microsoft. számítás. CredentialsCombo felhasználói felületi elemét Azure Portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: c1b4791f2ec80eba25a00e22cb4298b4c97da4de
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151081"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI element

Windows-és Linux-jelszavakhoz, valamint nyilvános SSH-kulcsokhoz beépített ellenőrzési csoport.

## <a name="ui-sample"></a>Felhasználói felület mintája

Windows esetén a felhasználók a következőket látják:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

A Linux és a jelszó kiválasztásával a felhasználók a következőket látják:

![Microsoft. számítás. CredentialsCombo Linux-jelszó](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

A Linux és a nyilvános SSH-kulcs kiválasztásával a felhasználók a következőket látják:

![Microsoft. számítás. CredentialsCombo Linux-kulcs](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Séma

Windows esetén használja a következő sémát:

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

**Linux**esetén használja a következő sémát:

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

Ha `osPlatform` **Windows**, vagy `osPlatform` **Linux** , és a felhasználó egy nyilvános SSH-kulcs helyett jelszót adott meg, a vezérlő a következő kimenetet adja vissza:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Ha `osPlatform` **Linux** , és a felhasználó egy nyilvános SSH-kulcsot adott meg, a vezérlő a következő kimenetet adja vissza:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="remarks"></a>Megjegyzések

- `osPlatform` meg kell adni, és lehet **Windows** vagy **Linux**.
- Ha a `constraints.required` értéke **true (igaz**), akkor a jelszónak vagy az SSH nyilvános kulcs szövegmezőknek megfelelő értékekkel kell rendelkezniük a sikeres ellenőrzéshez. Az alapértelmezett érték **true (igaz**).
- Ha `options.hideConfirmation` értéke TRUE ( **igaz**), akkor a felhasználó jelszavának megerősítésére szolgáló második szövegmező rejtve marad. Az alapértelmezett érték **hamis**.
- Ha a `options.hidePassword` értéke **true (igaz**), akkor a jelszó-hitelesítés használatának lehetősége rejtett. A szolgáltatás csak akkor használható, ha `osPlatform` **Linux**. Az alapértelmezett érték **hamis**.
- Az engedélyezett jelszavakkal kapcsolatos további korlátozásokat a `customPasswordRegex` tulajdonság használatával lehet megvalósítani. A `customValidationMessage`ban szereplő karakterlánc akkor jelenik meg, ha a jelszó egyéni érvényesítést jelez. Mindkét tulajdonság alapértelmezett értéke **Null**.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
