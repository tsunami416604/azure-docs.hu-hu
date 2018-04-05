---
title: Az Azure FileUpload felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Common.FileUpload felhasználói felületi elem ismerteti.
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
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: bfde11a189348472942248a6b90fd5ef1b5a1c89
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft.Common.FileUpload felhasználói felületi elem
Lehetővé teszi a felhasználóknak adjon meg egy vagy több fájl feltöltése vezérlő.

## <a name="ui-sample"></a>Felhasználói felület minta
![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft.common.fileupload.png)

## <a name="schema"></a>Séma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="remarks"></a>Megjegyzések
- `constraints.accept` Adja meg a fájlokat, amelyek megjelennek a böngésző fájlpárbeszédpanelt típusú. Tekintse meg a [HTML5-specifikációt](http://www.w3.org/TR/html5/forms.html#attr-input-accept) az engedélyezett értékek. Az alapértelmezett érték **null**.
- Ha `options.multiple` értéke **igaz**, a felhasználó által megadható egynél több fájl a böngésző fájl párbeszédpanelen. Az alapértelmezett érték **hamis**.
- Ez az elem feltöltése fájlokat támogatja a két mód értéke alapján `options.uploadMode`. Ha **fájl** van megadva, a kimenet tartalmazza a fájlt egy blobba tartalmát. Ha **URL-cím** van megadva, akkor a fájl feltöltése egy ideiglenes helyre, és a kimenet tartalmazza a blob URL-CÍMÉT. Ideiglenes blobok kiürítendő 24 óra múlva. Az alapértelmezett érték **fájl**.
- Értékének `options.openMode` határozza meg, hogy a fájl írásvédett. Ha a fájl várt egyszerű szövegként, adja meg **szöveg**; más, adja meg **bináris**. Az alapértelmezett érték **szöveg**.
- Ha `options.uploadMode` értéke **fájl** és `options.openMode` értéke **bináris**, a base64-kódolású kimenete.
- `options.encoding` Meghatározza, hogy a fájl olvasásánál használandó kódolás. Az alapértelmezett érték **UTF-8**, és csak akkor, ha `options.openMode` értéke **szöveg**.

## <a name="sample-output"></a>Példa kimenet
Ha options.multiple értéke false, és options.uploadMode fájlt, majd a kimenet tartalmazza a fájl tartalmát egy JSON-karakterlánc:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Ha igaz options.multiple and'options.uploadMode fájl, akkor a kimenet tartalmazza a fájlok tartalmát egy JSON-tömb:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Ha options.multiple értéke false, és options.uploadMode URL-címet, majd a kimeneti egy URL-címet tartalmaz JSON karakterláncként:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Ha options.multiple értéke true, és options.uploadMode URL-címet, majd a kimenet tartalmazza URL-címek listáját egy JSON-tömb:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Egy CreateUiDefinition tesztelésekor (például a Google Chrome) egyes böngészők csonkolja a böngészőbeli konzolon a Microsoft.Common.FileUpload eleme által generált URL-címeket. Kattintson a jobb gombbal az egyes hivatkozások másolása a teljes URL-címeket szeretne.


## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
