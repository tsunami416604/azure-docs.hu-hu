---
title: "Az Azure által felügyelt alkalmazás CredentialsCombo felhasználói felületi elem |} Microsoft Docs"
description: "A témakör ismerteti a Microsoft.Compute.CredentialsCombo felhasználói felületi elem Azure által felügyelt alkalmazások"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: d8faa36aca762bc8d787d5750fcf7efdbaf986ea
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo felhasználói felületi elem
A Windows és Linux jelszavak és a nyilvános SSH-kulcsok beépített érvényesítéssel vezérlők egy csoportja. Ez az elem használata során [Azure által felügyelt alkalmazások létrehozására](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Felhasználói felület minta
![Microsoft.Compute.CredentialsCombo](./media/managed-application-elements/microsoft.compute.credentialscombo.png)

## <a name="schema"></a>Séma
Ha `osPlatform` van **Windows**, majd a következő séma szolgál:
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

Ha `osPlatform` van **Linux**, majd a következő séma szolgál:
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
- `osPlatform`meg kell adni, és lehet **Windows** vagy **Linux**.
- Ha `constraints.required` értéke **igaz**, majd a jelszó vagy SSH nyilvános kulcs szövegmezőkben sikeresen érvényesíthető értékeket szabad tartalmaznia. Az alapértelmezett érték **igaz**.
- Ha `options.hideConfirmation` értéke **igaz**, majd erősítse meg a jelszót a második szöveges jelölőnégyzet be van-e rejtve. Az alapértelmezett érték **hamis**.
- Ha `options.hidePassword` értéke **igaz**, majd a jelszó-hitelesítés használatára van-e rejtve. Használat csak akkor, ha `osPlatform` van **Linux**. Az alapértelmezett érték **hamis**.
- Az engedélyezett jelszavak további korlátait használatával valósítható a `customPasswordRegex` tulajdonság. A karakterlánc a `customValidationMessage` akkor látható, ha a jelszó egyéni érvényesítése sikertelen. Az alapértelmezett érték a tulajdonságot is **null**.

## <a name="sample-output"></a>Példa kimenet
Ha `osPlatform` van **Windows**, vagy a felhasználó által megadott helyett nyilvános SSH-kulcs jelszót, majd a következő kimeneti várt:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

Ha a felhasználó által megadott nyilvános SSH-kulcsot, a következő kimeneti várhatóan:
```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Következő lépések
* Felügyelt alkalmazások bemutatása, lásd: [Azure kezelt alkalmazás – áttekintés](overview.md).
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
