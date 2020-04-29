---
title: Honosítás Azure Media Player
description: Többnyelvű támogatás a nem angol nyelvű felhasználók felhasználói számára.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 34805c4eaae5d969fc2338c24f9f92404e065d15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81727176"
---
# <a name="localization"></a>Honosítás #

A többnyelvű támogatás lehetővé teszi, hogy a nem angol nyelvű felhasználók natív módon használhassák a lejátszót. A Azure Media Player a nyelvek globális szótárát fogja létrehozni, amely az oldal nyelvén alapuló hibaüzeneteket fogja honosítani. Egy fejlesztő létrehozhat egy kényszerített beállított nyelvet tartalmazó Player-példányt is. Az alapértelmezett nyelv az angol (en).

> [!NOTE]
> Ez a funkció továbbra is egy tesztelésen megy keresztül, és a hibákra is vonatkozik.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

A Azure Media Player jelenleg a következő nyelveket támogatja a megfelelő nyelvi kódokkal:

| Nyelv            | Kód | Nyelv                | Kód   | Nyelv                | Kód         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Angol {default}   | hu   | Horvát                | hr     | Román                | ro           |
| Arab              | AR   | Magyar               | hu     | Szlovák                  | sk           |
| Bolgár           | BG   | Indonéz              | id     | Szlovén                 | SL           |
| Katalán             | CA   | Izlandi               | egyenlő     | Szerb – cirill betűs      | SR-Cyrl-cs   |
| Cseh               | CS   | Olasz                 | Ez     | Szerb – latin betűs         | sr-latn-rs   |
| Dán              | da   | Japán                | Japán     | Orosz                 | ru           |
| Német              | de   | Kazak                  | KK     | Svéd                 | Sv           |
| Görög               | el   | Koreai                  | Ko     | Thai                    | adik           |
| Spanyol             | Igen   | Litván              | lt     | Tagalog                 | TL           |
| Észt            | et   | Lett                 | lv     | Török                 | TR           |
| Baszk              | EU   | Malajziai               | MS     | Ukrán               | uk           |
| Perzsa               | h   | Norvég – BokmÃ ¥ l     | NB     | urdu                    | a           |
| Finn             | Fi   | Holland                   | nl     | Vietnami              | VI           |
| Francia              | fr   | Norvég – nynorsk     | pp     | Egyszerűsített kínai    | zh-Hans      |
| Gallego            | GL   | Lengyel                  | pl     | Kínai – hagyományos   | zh-Hant      |
| Héber              | ő   | Portugál - Brazília     | pt-br  |                         |              |
| Hindi               | hu   | Portugál - Portugália   | pt-pt  |                         |              |


> [!NOTE]
> Ha nem szeretné, hogy a honosítása ne történjen meg, az angol nyelvre kell kényszeríteni a nyelvet

## <a name="next-steps"></a>További lépések ##

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)