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
ms.openlocfilehash: 3d25cfd39b4b4278fedf33e042d394208fd5eafc
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53713179"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>A Translator Text API nyelvéhez és régiójához támogatása

A Translator Text API a következő nyelveket támogatja a szöveg a fordítás. Neurális gépi fordítás (NMT) magas színvonalú mesterséges Intelligenciával rendelkező gép fordítások új szabványa, és használja, a Translator Text API v3-as, akkor egy Neurális rendszeren érhető el az alapértelmezett érhető el. 

[További információ arról, hogyan működik a gépi fordítás](https://www.microsoft.com/translator/mt.aspx)

**V2 Translator API**

> [!NOTE]
> V2 elavulttá 2018. április 30., és a 2019. április 30. ezt megszüntetjük.

* Csak statisztikai: Nincs Neurális rendszer nem érhető el ehhez a nyelvhez.
* Neurális érhető el: Egy Neurális rendszeren érhető el. Használja ezt a paramétert `category=generalnn` hozzáférhetnek a Neurális rendszerhez.
* Neurális alapértelmezett: Neurális, az alapértelmezett fordítási rendszer. Használja ezt a paramétert `category=smt` hozzáférhetnek a statisztikai rendszerhez a Microsoft Translator Hub való használatra.
* Neurális csak: Csak a Neurális fordítással érhető el.

**Translator API v3-as** a v3-as Translator API egy Neurális alapértelmezés szerint, és statisztikai rendszerek csak érhetők el, ha Neurális rendszert nem létezik. Egyéni a fordítót csak akkor használható, a Neurális nyelvek. 

|Nyelv|  Nyelvkód|  V2 API| V3 API|
|:-----|:-----:|:-----|:-----|
|afrikaans| `af`    |Csak statisztikai|  Neurális|
|arab|    `ar`    |Neurális érhető el|  Neurális|
|Bengáli|    `bn`    |Neurális érhető el|  Neurális|
|bosnyák (latin betűs)|   `bs`    |Neurális érhető el|  Neurális|
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
| arab | `ar` | arab `Arab` | <--> | Latin `Latn` |
|Bengáli  | `bn` | bengáli `Beng` | <--> | Latin `Latn` |
| kínai (egyszerűsített) | `zh-Hans` | kínai (egyszerűsített) `Hans`| <--> | Latin `Latn` |
| kínai (egyszerűsített) | `zh-Hans` | kínai (egyszerűsített) `Hans`| <--> | kínai (hagyományos) `Hant`|
| kínai (hagyományos) | `zh-Hant` | kínai (hagyományos) `Hant`| <--> | Latin `Latn` |
| kínai (hagyományos) | `zh-Hant` | kínai (hagyományos) `Hant`| <--> | kínai (egyszerűsített) `Hans` |
| gudzsaráti | `gu`  | gudzsaráti `Gujr` | --> | Latin `Latn` |
| héber | `he` | héber `Hebr` | <--> | Latin `Latn` |
| hindi | `hi` | Devanagári `Deva` | <--> | Latin `Latn` |
| japán | `ja` | japán `Jpan` | <--> | Latin `Latn` |
| kannada | `kn` | kannada `Knda` | --> | Latin `Latn` |
| malajálam | `ml` | malajálam `Mlym` | --> | Latin `Latn` |
| maráthi | `mr` | Devanagári `Deva` | --> | Latin `Latn` |
| orija | `or` | Orija `Orya` | <--> | Latin `Latn` |
| pandzsábi | `pa` | Gurmukhi `Guru`  | <--> | Latin `Latn`  |
| szerb (cirill betűs) | `sr-Cyrl` | Cyrilice `Cyrl`  | --> | Latin `Latn` |
| szerb (latin betűs) | `sr-Latn` | Latin `Latn` | --> | Cyrilice `Cyrl`|
| tamil | `ta` | tamil `Taml` | --> | Latin `Latn` |
| telugu | `te` | telugu `Telu` | --> | Latin `Latn` |
| thai | `th` | thai `Thai` | <--> | Latin `Latn` |

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

## <a name="detect"></a>Észlelés

Az észlelési módszer a következő nyelveket támogatja. Észleli a május azonosítani azokat a nyelveket, amelyek a Microsoft Translator nem fordítható le.

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
| üzbég (latin betűs) |
| vietnami |
| walesi |
| Jiddis |

## <a name="access-the-translator-text-api-language-list-programmatically"></a>A Translator Text API nyelvek listája programozott elérése

A Translator Text API 3.0 a nyelvek metódussal a támogatott nyelvek listáját kérheti le. A lista funkció, nyelvi kódját, valamint a nyelv neve szerint angolul vagy bármely más támogatott nyelv tekintheti meg. Ez a lista automatikusan frissül a Microsoft Translator szolgáltatás szerint az új nyelvek elérhetővé válnak.

[Nyelvek művelet segédanyagok megtekintése](reference/v3-0-languages.md)

## <a name="customization"></a>Testreszabás

A következő nyelveket is rendelkezésre áll-e a testreszabási [egyéni a fordítót](http://aka.ms/CustomTranslator).

| Nyelv    | Nyelvkód |
|:----------- |:-------------:|
| arab       | `ar`          |
| Bengáli      | `bn`          |
| bosnyák (latin betűs)      | `bs`          |
| bolgár      | `bg`          |
| kínai (egyszerűsített)      | `zh-Hans`          |
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
| olasz      | `it`          |
| japán      | `ja`          |
| koreai      | `ko`          |
| lett      | `lv`          |
| litván      | `lt`          |
| norvég      | `nb`          |
| lengyel      | `pl`          |
| portugál      | `pt`          |
| román      | `ro`          |
| orosz      | `ru`          |
| szerb (latin betűs)      | `sr-Latn`          |
| szlovák     | `sk`          |
| szlovén      | `sl`          |
| spanyol      | `es`          |
| svéd      | `sv`          |
| thai      | `th`          |
| török      | `tr`          |
| ukrán      | `uk`          |
| vietnami      | `vi`          |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Hozzáférés a listában, a Microsoft Translator webhelyen

A Microsoft Translator webhely a nyelvek egy gyors pillantást mutatja be, a Translator Text és a Speech API-k által támogatott összes nyelv. Ez a lista nem tartalmazza a fejlesztői jellemző információkat, például nyelvi kódot.

[Nyelvek listáját lásd:](https://www.microsoft.com/translator/languages.aspx)
