---
title: Nyelvi támogatás – Translator Text API
titleSuffix: Azure Cognitive Services
description: A Translator Text API a következő nyelveket támogatja szöveg-szöveg fordításához neurális gépi fordítás (NMT) használatával.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: a4f9833e8dd14dc7c8ec5849cb809bf2089a5dae
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77206123"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>A Translator Text API nyelvi és területi támogatása

A Translator Text API az alábbi nyelveket támogatja szöveg szöveggé fordításához. A neurális gépi fordítás (NMT) a kiváló minőségű, mesterséges intelligenciát használó gépi fordítások új szabványa, amely a Translator Text API v3-as verziójában érhető el, amikor egy neurális rendszer elérhető.

[További információ a gépi fordítás működéséről](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Fordítás

**V2 Translator API**

> [!NOTE]
> A v2 2018. április 30-ig elavult. Telepítse át az alkalmazásait a v3-ra, hogy kihasználhassa a kizárólag a v3 verzióban elérhető új funkciókat.

* Csak statisztikai: nincs elérhető neurális rendszer ehhez a nyelvhez.
* Neurális elérhető: egy neurális rendszer áll rendelkezésre. Használja a `category=generalnn` paramétert a neurális rendszerek eléréséhez.
* Neurális alapértékek: a neurális az alapértelmezett fordítási rendszer. A `category=smt` paraméter használatával férhet hozzá a statisztikai rendszerhez a Microsoft Translator hub használatával.
* Csak neurális: csak neurális fordítás érhető el.

**V3 Translator API** A v3 Translator API alapértelmezés szerint a neurális, a statisztikai rendszerek pedig csak akkor érhetők el, ha nem létezik neurális rendszer.

> [!NOTE]
> Jelenleg a neurális nyelvek egy részhalmaza érhető el az egyéni fordítóban, és fokozatosan adunk hozzá továbbikat. [Az egyéni fordítóban jelenleg elérhető nyelvek megtekintése](#customization).

|Nyelv|  Nyelvkód|  V3 API|
|:-----|:-----:|:-----|
|afrikaans| `af`|   Neurális|
|arab|    `ar`    |   Neurális|
|Bengáli|    `bn`    |   Neurális|
|bosnyák (latin betűs)|   `bs`    |   Neurális|
|bolgár| `bg`    |   Neurális|
|Kantoni (hagyományos)|   `yue`|  Statisztikai|
|katalán|   `ca`    |   Statisztikai|
|kínai (egyszerűsített)|    `zh-Hans`|Neurális|
|kínai (hagyományos)|   `zh-Hant`       |Neurális|
|horvát|  `hr`    |Neurális|
|cseh| `cs`    |   Neurális|
|dán|    `da`        |Neurális|
|holland| `nl`|   Neurális|
|Angol|   `en`    |   Neurális|
|észt|  `et`    |   Neurális|
|Fijian|    `fj`    |   Statisztikai|
|filippínó|  `fil`   |   Statisztikai|
|finn|   `fi`    |   Neurális|
|francia|    `fr`    |   Neurális|
|német|    `de`    |   Neurális|
|görög| `el`    |   Neurális|
|Haitian Creole|    `ht`        |Statisztikai|
|héber |`he`   |Neurális
|hindi| `hi`    |   Neurális|
|Hmong Daw| `mww`   |   Statisztikai|
|magyar| `hu`    |   Neurális|
|izlandi| `is`    |   Neurális|
|indonéz|    `id`    |   Statisztikai|
|ír | `ga`| Neurális
|olasz|   `it`    |   Neurális|
|japán|  `ja`    |   Neurális|
|kannada|`kn`| Neurális
|szuahéli| `sw`    |   Statisztikai|
|Klingon|   `tlh`   |   Statisztikai|
|Klingon (plqaD)|   `tlh-Qaak`  |   Statisztikai|
|koreai |`ko`   |   Neurális|
|lett|   `lv`    |   Neurális|
|litván|    `lt`    |   Neurális|
|Madagaszkári|  `mg`    |   Statisztikai|
|maláj| `ms`        |Statisztikai|
|malajálam| `ml` | Neurális
|máltai|   `mt`    |   Statisztikai|
|maori| `mi`  | Neurális|
|norvég| `nb`    |   Neurális|
|perzsa|   `fa`    |   Neurális|
|lengyel|    `pl`    |   Neurális|
|portugál (brazíliai)|   `pt-br` |   Neurális|
|portugál (általános)| `pt-pt` | Neurális
|pandzsábi|`pa`|Neurális
|Queretaro Otomi|   `otq`   |   Statisztikai|
|román|  `ro`    |   Neurális|
|Orosz|   `ru`    |   Neurális|
|Samoa|    `sm`    |   Statisztikai|
|szerb (cirill betűs)|    `sr-Cyrl`|  Statisztikai|
|szerb (latin betűs)|   `sr-Latn`       |Statisztikai|
|szlovák|    `sk`    |   Neurális|
|szlovén| `sl`    |   Neurális|
|spanyol|   `es`    |   Neurális|
|svéd|   `sv`    |Neurális|
|Tahitian|  `ty`    |Statisztikai|
|tamil| `ta`    |   Neurális|
|telugu|    `te`    |   Neurális|
|thai|  `th`    |   Neurális|
|Tongan|    `to`    |   Statisztikai|
|török|   `tr`        |Neurális|
|ukrán| `uk`    |   Neurális|
|urdu|  `ur`    |   Statisztikai|
|vietnami|    `vi`    |   Neurális|
|walesi| `cy`    |   Neurális|
|A Maya alkalmazáshoz Yucatec|  `yua`   |   Statisztikai|

> [!NOTE]
> A nyelvi kód `pt` alapértelmezett értéke `pt-br`, Portugál (brazíliai).

## <a name="transliteration"></a>Átbetűzésű

A Átbetűzés metódus a következő nyelveket támogatja. A "to/from", "<->" kifejezés azt jelzi, hogy a nyelv a felsorolt parancsfájlokból vagy azok egyikére is átadható. A "-->" érték azt jelzi, hogy a nyelv csak az egyik parancsfájlból a másikba való átírására használható.

| Nyelv    | Nyelvkód | Szkript | /Feladó | Szkript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| arab | `ar` | Arab `Arab` | <--> | Latin `Latn` |
|Bengáli  | `bn` | Bengáli `Beng` | <--> | Latin `Latn` |
| kínai (egyszerűsített) | `zh-Hans` | Egyszerűsített kínai `Hans`| <--> | Latin `Latn` |
| kínai (egyszerűsített) | `zh-Hans` | Egyszerűsített kínai `Hans`| <--> | Hagyományos kínai `Hant`|
| kínai (hagyományos) | `zh-Hant` | Hagyományos kínai `Hant`| <--> | Latin `Latn` |
| kínai (hagyományos) | `zh-Hant` | Hagyományos kínai `Hant`| <--> | Egyszerűsített kínai `Hans` |
| gudzsaráti | `gu`  | Gudzsaráti `Gujr` | --> | Latin `Latn` |
| héber | `he` | Héber `Hebr` | <--> | Latin `Latn` |
| hindi | `hi` | Devanagari `Deva` | <--> | Latin `Latn` |
| japán | `ja` | Japán `Jpan` | <--> | Latin `Latn` |
| kannada | `kn` | Kannada `Knda` | --> | Latin `Latn` |
| malajálam | `ml` | Malayalam `Mlym` | --> | Latin `Latn` |
| marathi | `mr` | Devanagari `Deva` | --> | Latin `Latn` |
| Oriya | `or` | Oriya `Orya` | <--> | Latin `Latn` |
| pandzsábi | `pa` | Gurmukhi `Guru`  | <--> | Latin `Latn`  |
| szerb (cirill betűs) | `sr-Cyrl` | Cirill `Cyrl`  | --> | Latin `Latn` |
| szerb (latin betűs) | `sr-Latn` | Latin `Latn` | --> | Cirill `Cyrl`|
| tamil | `ta` | Tamil `Taml` | --> | Latin `Latn` |
| telugu | `te` | Telugu `Telu` | --> | Latin `Latn` |
| thai | `th` | Thai `Thai` | --> | Latin `Latn` |

## <a name="dictionary"></a>Szótár

A szótár a következő nyelveket támogatja angol nyelven vagy angol nyelven a keresési és példákat használó módszerek használatával.

| Nyelv    | Nyelvkód |
|:----------- |:-------------:|
| afrikaans      | `af`          |
| arab       | `ar`          |
| Bengáli      | `bn`          |
| bosnyák (latin betűs)      | `bs`          |
| bolgár      | `bg`          |
| katalán      | `ca`          |
| kínai (egyszerűsített)      | `zh-Hans`          |
| horvát      | `hr`          |
| cseh      | `cs`          |
| dán      | `da`          |
| holland      | `nl`          |
| észt      | `et`          |
| finn      | `fi`          |
| francia      | `fr`          |
| német      | `de`          |
| görög      | `el`          |
| Haitian Creole      | `ht`          |
| héber      | `he`          |
| hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| magyar      | `hu`          |
| izlandi    | `is`  |
| indonéz      | `id`          |
| olasz      | `it`          |
| japán      | `ja`          |
| szuahéli      | `sw`          |
| Klingon      | `tlh`          |
| koreai      | `ko`          |
| lett      | `lv`          |
| litván      | `lt`          |
| maláj      | `ms`          |
| máltai      | `mt`          |
| norvég      | `nb`          |
| perzsa      | `fa`          |
| lengyel      | `pl`          |
| portugál (brazíliai)     | `pt-br`          |
| román      | `ro`          |
| Orosz      | `ru`          |
| szerb (latin betűs)      | `sr-Latn`          |
| szlovák     | `sk`          |
| szlovén      | `sl`          |
| spanyol      | `es`          |
| svéd      | `sv`          |
| tamil      | `ta`          |
| thai      | `th`          |
| török      | `tr`          |
| ukrán      | `uk`          |
| urdu      | `ur`          |
| vietnami      | `vi`          |
| walesi      | `cy`          |

## <a name="detect"></a>Észlelés

Translator Text API észleli a fordításhoz és az íráshoz elérhető összes nyelvet.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Programozott módon érheti el a Translator Text API nyelvi listáját

A támogatott nyelvek listáját a languages metódussal kérheti le a Translator Text API v 3.0-s verziójában. A listát a szolgáltatás, a Nyelvkód, valamint az angol nyelvű és más támogatott nyelven is megtekintheti. Ezt a listát a Microsoft Translator Service automatikusan frissíti, mivel a rendszer új nyelveket tesz elérhetővé.

[Nyelvek megtekintése – útmutató – dokumentáció](reference/v3-0-languages.md)

## <a name="customization"></a>Testreszabás

Az [Egyéni Translator](https://aka.ms/CustomTranslator)használatával a következő nyelvek érhetők el az angol nyelvre és a testreszabásra.

| Nyelv    | Nyelvkód |
|:----------- |:-------------:|
| arab       | `ar`          |
| Bengáli      | `bn`          |
| bosnyák (latin betűs)      | `bs`          |
| bolgár      | `bg`          |
| kínai (egyszerűsített)      | `zh-Hans`          |
|kínai (hagyományos)|   `zh-Hant`   |
| horvát      | `hr`          |
| cseh      | `cs`          |
| dán      | `da`          |
| holland      | `nl`          |
| Angol    | `en`     |
| észt      | `et`          |
| finn      | `fi`          |
| francia      | `fr`          |
| német      | `de`          |
| görög      | `el`          |
| héber      | `he`          |
| hindi      | `hi`          |
| magyar      | `hu`          |
| izlandi | `is` |
| indonéz|   `id`    |
| ír | `ga`  |
| olasz      | `it`          |
| japán      | `ja`          |
| szuahéli|    `sw`    |
| koreai      | `ko`          |
| lett      | `lv`          |
| litván      | `lt`          |
| Madagaszkári| `mg`    |
| maori| `mi`  |
| norvég      | `nb`          |
| perzsa      | `fa`          |
| lengyel      | `pl`          |
| portugál (brazíliai) | `pt-br` |
| román      | `ro`          |
| Orosz      | `ru`          |
| Samoa|   `sm`    |
| szerb (latin betűs)      | `sr-Latn`          |
| szlovák     | `sk`          |
| szlovén      | `sl`          |
| spanyol      | `es`          |
| svéd      | `sv`          |
| thai      | `th`          |
| török      | `tr`          |
| ukrán      | `uk`          |
| vietnami      | `vi`          |
| walesi | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>A lista elérése a Microsoft Translator webhelyén

A nyelvek gyors áttekintéséhez a Microsoft Translator webhelyén a Translator Text és a Speech API-k által támogatott összes nyelv látható. Ez a lista nem tartalmazza a fejlesztői adatokat, például a nyelvi kódokat.

[A nyelvek listája](https://www.microsoft.com/translator/languages.aspx)
