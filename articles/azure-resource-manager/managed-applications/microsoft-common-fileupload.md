---
title: FileUpload felhasználói felület i elem
description: A cikk a Microsoft.Common.FileUpload felhasználói felületösszetevőt ismerteti az Azure Portalon. Lehetővé teszi, hogy a felhasználóknak fájlokat kell feltölteni, amikor egy felügyelt alkalmazás telepítése.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 61e1c9fe07fdd29ebc00e7e3491472d073bc4e5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652488"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft.Common.FileUpload felhasználói felületi elem

Olyan vezérlő, amely lehetővé teszi, hogy a felhasználó egy vagy több feltöltendő fájlt adjon meg.

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

## <a name="sample-output"></a>Példa kimenet

Ha options.multiple hamis és options.uploadMode fájl, akkor a kimenet tartalmát a fájl, mint egy JSON-karakterlánc:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Ha options.multiple igaz, és'options.uploadMode a fájl, akkor a kimenet tartalmát a fájlok, mint a JSON tömb:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Ha options.multiple hamis és options.uploadMode url, akkor a kimenet egy URL-t, mint a JSON string:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Ha options.multiple igaz, és options.uploadMode url, akkor a kimenet egy listát az URL-eket, mint a JSON tömb:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

A CreateUiDefinition tesztelésekor egyes böngészők (például a Google Chrome) csonkolják a Microsoft.Common.FileUpload elem által a böngészőkonzolon létrehozott URL-eket. Előfordulhat, hogy a teljes URL-címek másolásához a jobb gombbal az egyes hivatkozásokra kell kattintania.

## <a name="remarks"></a>Megjegyzések

- `constraints.accept`a böngésző fájlpárbeszédpanelén megjelenő fájltípusokat határozza meg. Tekintse meg a [HTML5 specifikáció](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept) az engedélyezett értékeket. Az alapértelmezett érték **null**.
- Ha `options.multiple` értéke **igaz,** a felhasználó több fájlt is kijelölhet a böngésző fájlpárbeszédpanelén. Az alapértelmezett érték: **hamis**.
- Ez az elem támogatja a fájlok feltöltését két `options.uploadMode`módban a . Ha **a fájl** meg van adva, a kimenet a fájl tartalmát blobként. Ha **url van** megadva, majd a fájl feltöltése egy ideiglenes helyre, és a kimenet a blob URL-címét. Az ideiglenes blobok 24 óra elteltével törlődnek. Az alapértelmezett érték a **fájl**.
- A feltöltött fájl védett. A kimeneti URL-cím tartalmaz egy [SAS-jogkivonatot](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) a fájl központi telepítés során való eléréséhez.
- A fájl `options.openMode` olvasásának értékét határozza meg. Ha a fájl várhatóan egyszerű szöveg, adja meg a **szöveget**; máshol adja meg a **bináris**. Az alapértelmezett érték a **szöveg**.
- Ha `options.uploadMode` **fájlra** van `options.openMode` állítva, és **bináris,** a kimenet base64-kódolású.
- `options.encoding`megadja a fájl olvasásakor használandó kódolást. Az alapértelmezett érték **UTF-8**, és `options.openMode` csak akkor használatos , ha **szövegre**van állítva .

## <a name="next-steps"></a>További lépések

* A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
* A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
