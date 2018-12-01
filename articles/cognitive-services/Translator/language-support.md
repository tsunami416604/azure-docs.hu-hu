---
title: Nyelvi támogatás – Translator Text API
titleSuffix: Azure Cognitive Services
description: A Translator Text API által támogatott természetes nyelvek listáját.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 09/25/2018
ms.author: v-jansko
ms.openlocfilehash: 0b1187083c14fc7c536f6a32f3a41957f53f299b
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679715"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>A Translator Text API nyelvéhez és régiójához támogatása

A Translator Text API a következő nyelveket támogatja a szöveg a fordítás. Neurális gépi fordítás (NMT) magas színvonalú mesterséges Intelligenciával rendelkező gép fordítások új szabványa, és használja, a Translator Text API v3-as, akkor egy Neurális rendszeren érhető el az alapértelmezett érhető el. 

[További információ arról, hogyan működik a gépi fordítás](https://www.microsoft.com/translator/mt.aspx)

**V2 Translator API**

> [!NOTE]
> V2 elavulttá 2018. április 30., és a 2019. április 30. ezt megszüntetjük.

* Csak statisztikai: nincs Neurális rendszer nem érhető el ehhez a nyelvhez.
* Neurális érhető el: egy Neurális rendszeren érhető el. Használja ezt a paramétert `category=generalnn` hozzáférhetnek a Neurális rendszerhez.
* Neurális alapértelmezett: Neurális az alapértelmezett fordítási rendszerét. Használja ezt a paramétert `category=smt` hozzáférhetnek a statisztikai rendszerhez a Microsoft Translator Hub való használatra.
* Csak Neurális: csak a Neurális fordítással érhető el.

**Translator API v3-as** a v3-as Translator API egy Neurális alapértelmezés szerint, és statisztikai rendszerek csak érhetők el, ha Neurális rendszert nem létezik. Egyéni a fordítót csak akkor használható, a Neurális nyelvek. 

|Nyelv|  Nyelvkód|  V2 API| V3 API|
|:-----|:-----:|:-----|:-----|
|afrikaans| `af`    |Csak statisztikai|  Neurális|
|arab|    `ar`    |Neurális érhető el|  Neurális|
|Arab, Levantine| `apc`   |Neurális érhető el|  Neurális|
|Bengáli|    `bn`    |Neurális érhető el|  Neurális|
|bosnyák (latin betűs)|   `bs`    |Csak statisztikai|  Statisztikai|
|bolgár| `bg`    |Neurális érhető el|  Neurális|
|Kantoni (hagyományos)|   `yue`   |Csak statisztikai|  Statisztikai|
|katalán|   `ca`    |Csak statisztikai|  Statisztikai|
|kínai (egyszerűsített)|    `zh-Hans`   |Neurális alapértelmezett |Neurális|
|kínai (hagyományos)|   `zh-Hant`   |Neurális alapértelmezett |Neurális|
|horvát|  `hr`    |Neurális érhető el|  Neurális|
|cseh| `cs`    |Neurális érhető el|  Neurális|
|dán|    `da`    |Neurális érhető el   |Neurális|
|holland| `nl`    |Neurális érhető el|  Neurális|
|Angol|   `en`    |Neurális érhető el|  Neurális|
|észt|  `et`    |Neurális érhető el|  Neurális|
|Fijian|    `fj`    |Csak statisztikai|  Statisztikai|
|filippínó|  `fil`   |Csak statisztikai|  Statisztikai|
|finn|   `fi`    |Neurális érhető el|  Neurális|
|francia|    `fr`    |Neurális érhető el|  Neurális|
|német|    `de`    |Neurális érhető el|  Neurális|
|görög| `el`    |Neurális érhető el|  Neurális|
|Haitian Creole|    `ht`    |Csak statisztikai   |Statisztikai|
|héber |`he`   |Neurális érhető el   |Neurális|
|hindi| `hi`    |Neurális alapértelmezett|    Neurális|
|Hmong Daw| `mww`   |Csak statisztikai|  Statisztikai|
|magyar| `hu`    |Neurális érhető el|  Neurális|
|izlandi| `is`    |Csak Neurális|   Neurális|
|indonéz|    `id`    |Csak statisztikai|  Statisztikai|
|olasz|   `it`    |Neurális érhető el|  Neurális|
|japán|  `ja`    |Neurális érhető el|  Neurális|
|szuahéli| `sw`    |Csak statisztikai|  Statisztikai|
|Klingon|   `tlh`   |Csak statisztikai|  Statisztikai|
|Klingon (plqaD)|   `tlh-Qaak`  |Csak statisztikai|  Statisztikai|
|koreai |`ko`   |Neurális érhető el|  Neurális|
|lett|   `lv`    |Neurális érhető el|  Neurális|
|litván|    `lt`    |Neurális érhető el|  Neurális|
|Madagaszkári|  `mg`    |Csak statisztikai|  Statisztikai|
|maláj| `ms`    |Csak statisztikai   |Statisztikai|
|máltai|   `mt`    |Csak statisztikai|  Statisztikai|
|norvég| `nb`    |Neurális érhető el|  Neurális|
|perzsa|   `fa`    |Csak statisztikai|  Statisztikai|
|lengyel|    `pl`    |Neurális érhető el|  Neurális|
|portugál|    `pt`    |Neurális érhető el|  Neurális|
|Queretaro Otomi|   `otq`   |Csak statisztikai|  Statisztikai|
|román|  `ro`    |Neurális érhető el|  Neurális|
|orosz|   `ru`    |Neurális érhető el|  Neurális|
|Samoa|    `sm`    |Csak statisztikai|  Statisztikai|
|szerb (cirill betűs)|    `sr-Cyrl`   |Csak statisztikai|  Statisztikai|
|szerb (latin betűs)|   `sr-Latn`   |Csak statisztikai   |Statisztikai|
|szlovák|    `sk`    |Neurális érhető el|  Neurális|
|szlovén| `sl`    |Neurális érhető el|  Neurális|
|spanyol|   `es`    |Neurális érhető el|  Neurális|
|svéd|   `sv`    |Neurális érhető el   |Neurális|
|Tahitian|  `ty`    |Csak statisztikai|  Statisztikai|
|tamil| `ta`    |Csak statisztikai|  Statisztikai|
|telugu|    `te`    |Csak Neurális|   Neurális|
|thai|  `th`    |Neurális érhető el|  Neurális|
|Tongan|    `to`    |Csak statisztikai|  Statisztikai|
|török|   `tr`    |Neurális érhető el   |Neurális|
|ukrán| `uk`    |Neurális érhető el|  Neurális|
|urdu|  `ur`    |Csak statisztikai|  Statisztikai|
|vietnami|    `vi`    |Neurális érhető el|  Neurális|
|walesi| `cy`    |Neurális érhető el|  Neurális|
|A Maya alkalmazáshoz Yucatec|  `yua`   |Csak statisztikai|  Statisztikai|

## <a name="transliteration"></a>Átírás

A Transliterate módszer a következő nyelveket támogatja. A "/", a "<> –" azt jelzi, hogy a nyelvi átírt, vagy az a felsorolt parancsfájlok közül választhat. A "-->" azt jelzi, hogy a nyelvi is csak át kell írni egy parancsfájlból származó a másikba.

| Nyelv    | Nyelvkód | Szkript | És-tárolókról | Szkript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| arab | ar | arab | <--> | Latin |
|Bengáli  | BN | bengáli | <--> | Latin |
| kínai (egyszerűsített) | zh-Hans | kínai (egyszerűsített) | <--> | Latin |
| kínai (egyszerűsített) | zh-Hans | kínai (egyszerűsített) | <--> | kínai (hagyományos) |
| kínai (hagyományos) | zh-Hant | kínai (hagyományos) | <--> | Latin |
| kínai (hagyományos) | zh-Hant | kínai (hagyományos) | <--> | kínai (egyszerűsített) |
| gudzsaráti | Gu  | gudzsaráti | --> | Latin |
| héber | Phil | héber | <--> | Latin |
| hindi | szia | Devanagári | <--> | Latin |
| japán | ja | japán | <--> | Latin |
| kannada | KN | kannada | --> | Latin |
| malajálam | ml | malajálam | --> | Latin |
| maráthi | MR | Devanagári | --> | Latin |
| orija | vagy | orija | <--> | Latin |
| pandzsábi | Pa | Gurmukhi | <--> | Latin  |
| szerb (cirill betűs) | az SR-Cyrl | Cyrilice  | --> | Latin |
| szerb (latin betűs) | az SR-Latn | Latin | --> | Cyrilice |
| tamil | TA | tamil | --> | Latin |
| telugu | Te | telugu | --> | Latin |
| thai | . | thai | <--> | Latin |

## <a name="dictionary"></a>Szótár

A szótár, vagy a Keresés és a példák módszerekkel angol a következő nyelveket támogatja.

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
| portugál      | `pt`          |
| román      | `ro`          |
| orosz      | `ru`          |
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

## <a name="languages-detected-by-the-detect-method"></a>Az észlelési módszer által észlelt nyelvek

Az alábbi nyelveken észlelik az észlelési módszer. Május észleli, hogy a Microsoft Translator nem fordítható le nyelvfelismerés.

| Nyelv    |
|:----------- |
| afrikaans |
| albán |
| arab |
| baszk |
| belorusz |
| bolgár |
| katalán |
| kínai |
| kínai (egyszerűsített) |
| kínai (hagyományos) |
| horvát |
| cseh |
| dán |
| holland |
| Angol |
| Esperanto |
| észt |
| finn |
| francia |
| galíciai |
| német |
| görög |
| Haitian Creole |
| héber |
| hindi |
| magyar |
| izlandi |
| indonéz |
| ír |
| olasz |
| japán |
| koreai |
| Ázsiai Kurd (arab) |
| Ázsiai Kurd (latin betűs) |
| Latin |
| lett |
| litván |
| macedón |
| maláj |
| máltai |
| norvég |
| norvég (nynorsk) |
| pasto |
| perzsa |
| lengyel |
| portugál |
| román |
| orosz |
| szerb (cirill betűs) |
| szerb (latin betűs) |
| szlovák |
| szlovén |
| Szomáli |
| spanyol |
| szuahéli |
| svéd |
| Tagalog |
| telugu |
| thai |
| török |
| ukrán |
| urdu |
| üzbég (cirill betűs) |
| özbeg (latin betűs) |
| vietnami |
| walesi |
| Jiddis |

## <a name="access-the-list-programmatically"></a>A lista programozott elérése

Programozott módon használja a 3.0-s verzió API nyelvek működésének támogatott nyelvek listáját is elérheti. A lista funkció, nyelvi kódját, valamint a nyelv neve szerint angolul vagy bármely más támogatott nyelv tekintheti meg. Ez a lista automatikusan frissül a Microsoft Translator szolgáltatás, amint elérhetővé válnak az új nyelvek.

[Nyelvek művelet segédanyagok megtekintése](reference/v3-0-languages.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Hozzáférés a listában, a Microsoft Translator webhelyen

A Microsoft Translator webhely a nyelvek egy gyors pillantást mutatja be, a Translator Text és a Speech API-k által támogatott összes nyelv. Ez a lista nem tartalmazza a fejlesztői jellemző információkat, például nyelvi kódot.

[Nyelvek listáját lásd:](https://www.microsoft.com/translator/languages.aspx)
