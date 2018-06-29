---
title: Az Azure CredentialsCombo felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Compute.CredentialsCombo felhasználói felületi elem ismerteti.
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
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 183075f7407b0a0ca6ea53871e239ab8c2d89490
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098620"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo felhasználói felületi elem
A Windows és Linux jelszavak és a nyilvános SSH-kulcsok beépített érvényesítéssel vezérlők egy csoportja.

## <a name="ui-sample"></a>Felhasználói felület minta

A Windows a felhasználók lásd:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

A kiválasztott jelszóval Linux felhasználók lásd:

![Microsoft.Compute.CredentialsCombo Linux jelszó](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Az SSH nyilvános kulcsát a kijelölt Linux felhasználók lásd:

![Microsoft.Compute.CredentialsCombo Linux kulcs](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Séma
A Windows a következő séma használata:

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
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

A **Linux**, a következő séma használata:

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
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
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
- `osPlatform` meg kell adni, és lehet **Windows** vagy **Linux**.
- Ha `constraints.required` értéke **igaz**, majd a jelszó vagy SSH nyilvános kulcs szövegmezőkben sikeresen érvényesíthető értéknek kell lennie. Az alapértelmezett érték **igaz**.
- Ha `options.hideConfirmation` értéke **igaz**, majd erősítse meg a jelszót a második szöveges jelölőnégyzet be van-e rejtve. Az alapértelmezett érték **hamis**.
- Ha `options.hidePassword` értéke **igaz**, majd a jelszó-hitelesítés használatára van-e rejtve. Használat csak akkor, ha `osPlatform` van **Linux**. Az alapértelmezett érték **hamis**.
- Az engedélyezett jelszavak további korlátait használatával valósítható a `customPasswordRegex` tulajdonság. A karakterlánc a `customValidationMessage` akkor látható, ha a jelszó egyéni érvényesítése sikertelen. Az alapértelmezett érték a tulajdonságot is **null**.

## <a name="sample-output"></a>Példa kimenet
Ha `osPlatform` van **Windows**, vagy `osPlatform` van **Linux** és a felhasználó által megadott helyett nyilvános SSH-kulcs jelszót, a vezérlő a következő eredményt adja vissza:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

Ha `osPlatform` van **Linux** és a felhasználó által megadott nyilvános SSH-kulcsot, a vezérlő a következő eredményt adja vissza:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
