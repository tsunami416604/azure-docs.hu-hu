---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "78189019"
---
## <a name="sample-templates"></a>Példasablonok
A felhasználói felület testreszabásához itt talál példákat:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Ez a projekt a következő sablonokat tartalmazza:
- [Ocean Blue](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Szürke pala](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

A minta használata:

1. A tárház klónozása a helyi gépen. Válasszon egy sablon mappát `/ocean_blue` vagy `/slate_gray` .
1. Töltse fel a sablon mappájában és a mappában található összes fájlt a `/assets` blob Storage-ba az előző szakaszokban leírtak szerint.
1. Ezután nyissa meg a `\*.html` vagy a gyökérkönyvtárában található `/ocean_blue` `/slate_gray` összes fájlt, cserélje le a relatív URL-címek összes példányát a 2. lépésben feltöltött CSS-, kép-és betűkészlet-fájlok URL-címeire. Például:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Művelet
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Mentse a `\*.html` fájlokat, és töltse fel őket a blob Storage-ba.
1. Most módosítsa a szabályzatot, amely a korábban említett HTML-fájlra mutat.
1. Ha a hiányzó betűkészleteket, képeket vagy CSS-ket látja, tekintse át a hivatkozásokat a kiterjesztések és a \* . HTML fájlok között.
