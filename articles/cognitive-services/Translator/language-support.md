---
title: Nyelvi támogatás – Translator
titleSuffix: Azure Cognitive Services
description: Cognitive Services Translator a következő nyelveket támogatja szöveg-szöveg fordításához neurális gépi fordítás (NMT) használatával.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/10/2020
ms.author: swmachan
ms.openlocfilehash: d8e6a4e769b08d674b355f6f88a0967316e95c63
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782328"
---
# <a name="language-and-region-support-for-text-and-speech-translation"></a>Nyelvi és területi támogatás szöveg-és beszédfelismerési fordításhoz

A fordítóval fordítást használhat a 70-es és a szöveges fordítási nyelvek bármelyikére. A neurális gépi fordítás (NMT) az új szabvány a kiváló minőségű, mesterséges intelligenciát használó gépi fordításokhoz, és az alapszintű Translator v3 használatával érhető el, amikor egy neurális rendszer elérhető.

A fordítót az egyéni fordítóval együtt is használhatja olyan neurális fordítási rendszerek kiépítéséhez, amelyek megértik a saját üzleti és iparági terminológiáját, valamint a Microsoft Speech Service segítségével beszédfelismerési fordítást adhat az alkalmazásához.

[További információ a gépi fordítás működéséről](https://www.microsoft.com/translator/mt.aspx)

## <a name="text-translation"></a>Szövegfordítás
A fordítás a fordítási művelettel érhető el a fordítóban elérhető bármely nyelvről, illetve azokról. Az API az észlelési művelettel, a Átbetűzés művelettel és a szótári keresési és szótári példákat használó kétnyelvű szótárakkal is kínál nyelvi észlelést. Az egyes műveletekhez elérhető nyelvek alább láthatók. 

### <a name="translate"></a>Fordítás

A fordító a következő nyelveket támogatja szöveg szöveggé fordításához. 

[A fordítási műveletekre vonatkozó dokumentáció megtekintése](reference/v3-0-translate.md)

|Nyelv|  Nyelvkód|
|:-----|:-----:|
|búr| `af`|
|Arab|    `ar`    |
|Bangla|    `bn`    |
|Bosnyák (latin betűs)|   `bs`    |
|Bolgár| `bg`    |
|Kantoni (hagyományos)|   `yue`|
|Katalán|   `ca`    |
|kínai (egyszerűsített)|    `zh-Hans`|
|kínai (hagyományos)|   `zh-Hant`       |
|Horvát|  `hr`    |
|Cseh| `cs`    |
|Dari|  `prs`   |
|Dán|    `da`        |
|Holland| `nl`|
|Angol|   `en`    |
|Észt|  `et`    |
|Fidzsi|    `fj`    |
|Filipino|  `fil`   |
|Finn|   `fi`    |
|Francia|    `fr`    |
|Német|    `de`    |
|Görög| `el`    |
|gudzsaráti|  `gu`    |
|Haiti kreol|    `ht`        |
|Héber |`he`   |
|Hindi| `hi`    |
|Hmong DAW| `mww`   |
|Magyar| `hu`    |
|Izlandi| `is`    |
|Indonéz|    `id`    |
|Ír | `ga`|
|Olasz|   `it`    |
|Japán|  `ja`    |
|kannada|`kn`|
|Kazak|`kk`|
|Kiswahili| `sw`    |
|Klingon|   `tlh-Latn`  |
|Klingon (plqaD)|   `tlh-Piqd`  |
|Koreai |`ko`   |
|Kurd (Közép)  |`ku`   |
|Kurd (északi) |`kmr`  |
|Lett|   `lv`    |
|Litván|    `lt`    |
|Malgas|  `mg`    |
|Maláj| `ms`        |
|malajálam| `ml` |
|Máltai|   `mt`    |
|maori| `mi`  |
|marathi| `mr`  |
|Norvég| `nb`    |
|Odia|  `or`    |
|Pasto|    `ps`    |
|perzsa|   `fa`    |
|Lengyel|    `pl`    |
|Portugál (Brazília)|   `pt-br` |
|Portugál (Portugália)| `pt-pt` |
|pandzsábi|`pa`|
|Queretaro otomi|   `otq`   |
|Román|  `ro`    |
|Orosz|   `ru`    |
|Szamoai|    `sm`    |
|Szerb (cirill betűs)|    `sr-Cyrl`|
|Szerb (latin betűs)|   `sr-Latn`       |
|Szlovák|    `sk`    |
|Szlovén| `sl`    |
|Spanyol|   `es`    |
|Svéd|   `sv`    |
|Tahitian|  `ty`    |
|tamil| `ta`    |
|telugu|    `te`    |
|Thai|  `th`    |
|Tongai|    `to`    |
|Török|   `tr`        |
|Ukrán| `uk`    |
|urdu|  `ur`    |
|Vietnámi|    `vi`    |
|walesi| `cy`    |
|Yucatec Maya|  `yua`   |

> [!NOTE]
> A Nyelvkód `pt` alapértelmezett értéke `pt-br` , Portugál (Brazília).

### <a name="detect"></a>Észlelés

A Translator a következő nyelveket észleli fordításhoz és íráshoz.

[Észlelési műveletre vonatkozó dokumentáció megtekintése](reference/v3-0-detect.md)

|Nyelv|  Nyelvkód|
|:-----|:-----:|
|búr| `af`|
|Arab|    `ar`    |
|Bolgár| `bg`    |
|Katalán|   `ca`    |
|kínai (egyszerűsített)|    `zh-Hans`|
|kínai (hagyományos)|   `zh-Hant`       |
|Horvát|  `hr`    |
|Cseh| `cs`    |
|Dán|    `da`        |
|Holland| `nl`|
|Angol|   `en`    |
|Észt|  `et`    |
|Finn|   `fi`    |
|Francia|    `fr`    |
|Német|    `de`    |
|Görög| `el`    |
|gudzsaráti|  `gu`    |
|Haiti kreol|    `ht`        |
|Héber |`he`   |
|Hindi| `hi`    |
|Magyar| `hu`    |
|Izlandi| `is`    |
|Indonéz|    `id`    |
|Ír | `ga`|
|Olasz|   `it`    |
|Japán|  `ja`    |
|Kiswahili| `sw`    |
|Klingon|   `tlh-Latn`  |
|Koreai |`ko`   |
|Lett|   `lv`    |
|Litván|    `lt`    |
|Maláj| `ms`        |
|Máltai|   `mt`    |
|Norvég| `nb`    |
|Pasto|    `ps`    |
|perzsa|   `fa`    |
|Lengyel|    `pl`    |
|Portugál (Brazília)|   `pt-br` |
|Portugál (Portugália)| `pt-pt` |
|Román|  `ro`    |
|Orosz|   `ru`    |
|Szerb (cirill betűs)|    `sr-Cyrl`|
|Szerb (latin betűs)|   `sr-Latn`       |
|Szlovák|    `sk`    |
|Szlovén| `sl`    |
|Spanyol|   `es`    |
|Svéd|   `sv`    |
|Tahitian|  `ty`    |
|Thai|  `th`    |
|Török|   `tr`        |
|Ukrán| `uk`    |
|urdu|  `ur`    |
|Vietnámi|    `vi`    |
|walesi| `cy`    |
|Yucatec Maya|  `yua`   |

### <a name="transliterate"></a>Átírás

A Átbetűzés metódus a következő nyelveket támogatja. A "to/from", "<->" kifejezés azt jelzi, hogy a nyelv a felsorolt parancsfájlokból vagy azok egyikére is átadható. A "-->" érték azt jelzi, hogy a nyelv csak az egyik parancsfájlból a másikba való átírására használható.

[A Átbetűzés-művelet dokumentációjának megtekintése](reference/v3-0-translate.md)


| Nyelv    | Nyelvkód | Script | Hová/honnan | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arab | `ar` | arab `Arab` | <--> | Latin betűs `Latn` |
| Bangla  | `bn` | bengáli `Beng` | <--> | Latin betűs `Latn` |
| Kínai (egyszerűsített) | `zh-Hans` | kínai (egyszerűsített) `Hans`| <--> | Latin betűs `Latn` |
| Kínai (egyszerűsített) | `zh-Hans` | kínai (egyszerűsített) `Hans`| <--> | kínai (hagyományos) `Hant`|
| Kínai (hagyományos) | `zh-Hant` | kínai (hagyományos) `Hant`| <--> | Latin betűs `Latn` |
| Kínai (hagyományos) | `zh-Hant` | kínai (hagyományos) `Hant`| <--> | kínai (egyszerűsített) `Hans` |
| gudzsaráti | `gu`  | gudzsaráti `Gujr` | <--> | Latin betűs `Latn` |
| Héber | `he` | héber `Hebr` | <--> | Latin betűs `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Latin betűs `Latn` |
| Japán | `ja` | japán `Jpan` | <--> | Latin betűs `Latn` |
| kannada | `kn` | kannada `Knda` | <--> | Latin betűs `Latn` |
| malajálam | `ml` | malajálam `Mlym` | <--> | Latin betűs `Latn` |
| marathi | `mr` | Devanagari `Deva` | <--> | Latin betűs `Latn` |
| Odia | `or` | Oriya `Orya` | <--> | Latin betűs `Latn` |
| pandzsábi | `pa` | Gurmukhi `Guru`  | <--> | Latin betűs `Latn`  |
| Szerb (cirill betűs) | `sr-Cyrl` | Cirill betűs `Cyrl`  | --> | Latin betűs `Latn` |
| Szerb (latin betűs) | `sr-Latn` | Latin betűs `Latn` | --> | Cirill betűs `Cyrl`|
| tamil | `ta` | tamil `Taml` | <--> | Latin betűs `Latn` |
| telugu | `te` | telugu `Telu` | <--> | Latin betűs `Latn` |
| Thai | `th` | thai `Thai` | --> | Latin betűs `Latn` |

### <a name="dictionary"></a>Szótár

A szótár a következő nyelveket támogatja angol nyelven vagy angol nyelven a keresési és példákat használó módszerek használatával.

Tekintse meg a [szótári keresési](reference/v3-0-dictionary-lookup.md) és a [szótári példákhoz](reference/v3-0-dictionary-examples.md) kapcsolódó dokumentációt.

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
| Hmong DAW      | `mww`          |
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
| Vietnámi      | `vi`          |
| walesi      | `cy`          |

### <a name="access-the-translator-language-list-programmatically"></a>Programozott módon férhet hozzá a Translator Language listához

A fordító által támogatott nyelvek listáját a languages metódus használatával kérheti le. A listát a szolgáltatás, a Nyelvkód, valamint az angol nyelvű és más támogatott nyelven is megtekintheti. Ezt a listát a Microsoft Translator Service automatikusan frissíti, mivel a rendszer új nyelveket tesz elérhetővé.

[Nyelvek megtekintése – útmutató – dokumentáció](reference/v3-0-languages.md)

## <a name="customization"></a>Testreszabás

Az [Egyéni Translator](https://aka.ms/CustomTranslator)használatával a következő nyelvek érhetők el az angol nyelvre és a testreszabásra.

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
| gudzsaráti| `gu`    |
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
| Malgas| `mg`    |
| Maláj|    `ms`        |
| maori| `mi`  |
| marathi| `mr`  |
| Norvég      | `nb`          |
| perzsa      | `fa`          |
| Lengyel      | `pl`          |
| Portugál (Brazília) | `pt-br` |
| pandzsábi|`pa`|
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
| urdu| `ur`    |
| Vietnámi      | `vi`          |
| walesi | `cy` |

## <a name="speech-translation"></a>Speech Translation
A fordítás a fordító és a Cognitive Services Speech Service használatával érhető el. A [beszédfelismerési szolgáltatás dokumentációjában](https://docs.microsoft.com/azure/cognitive-services/speech-service/) tájékozódhat a beszédfelismerés használatáról és az összes [elérhető nyelvi lehetőségről](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support).

### <a name="speech-to-text"></a>Diktálás
A beszédet szöveggé alakíthatja, hogy a választott szöveg nyelvét lefordítsa. A beszédfelismerési szöveg fordítására, illetve beszéd-beszéd fordításra szolgál, ha a beszédfelismeréssel együtt használja.

| Nyelv    |
|:----------- |
|Arab|
|Kantoni (hagyományos)|
|Katalán|
|kínai (egyszerűsített)|
|kínai (hagyományos)|
|Dán|
|Holland|
|Angol|
|Finn|
|Francia|
|Német|
|gudzsaráti|
|Hindi|
|Olasz|
|Japán|
|Koreai|
|marathi|
|Norvég|
|Lengyel|
|Portugál (Brazília)|
|Portugál (Portugália)|
|Orosz|
|Spanyol|
|Svéd|
|tamil|
|telugu|
|Thai|
|Török|

### <a name="text-to-speech"></a>Szövegfelolvasás
Szöveg konvertálása beszédre A szöveg és a beszéd használatával a fordítási eredmények hallható kimenetét, illetve beszéd – beszéd fordítást lehet használni a beszédfelismerési szöveggel történő használathoz. 

| Nyelv    |
|:----------- |
|Arab|
|Bolgár|
|Kantoni (hagyományos)|
|Katalán|
|kínai (egyszerűsített)|
|kínai (hagyományos)|
|Horvát|
|Cseh|
|Dán|
|Holland|
|Angol|
|Finn|
|Francia|
|Német|
|Görög|
|Héber|
|Hindi|
|Magyar|
|Indonéz|
|Olasz|
|Japán|
|Koreai|
|Maláj|
|Norvég|
|Lengyel|
|Portugál (Brazília)|
|Portugál (Portugália)|
|Román|
|Orosz|
|Szlovák|
|Szlovén|
|Spanyol|
|Svéd|
|tamil|
|telugu|
|Thai|
|Török|
|Vietnámi|

## <a name="view-the-language-list-on-the-microsoft-translator-website"></a>A Microsoft Translator webhelyén megtekintheti a nyelvi listát

A nyelvek gyors áttekintéséhez a Microsoft Translator webhelye a fordító által támogatott összes nyelvet megjeleníti a fordítási és beszédfelismerési szolgáltatásokhoz. Ez a lista nem tartalmazza a fejlesztői adatokat, például a nyelvi kódokat.

[A nyelvek listája](https://www.microsoft.com/translator/languages.aspx)
