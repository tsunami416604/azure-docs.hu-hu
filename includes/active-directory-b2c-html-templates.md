---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: marsma
ms.openlocfilehash: 264604bfdf0c514e6464854f431addbc9d2dcdef
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373098"
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

1. A tárház klónozása a helyi gépen. Válasszon egy sablon mappát `/ocean_blue` vagy `/slate_gray`.
1. Töltse fel a sablon mappájában és a `/assets` mappában található összes fájlt a blob Storage-ba az előző szakaszokban leírtak szerint.
1. Ezután nyissa meg az egyes `\*.html` fájlokat `/ocean_blue` vagy `/slate_gray`gyökerében, és cserélje le a relatív URL-címek összes példányát a 2. lépésben feltöltött CSS-, kép-és betűkészlet-fájlokra. Például:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Művelet 
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Mentse a `\*.html` fájlokat, és töltse fel őket a blob Storage-ba.
1. Most módosítsa a szabályzatot, amely a korábban említett HTML-fájlra mutat.
1. Ha nem látja a hiányzó betűkészleteket, képeket vagy CSS-ket, tekintse át a hivatkozásokat a kiterjesztések házirendben és a \*. html fájlban.
