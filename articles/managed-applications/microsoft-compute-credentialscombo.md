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
ms.openlocfilehash: 3036e5882e236dbbb9cf4f9aae17617822422a82
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742106"
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

## <a name="remarks"></a>Megjegyzések
- `osPlatform`kötelező megadni, és lehet **Windows** vagy **Linux**.
- Ha `constraints.required` a értéke **true (igaz**), akkor a jelszónak vagy az SSH nyilvános kulcs szövegmezőknek megfelelő értékekkel kell rendelkezniük a sikeres ellenőrzéshez. Az alapértelmezett érték **true (igaz**).
- Ha `options.hideConfirmation` a értéke **true (igaz**), a rendszer elrejti a felhasználó jelszavának megerősítésére szolgáló második szövegmezőt. Az alapértelmezett érték **hamis**.
- Ha `options.hidePassword` a értéke **true (igaz**), akkor a jelszó-hitelesítés használatának lehetősége rejtett. A szolgáltatás csak akkor használható, `osPlatform` ha a **Linux**. Az alapértelmezett érték **hamis**.
- Az engedélyezett jelszavakkal kapcsolatos további korlátozásokat a `customPasswordRegex` tulajdonság használatával lehet megvalósítani. A karakterlánc `customValidationMessage` akkor jelenik meg, ha egy jelszó egyéni érvényesítése sikertelen. Mindkét tulajdonság alapértelmezett értéke **Null**.

## <a name="sample-output"></a>Példa kimenet
Ha `osPlatform` a **Windows**vagy `osPlatform` **Linux** , és a felhasználó egy nyilvános SSH-kulcs helyett jelszót adott meg, a vezérlő a következő kimenetet adja vissza:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Ha `osPlatform` a **Linux** és a felhasználó egy nyilvános SSH-kulcsot adott meg, a vezérlő a következő kimenetet adja vissza:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>További lépések
* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
