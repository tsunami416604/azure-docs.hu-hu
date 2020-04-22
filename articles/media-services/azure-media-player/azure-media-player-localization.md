---
title: Az Azure Media Player honosítása
description: Több nyelvi támogatás a nem angol nyelvű felhasználók számára.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 34805c4eaae5d969fc2338c24f9f92404e065d15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727176"
---
# <a name="localization"></a>Honosítás #

A többnyelvű támogatás lehetővé teszi a nem angol nyelvű felhasználók számára, hogy natív módon kommunikáljanak a lejátszóval. Az Azure Media Player egy globális nyelvszótárral fog összeállítani, amely az oldal nyelve alapján honosítja a hibaüzeneteket. A fejlesztő kényszerített beállított nyelvvel rendelkező játékospéldányt is létrehozhat. Az alapértelmezett nyelv az angol (hu).

> [!NOTE]
> Ez a funkció még mindig megy keresztül néhány vizsgálat, és mint ilyen, a hibákat.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Az Azure Media Player jelenleg a következő nyelveket támogatja a megfelelő nyelvi kódokkal:

| Nyelv            | Kód | Nyelv                | Kód   | Nyelv                | Kód         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Angol ({default} )   | hu   | Horvát                | hr     | Román                | Ro           |
| Arab              | Ar   | Magyar               | hu     | Szlovák                  | Sk           |
| Bolgár           | Bg   | Indonéz              | id     | Szlovén                 | Sl           |
| Katalán             | Ca   | Izlandi               | egyenlő     | Szerb - cirill      | sr-cyrl-cs   |
| Cseh               | Cs   | Olasz                 | ez     | Szerb - Latin         | sr-latn-rs   |
| Dán              | Da   | Japán                | ja (ja)     | Orosz                 | Ru           |
| Német              | de de   | Kazak                  | Kk     | Svéd                 | Sv           |
| Görög               | el   | Koreai                  | Ko     | Thai                    | Th           |
| Spanyol             | Igen   | Litván              | Hadnagy     | Tagalog                 | Tl           |
| Észt            | et   | Lett                 | Lv     | Török                 | Tr           |
| Baszk              | Eu   | Malajziai               | Ms     | Ukrán               | uk           |
| Perzsa               | fa   | Norvég - BokmÃ¥ l     | Nb     | urdu                    | A           |
| Finn             | Fi   | Holland                   | Nl     | Vietnami              | Vi           |
| Francia              | Fr   | Norvég - Nynorsk     | pp     | Kínai - egyszerűsített    | zh-hans között      |
| Gallego            | Gl   | Lengyel                  | Pl     | Kínai - hagyományos   | zh-hant között      |
| Héber              | Ő   | Portugál - Brazília     | pt-br  |                         |              |
| Hindi               | hu   | Portugál - Portugália   | pt-pt  |                         |              |


> [!NOTE]
> Ha nem szeretné, hogy bármilyen lokalizáció bekövetkezzen, a nyelvet angolra kell kényszerítenie

## <a name="next-steps"></a>További lépések ##

- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)