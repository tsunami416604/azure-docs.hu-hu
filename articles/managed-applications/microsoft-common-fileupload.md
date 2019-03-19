---
title: Az Azure FileUpload felhasználói felületi elemben |} A Microsoft Docs
description: Az Azure portal a Microsoft.Common.FileUpload felhasználói felületi elem ismerteti.
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
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 92a5f7c058904015cb22a239b7e7c4938ae1fdae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895252"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft.Common.FileUpload UI element
Egy olyan vezérlőelem, amely lehetővé teszi a felhasználóknak adjon meg egy vagy több fájlt feltölteni.

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
- `constraints.accept` fájltípusok látható a böngészőben vyhledání souboru meghatározza. Tekintse meg a [HTML5-specifikációt](https://www.w3.org/TR/html5/forms.html#attr-input-accept) az engedélyezett értékek. Az alapértelmezett érték **null**.
- Ha `options.multiple` értékre van állítva **igaz**, a felhasználó számára engedélyezett a böngésző fájl párbeszédpanelen válassza ki a egynél több fájlt. Az alapértelmezett érték **hamis**.
- Ez az elem értéke alapján két módban támogatja a fájlokat tölthet fel `options.uploadMode`. Ha **fájl** van megadva, a kimenet tartalmaz a blob, fájl tartalmát. Ha **URL-cím** van megadva, akkor a fájlt töltenek fel egy ideiglenes helyre, és a kimenet tartalmaz a blob URL-CÍMÉT. Ideiglenes blobok 24 óra múlva lesz kiürítve. Az alapértelmezett érték **fájl**.
- A feltöltött fájl védelmét. A kimeneti URL-cím tartalmazza egy [SAS-jogkivonat](../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) üzembe helyezés során a fájl eléréséhez.
- Az érték `options.openMode` határozza meg, hogyan van olvasni a fájlt. Ha a fájl várt lehet egyszerű szöveges, adja meg a **szöveg**; más, adja meg **bináris**. Az alapértelmezett érték **szöveg**.
- Ha `options.uploadMode` értékre van állítva **fájl** és `options.openMode` értékre van állítva **bináris**, a kimenet a base64-kódolású.
- `options.encoding` Itt adhatja meg a használni kívánt kódolás és a fájl olvasásakor. Az alapértelmezett érték **UTF-8**, és csak akkor, ha `options.openMode` értékre van állítva **szöveg**.

## <a name="sample-output"></a>Példa kimenet
Options.multiple értéke hamis és options.uploadMode fájlt, majd a kimenetet a fájl tartalmát egy JSON-karakterlánc:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Ha options.multiple igaz and'options.uploadMode fájl, akkor a kimeneti fájl tartalmának megegyezik egy JSON-tömb:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Options.multiple értéke hamis és options.uploadMode URL-címet, majd a kimenetet a egy URL-cím egy JSON-karakterlánc:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Options.multiple nastavena nA hodnotu true és options.uploadMode URL-címet, majd a kimenetet a URL-címek listáját, egy JSON-tömböt:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

A CreateUiDefinition tesztelésekor egyes böngészők (mint például a Google Chrome) csonkolja a böngészőbeli konzolon Microsoft.Common.FileUpload eleme által létrehozott URL-címeket. Kattintson a jobb gombbal az egyes kapcsolatok másolása a teljes URL-címeket szeretne.


## <a name="next-steps"></a>További lépések
* Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
* Egy felhasználói felületi elemeket általános tulajdonságok leírásáért lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
