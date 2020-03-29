---
title: Nyelvi támogatás - Translator Text API
titleSuffix: Azure Cognitive Services
description: A Translator Text API a következő nyelveket támogatja a neurális gépi fordítás (NMT) használatával történő szöveg-szöveg fordításhoz.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: a4f9833e8dd14dc7c8ec5849cb809bf2089a5dae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77206123"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>A Translator Text API nyelvi és régiótámogatása

A Translator Text API a következő nyelveket támogatja a szöveg-szöveg fordításhoz. Neurális gépi fordítás (NMT) az új szabvány a kiváló minőségű AI-alapú gépi fordítások és elérhető alapértelmezett ként a Translator Text API V3-on, ha neurális rendszer áll rendelkezésre.

[További információ a gépi fordítás működéséről](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Fordítás

**V2 fordító API**

> [!NOTE]
> 2018. április 30-án a V2 elavult. Kérjük, telepítse át alkalmazásait a V3-ba, hogy kihasználja a kizárólag a V3-ban elérhető új funkciókat.

* Csak statisztikai: Nincs neurális rendszer érhető el ehhez a nyelvhez.
* Neurális elérhető: Neurális rendszer áll rendelkezésre. Használja a `category=generalnn` paramétert a neurális rendszer eléréséhez.
* Neurális alapértelmezett: Neurális az alapértelmezett fordítási rendszer. A paraméter `category=smt` segítségével érheti el a Microsoft Translator Hubkal használható statisztikai rendszert.
* Csak neurális: Csak neurális fordítás érhető el.

**V3 fordító API** A V3 Translator API alapértelmezés szerint neurális, és a statisztikai rendszerek csak akkor érhetők el, ha nincs neurális rendszer létezik.

> [!NOTE]
> Jelenleg a neurális nyelvek egy részhalmaza érhető el az egyéni fordítóban, és fokozatosan továbbieket adunk hozzá. [Az Egyéni fordítóban jelenleg elérhető nyelvek megtekintése.](#customization)

|Nyelv|  Nyelvkód|  V3 API|
|:-----|:-----:|:-----|
|búr| `af`|   Neurális|
|Arab|    `ar`    |   Neurális|
|Bangla|    `bn`    |   Neurális|
|Bosnyák (latin betűs)|   `bs`    |   Neurális|
|Bolgár| `bg`    |   Neurális|
|Kantoni (hagyományos)|   `yue`|  Statisztikai|
|Katalán|   `ca`    |   Statisztikai|
|kínai (egyszerűsített)|    `zh-Hans`|Neurális|
|kínai (hagyományos)|   `zh-Hant`       |Neurális|
|Horvát|  `hr`    |Neurális|
|Cseh| `cs`    |   Neurális|
|Dán|    `da`        |Neurális|
|Holland| `nl`|   Neurális|
|Angol|   `en`    |   Neurális|
|Észt|  `et`    |   Neurális|
|Fidzsi|    `fj`    |   Statisztikai|
|Filipino|  `fil`   |   Statisztikai|
|Finn|   `fi`    |   Neurális|
|Francia|    `fr`    |   Neurális|
|Német|    `de`    |   Neurális|
|Görög| `el`    |   Neurális|
|Haiti kreol|    `ht`        |Statisztikai|
|Héber |`he`   |Neurális
|Hindi| `hi`    |   Neurális|
|Hmong Daw| `mww`   |   Statisztikai|
|Magyar| `hu`    |   Neurális|
|Izlandi| `is`    |   Neurális|
|Indonéz|    `id`    |   Statisztikai|
|Ír | `ga`| Neurális
|Olasz|   `it`    |   Neurális|
|Japán|  `ja`    |   Neurális|
|kannada|`kn`| Neurális
|Kiswahili| `sw`    |   Statisztikai|
|Klingon|   `tlh`   |   Statisztikai|
|Klingon (plqaD)|   `tlh-Qaak`  |   Statisztikai|
|Koreai |`ko`   |   Neurális|
|Lett|   `lv`    |   Neurális|
|Litván|    `lt`    |   Neurális|
|Málgas|  `mg`    |   Statisztikai|
|Maláj| `ms`        |Statisztikai|
|malajálam| `ml` | Neurális
|Máltai|   `mt`    |   Statisztikai|
|maori| `mi`  | Neurális|
|Norvég| `nb`    |   Neurális|
|perzsa|   `fa`    |   Neurális|
|Lengyel|    `pl`    |   Neurális|
|Portugál (Brazília)|   `pt-br` |   Neurális|
|Portugál (Portugália)| `pt-pt` | Neurális
|pandzsábi|`pa`|Neurális
|Queretaro Otomi|   `otq`   |   Statisztikai|
|Román|  `ro`    |   Neurális|
|Orosz|   `ru`    |   Neurális|
|Szamoai|    `sm`    |   Statisztikai|
|Szerb (cirill betűs)|    `sr-Cyrl`|  Statisztikai|
|Szerb (latin betűs)|   `sr-Latn`       |Statisztikai|
|Szlovák|    `sk`    |   Neurális|
|Szlovén| `sl`    |   Neurális|
|Spanyol|   `es`    |   Neurális|
|Svéd|   `sv`    |Neurális|
|Tahitian|  `ty`    |Statisztikai|
|tamil| `ta`    |   Neurális|
|telugu|    `te`    |   Neurális|
|Thai|  `th`    |   Neurális|
|Tongai|    `to`    |   Statisztikai|
|Török|   `tr`        |Neurális|
|Ukrán| `uk`    |   Neurális|
|urdu|  `ur`    |   Statisztikai|
|Vietnami|    `vi`    |   Neurális|
|walesi| `cy`    |   Neurális|
|Yucatec Maya|  `yua`   |   Statisztikai|

> [!NOTE]
> A `pt` nyelvkód `pt-br`alapértelmezés szerint a portugál (Brazília) lesz.

## <a name="transliteration"></a>Átírás

A Transliterate metódus a következő nyelveket támogatja. A "To/From", "< -- >" azt jelzi, hogy a nyelv átírható vagy bármelyik felsorolt szkriptek. A "-->" azt jelzi, hogy a nyelv csak átírható az egyik forgatókönyvből a másikba.

| Nyelv    | Nyelvkód | Parancsfájl | Hová/honnan | Parancsfájl|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arab | `ar` | arab`Arab` | <--> | Latin`Latn` |
|Bangla  | `bn` | bengáli`Beng` | <--> | Latin`Latn` |
| Kínai (egyszerűsített) | `zh-Hans` | kínai (egyszerűsített)`Hans`| <--> | Latin`Latn` |
| Kínai (egyszerűsített) | `zh-Hans` | kínai (egyszerűsített)`Hans`| <--> | kínai (hagyományos)`Hant`|
| Kínai (hagyományos) | `zh-Hant` | kínai (hagyományos)`Hant`| <--> | Latin`Latn` |
| Kínai (hagyományos) | `zh-Hant` | kínai (hagyományos)`Hant`| <--> | kínai (egyszerűsített)`Hans` |
| gudzsaráti | `gu`  | gudzsaráti`Gujr` | --> | Latin`Latn` |
| Héber | `he` | héber`Hebr` | <--> | Latin`Latn` |
| Hindi | `hi` | Devanagari`Deva` | <--> | Latin`Latn` |
| Japán | `ja` | japán`Jpan` | <--> | Latin`Latn` |
| kannada | `kn` | kannada`Knda` | --> | Latin`Latn` |
| malajálam | `ml` | malajálam`Mlym` | --> | Latin`Latn` |
| marathi | `mr` | Devanagari`Deva` | --> | Latin`Latn` |
| Oriya | `or` | Oriya`Orya` | <--> | Latin`Latn` |
| pandzsábi | `pa` | Gurmukhi között`Guru`  | <--> | Latin`Latn`  |
| Szerb (cirill betűs) | `sr-Cyrl` | Cirill`Cyrl`  | --> | Latin`Latn` |
| Szerb (latin betűs) | `sr-Latn` | Latin`Latn` | --> | Cirill`Cyrl`|
| tamil | `ta` | tamil`Taml` | --> | Latin`Latn` |
| telugu | `te` | telugu`Telu` | --> | Latin`Latn` |
| Thai | `th` | thai`Thai` | --> | Latin`Latn` |

## <a name="dictionary"></a>Szótár

A szótár a következő nyelveket támogatja angolra vagy angolról a Keresés és a Példák módszerrel.

| Nyelv    | Nyelvkód |
|:----------- |:-------------:|
| búr      | `af`          |
| Arab       | `ar`          |
| Bangla      | `bn`          |
| Bosnyák (latin betűs)      | `bs`          |
| Bolgár      | `bg`          |
| Katalán      | `ca`          |
| kínai (egyszerűsített)      | `zh-Hans`          |
| Horvát      | `hr`          |
| Cseh      | `cs`          |
| Dán      | `da`          |
| Holland      | `nl`          |
| Észt      | `et`          |
| Finn      | `fi`          |
| Francia      | `fr`          |
| Német      | `de`          |
| Görög      | `el`          |
| Haiti kreol      | `ht`          |
| Héber      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Magyar      | `hu`          |
| Izlandi    | `is`  |
| Indonéz      | `id`          |
| Olasz      | `it`          |
| Japán      | `ja`          |
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Koreai      | `ko`          |
| Lett      | `lv`          |
| Litván      | `lt`          |
| Maláj      | `ms`          |
| Máltai      | `mt`          |
| Norvég      | `nb`          |
| perzsa      | `fa`          |
| Lengyel      | `pl`          |
| Portugál (Brazília)     | `pt-br`          |
| Román      | `ro`          |
| Orosz      | `ru`          |
| Szerb (latin betűs)      | `sr-Latn`          |
| Szlovák     | `sk`          |
| Szlovén      | `sl`          |
| Spanyol      | `es`          |
| Svéd      | `sv`          |
| tamil      | `ta`          |
| Thai      | `th`          |
| Török      | `tr`          |
| Ukrán      | `uk`          |
| urdu      | `ur`          |
| Vietnami      | `vi`          |
| walesi      | `cy`          |

## <a name="detect"></a>Észlelés

A Translator Text API észleli a fordításhoz és átíráshoz rendelkezésre álló összes nyelvet.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>A Translator Text API nyelvi listájának elérése programozott módon

A Translator Text API 3.0-s listájának lekérésével a Nyelvek metódus sal. A listát funkció, nyelvkód, valamint a nyelv neve angol vagy más támogatott nyelven tekintheti meg. Ezt a listát a Microsoft Translator szolgáltatás automatikusan frissíti, amint új nyelvek érhetők el.

[Nyelvek műveleti referenciadokumentációjának megtekintése](reference/v3-0-languages.md)

## <a name="customization"></a>Testreszabás

Az alábbi nyelvek az angol nyelvre vagy angolnyelvre történő testreszabáshoz érhetők el [az egyéni fordító](https://aka.ms/CustomTranslator)segítségével.

| Nyelv    | Nyelvkód |
|:----------- |:-------------:|
| Arab       | `ar`          |
| Bangla      | `bn`          |
| Bosnyák (latin betűs)      | `bs`          |
| Bolgár      | `bg`          |
| kínai (egyszerűsített)      | `zh-Hans`          |
|kínai (hagyományos)|   `zh-Hant`   |
| Horvát      | `hr`          |
| Cseh      | `cs`          |
| Dán      | `da`          |
| Holland      | `nl`          |
| Angol    | `en`     |
| Észt      | `et`          |
| Finn      | `fi`          |
| Francia      | `fr`          |
| Német      | `de`          |
| Görög      | `el`          |
| Héber      | `he`          |
| Hindi      | `hi`          |
| Magyar      | `hu`          |
| Izlandi | `is` |
| Indonéz|   `id`    |
| Ír | `ga`  |
| Olasz      | `it`          |
| Japán      | `ja`          |
| Kiswahili|    `sw`    |
| Koreai      | `ko`          |
| Lett      | `lv`          |
| Litván      | `lt`          |
| Málgas| `mg`    |
| maori| `mi`  |
| Norvég      | `nb`          |
| perzsa      | `fa`          |
| Lengyel      | `pl`          |
| Portugál (Brazília) | `pt-br` |
| Román      | `ro`          |
| Orosz      | `ru`          |
| Szamoai|   `sm`    |
| Szerb (latin betűs)      | `sr-Latn`          |
| Szlovák     | `sk`          |
| Szlovén      | `sl`          |
| Spanyol      | `es`          |
| Svéd      | `sv`          |
| Thai      | `th`          |
| Török      | `tr`          |
| Ukrán      | `uk`          |
| Vietnami      | `vi`          |
| walesi | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>A lista elérése a Microsoft Translator webhelyén

A nyelvek gyors áttekintéséhez a Microsoft Translator webhelye a Fordító szöveg- és beszédfelismerési API-i által támogatott összes nyelvet megjeleníti. Ez a lista nem tartalmaz fejlesztőspecifikus információkat, például nyelvkódokat.

[A nyelvek listájának megtekintése](https://www.microsoft.com/translator/languages.aspx)
