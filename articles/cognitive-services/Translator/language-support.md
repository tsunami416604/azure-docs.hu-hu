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
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 6339b86166aff008bf17b5096d42629daf6e3401
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434287"
---
# <a name="language-and-region-support-for-translator"></a>Nyelvi és területi támogatás fordítóknak

A fordító a következő nyelveket támogatja szöveg szöveggé fordításához. A neurális gépi fordítás (NMT) az új szabvány a kiváló minőségű, mesterséges intelligenciát használó gépi fordításokhoz, és az alapszintű Translator v3 használatával érhető el, amikor egy neurális rendszer elérhető.

[További információ a gépi fordítás működéséről](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Fordítás

**V2 Translator**

> [!NOTE]
> A v2 2018. április 30-ig elavult. Telepítse át az alkalmazásait a v3-ra, hogy kihasználhassa a kizárólag a v3 verzióban elérhető új funkciókat.

* Csak statisztikai: nincs elérhető neurális rendszer ehhez a nyelvhez.
* Neurális elérhető: egy neurális rendszer áll rendelkezésre. Használja a paramétert `category=generalnn` a neurális rendszerek eléréséhez.
* Neurális alapértékek: a neurális az alapértelmezett fordítási rendszer. A paraméter használatával `category=smt` férhet hozzá a statisztikai rendszerhez a Microsoft Translator hub használatával.
* Csak neurális: csak neurális fordítás érhető el.

**V3 Translator** A v3 Translator alapértelmezett és statisztikai rendszerek csak akkor érhetők el, ha nem létezik neurális rendszer.

> [!NOTE]
> Jelenleg a neurális nyelvek egy részhalmaza érhető el az egyéni fordítóban, és fokozatosan adunk hozzá továbbikat. [Az egyéni fordítóban jelenleg elérhető nyelvek megtekintése](#customization).

|Nyelv|    Nyelvkód|    V3 API|
|:-----|:-----:|:-----|
|búr|    `af`|    Neurális|
|Arab|    `ar`    |    Neurális|
|Bangla|    `bn`    |    Neurális|
|Bosnyák (latin betűs)|    `bs`    |    Neurális|
|Bolgár|    `bg`    |    Neurális|
|Kantoni (hagyományos)|    `yue`|    Statisztikai|
|Katalán|    `ca`    |    Statisztikai|
|kínai (egyszerűsített)|    `zh-Hans`|Neurális|
|kínai (hagyományos)|    `zh-Hant`        |Neurális|
|Horvát|    `hr`    |Neurális|
|Cseh|    `cs`    |    Neurális|
|Dán|    `da`        |Neurális|
|Holland|    `nl`|    Neurális|
|Angol|    `en`    |    Neurális|
|Észt|    `et`    |    Neurális|
|Fidzsi|    `fj`    |    Statisztikai|
|Filipino|    `fil`    |    Statisztikai|
|Finn|    `fi`    |    Neurális|
|Francia|    `fr`    |    Neurális|
|Német|    `de`    |    Neurális|
|Görög|    `el`    |    Neurális|
|gudzsaráti|    `gu`    |    Neurális|
|Haiti kreol|    `ht`        |Statisztikai|
|Héber    |`he`    |Neurális
|Hindi|    `hi`    |    Neurális|
|Hmong DAW|    `mww`    |    Statisztikai|
|Magyar|    `hu`    |    Neurális|
|Izlandi|    `is`    |    Neurális|
|Indonéz|    `id`    |    Statisztikai|
|Ír | `ga`| Neurális
|Olasz|    `it`    |    Neurális|
|Japán|    `ja`    |    Neurális|
|kannada|`kn`| Neurális|
|Kazak |`kk`| Neurális|
|Kiswahili|    `sw`    |    Statisztikai|
|Klingon|    `tlh-Latn`    |    Statisztikai|
|Klingon (plqaD)|    `tlh-Piqd`    |    Statisztikai|
|Koreai    |`ko`    |    Neurális|
|Lett|    `lv`    |    Neurális|
|Litván|    `lt`    |    Neurális|
|Malgas|    `mg`    |    Statisztikai|
|Maláj|    `ms`        |Statisztikai|
|malajálam| `ml` | Neurális
|Máltai|    `mt`    |    Statisztikai|
|maori| `mi`  | Neurális|
|marathi| `mr`  | Neurális|
|Norvég|    `nb`    |    Neurális|
|perzsa|    `fa`    |    Neurális|
|Lengyel|    `pl`    |    Neurális|
|Portugál (Brazília)|    `pt-br`    |    Neurális|
|Portugál (Portugália)| `pt-pt` | Neurális
|pandzsábi|`pa`|Neurális
|Queretaro otomi|    `otq`    |    Statisztikai|
|Román|    `ro`    |    Neurális|
|Orosz|    `ru`    |    Neurális|
|Szamoai|    `sm`    |    Statisztikai|
|Szerb (cirill betűs)|    `sr-Cyrl`|    Statisztikai|
|Szerb (latin betűs)|    `sr-Latn`        |Statisztikai|
|Szlovák|    `sk`    |    Neurális|
|Szlovén|    `sl`    |    Neurális|
|Spanyol|    `es`    |    Neurális|
|Svéd|    `sv`    |Neurális|
|Tahitian|    `ty`    |Statisztikai|
|tamil|    `ta`    |    Neurális|
|telugu|    `te`    |    Neurális|
|Thai|    `th`    |    Neurális|
|Tongai|    `to`    |    Statisztikai|
|Török|    `tr`        |Neurális|
|Ukrán|    `uk`    |    Neurális|
|urdu|    `ur`    |    Statisztikai|
|Vietnami|    `vi`    |    Neurális|
|walesi|    `cy`    |    Neurális|
|Yucatec Maya|    `yua`    |    Statisztikai|

> [!NOTE]
> A Nyelvkód `pt` alapértelmezett értéke `pt-br` , Portugál (Brazília).

## <a name="transliteration"></a>Átbetűzésű

A Átbetűzés metódus a következő nyelveket támogatja. A "to/from", "<->" kifejezés azt jelzi, hogy a nyelv a felsorolt parancsfájlokból vagy azok egyikére is átadható. A "-->" érték azt jelzi, hogy a nyelv csak az egyik parancsfájlból a másikba való átírására használható.

| Nyelv    | Nyelvkód | Parancsfájl | Hová/honnan | Parancsfájl|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arab | `ar` | arab`Arab` | <--> | Latin betűs`Latn` |
|Bangla  | `bn` | bengáli`Beng` | <--> | Latin betűs`Latn` |
| Kínai (egyszerűsített) | `zh-Hans` | kínai (egyszerűsített)`Hans`| <--> | Latin betűs`Latn` |
| Kínai (egyszerűsített) | `zh-Hans` | kínai (egyszerűsített)`Hans`| <--> | kínai (hagyományos)`Hant`|
| Kínai (hagyományos) | `zh-Hant` | kínai (hagyományos)`Hant`| <--> | Latin betűs`Latn` |
| Kínai (hagyományos) | `zh-Hant` | kínai (hagyományos)`Hant`| <--> | kínai (egyszerűsített)`Hans` |
| gudzsaráti | `gu`  | gudzsaráti`Gujr` | <--> | Latin betűs`Latn` |
| Héber | `he` | héber`Hebr` | <--> | Latin betűs`Latn` |
| Hindi | `hi` | Devanagari`Deva` | <--> | Latin betűs`Latn` |
| Japán | `ja` | japán`Jpan` | <--> | Latin betűs`Latn` |
| kannada | `kn` | kannada`Knda` | <--> | Latin betűs`Latn` |
| malajálam | `ml` | malajálam`Mlym` | <--> | Latin betűs`Latn` |
| marathi | `mr` | Devanagari`Deva` | <--> | Latin betűs`Latn` |
| Oriya | `or` | Oriya`Orya` | <--> | Latin betűs`Latn` |
| pandzsábi | `pa` | Gurmukhi`Guru`  | <--> | Latin betűs`Latn`  |
| Szerb (cirill betűs) | `sr-Cyrl` | Cirill betűs`Cyrl`  | --> | Latin betűs`Latn` |
| Szerb (latin betűs) | `sr-Latn` | Latin betűs`Latn` | --> | Cirill betűs`Cyrl`|
| tamil | `ta` | tamil`Taml` | <--> | Latin betűs`Latn` |
| telugu | `te` | telugu`Telu` | <--> | Latin betűs`Latn` |
| Thai | `th` | thai`Thai` | <--> | Latin betűs`Latn` |

## <a name="dictionary"></a>Szótár

A szótár a következő nyelveket támogatja angol nyelven vagy angol nyelven a keresési és példákat használó módszerek használatával.

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
| Klingon      | `tlh-Latn`          |
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

A Translator a fordításhoz és az íráshoz elérhető összes nyelvet észleli.


## <a name="access-the-translator-language-list-programmatically"></a>Programozott módon férhet hozzá a Translator Language listához

Lekérheti a Translator v 3.0 által támogatott nyelvek listáját a languages metódus használatával. A listát a szolgáltatás, a Nyelvkód, valamint az angol nyelvű és más támogatott nyelven is megtekintheti. Ezt a listát a Microsoft Translator Service automatikusan frissíti, mivel a rendszer új nyelveket tesz elérhetővé.

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
|kínai (hagyományos)|    `zh-Hant`    |
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
| Indonéz|    `id`    |
| Ír | `ga`    |
| Olasz      | `it`          |
| Japán      | `ja`          |
| Kiswahili|    `sw`    |
| Koreai      | `ko`          |
| Lett      | `lv`          |
| Litván      | `lt`          |
| Malgas|    `mg`    |
| maori| `mi`  |
| Norvég      | `nb`          |
| perzsa      | `fa`          |
| Lengyel      | `pl`          |
| Portugál (Brazília) | `pt-br` |
| Román      | `ro`          |
| Orosz      | `ru`          |
| Szamoai|    `sm`    |
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

A Microsoft Translator webhelyén gyorsan megtekintheti a nyelveket, és a fordító és a beszédfelismerés API-k által támogatott összes nyelvet megjeleníti. Ez a lista nem tartalmazza a fejlesztői adatokat, például a nyelvi kódokat.

[A nyelvek listája](https://www.microsoft.com/translator/languages.aspx)
