---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189019"
---
## <a name="sample-templates"></a>Példasablonok
A felhasználói felület testreszabásához használt mintasablonokat itt találja:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Ez a projekt a következő sablonokat tartalmazza:
- [Óceán kék](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Palaszürke](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

A minta használata:

1. Klónozza a tárta a helyi számítógépen. Válasszon sablonmappát `/ocean_blue` vagy `/slate_gray`a lehetőséget.
1. Töltse fel a sablonmappában `/assets` és a mappában lévő összes fájlt a Blob storage-ba az előző szakaszokban leírtak szerint.
1. Ezután nyissa `\*.html` meg az egyes `/ocean_blue` `/slate_gray`fájlokat a vagy a gyökérben, cserélje le a relatív URL-ek összes példányát a 2. Példa:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Művelet
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Mentse `\*.html` a fájlokat, és töltse fel őket a Blob storage-ba.
1. Most módosítsa a házirendet, mutatva a HTML-fájl, mint korábban említettük.
1. Ha hiányzó betűtípusokat, képeket vagy CSS-t lát, ellenőrizze a \*hivatkozásokat a bővítményekházirendben és a .html fájlokban.
