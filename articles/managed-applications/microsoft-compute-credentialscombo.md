---
title: Az Azure CredentialsCombo felhasználói felületi elemben |} A Microsoft Docs
description: Az Azure portal a Microsoft.Compute.CredentialsCombo felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: 0412d55fe60524cde404e6a640723d3259e020e1
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586240"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo felhasználói felületi elemben
A Windows és Linux-jelszavak és a nyilvános SSH-kulcsokat a beépített ellenőrzési vezérlőelemek csoport.

## <a name="ui-sample"></a>Felhasználói felület minta

A Windows, a felhasználók számára::

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Kiválasztott jelszóval rendelkező Linux-alapú felhasználók lásd:

![Microsoft.Compute.CredentialsCombo Linux-jelszó](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Az SSH nyilvános kulcs kiválasztott Linux felhasználók lásd:

![Microsoft.Compute.CredentialsCombo Linux kulcs](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Séma
Windows használja a következő mintát követik:

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
    "customValidationMessage": "The password must contain at least 12 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

A **Linux**, használja a következő mintát követik:

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
    "customValidationMessage": "The password must contain at least 12 characters, with at least 1 letter and 1 number."
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
- Ha `constraints.required` értékre van állítva **igaz**, majd a jelszó vagy SSH nyilvános kulcs szövegmezők sikeresen érvényesíthető értékeket kell rendelkeznie. Az alapértelmezett érték **igaz**.
- Ha `options.hideConfirmation` értékre van állítva **igaz**, akkor a második szöveges be a jelszó megerősítése el van rejtve. Az alapértelmezett érték **hamis**.
- Ha `options.hidePassword` értékre van állítva **igaz**, majd a beállítást a jelszó-hitelesítés használatára van-e rejtve. Használat csak akkor, ha `osPlatform` van **Linux**. Az alapértelmezett érték **hamis**.
- További korlátozások a engedélyezett jelszavak használatával valósítható a `customPasswordRegex` tulajdonság. A karakterlánc `customValidationMessage` jelenik meg, ha a jelszó egyéni ellenőrzés sikertelen lesz. Az alapértelmezett érték a mindkét tulajdonság **null**.

## <a name="sample-output"></a>Példa kimenet
Ha `osPlatform` van **Windows**, vagy `osPlatform` van **Linux** és egy jelszót az SSH nyilvános kulcs helyett, a felhasználó által megadott, a vezérlő a következő kimenetet ad vissza:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Ha `osPlatform` van **Linux** és a egy nyilvános SSH-kulcsot, a felhasználó által megadott, a vezérlő a következő kimenetet ad vissza:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>További lépések
* Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
* Egy felhasználói felületi elemeket általános tulajdonságok leírásáért lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
