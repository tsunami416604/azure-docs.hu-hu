---
title: Nyelvi támogatás – Translator Text API
titleSuffix: Azure Cognitive Services
description: A Translator Text API által támogatott természetes nyelvek listája.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 6ab500fe6a7f288bcfbdc9168ada8bdad5bdb6ea
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772953"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>A Translator Text API nyelvi és területi támogatása

A Translator Text API az alábbi nyelveket támogatja szöveg szöveggé fordításához. A neurális gépi fordítás (NMT) a kiváló minőségű, mesterséges intelligenciát használó gépi fordítások új szabványa, amely a Translator Text API v3-as verziójában érhető el, amikor egy neurális rendszer elérhető.

[További információ a gépi fordítás működéséről](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Fordítás

**V2 Translator API**

> [!NOTE]
> A v2 2018. április 30-ig elavult. Telepítse át az alkalmazásait a v3-ra, hogy kihasználhassa a kizárólag a v3 verzióban elérhető új funkciókat.

* Csak statisztikai: Ehhez a nyelvhez nem érhető el neurális rendszer.
* Rendelkezésre álló neurális: Egy neurális rendszer érhető el. Használja a paramétert `category=generalnn` a neurális rendszerek eléréséhez.
* Neurális alapértékek: A neurális az alapértelmezett fordítási rendszer. A paraméter `category=smt` használatával férhet hozzá a statisztikai rendszerhez a Microsoft Translator hub használatával.
* Csak neurális: Csak a neurális fordítás érhető el.

**V3 Translator API** A v3 Translator API alapértelmezés szerint a neurális, a statisztikai rendszerek pedig csak akkor érhetők el, ha nem létezik neurális rendszer.

> [!NOTE]
> Jelenleg a neurális nyelvek egy részhalmaza érhető el az egyéni fordítóban, és fokozatosan adunk hozzá továbbikat. [Az egyéni fordítóban jelenleg elérhető nyelvek megtekintése](#customization).

|Nyelv|  Nyelvkód|  V2 API| V3 API|
|:-----|:-----:|:-----|:-----|
|afrikaans| `af`    |Csak statisztikai|  Neurális|
|arab|    `ar`    |Rendelkezésre álló neurális|  Neurális|
|Bengáli|    `bn`    |Rendelkezésre álló neurális|  Neurális|
|bosnyák (latin betűs)|   `bs`    |Rendelkezésre álló neurális|  Neurális|
|bolgár| `bg`    |Rendelkezésre álló neurális|  Neurális|
|Kantoni (hagyományos)|   `yue`   |Csak statisztikai|  Statisztikai|
|katalán|   `ca`    |Csak statisztikai|  Statisztikai|
|kínai (egyszerűsített)|    `zh-Hans`   |Neurális alapértelmezett |Neurális|
|kínai (hagyományos)|   `zh-Hant`   |Neurális alapértelmezett |Neurális|
|horvát|  `hr`    |Rendelkezésre álló neurális|  Neurális|
|cseh| `cs`    |Rendelkezésre álló neurális|  Neurális|
|dán|    `da`    |Rendelkezésre álló neurális   |Neurális|
|holland| `nl`    |Rendelkezésre álló neurális|  Neurális|
|Angol|   `en`    |Rendelkezésre álló neurális|  Neurális|
|észt|  `et`    |Rendelkezésre álló neurális|  Neurális|
|Fijian|    `fj`    |Csak statisztikai|  Statisztikai|
|filippínó|  `fil`   |Csak statisztikai|  Statisztikai|
|finn|   `fi`    |Rendelkezésre álló neurális|  Neurális|
|francia|    `fr`    |Rendelkezésre álló neurális|  Neurális|
|német|    `de`    |Rendelkezésre álló neurális|  Neurális|
|görög| `el`    |Rendelkezésre álló neurális|  Neurális|
|Haitian Creole|    `ht`    |Csak statisztikai   |Statisztikai|
|héber |`he`   |Rendelkezésre álló neurális   |Neurális|
|hindi| `hi`    |Neurális alapértelmezett|    Neurális|
|Hmong Daw| `mww`   |Csak statisztikai|  Statisztikai|
|magyar| `hu`    |Rendelkezésre álló neurális|  Neurális|
|izlandi| `is`    |Csak neurális|   Neurális|
|indonéz|    `id`    |Csak statisztikai|  Statisztikai|
|olasz|   `it`    |Rendelkezésre álló neurális|  Neurális|
|japán|  `ja`    |Rendelkezésre álló neurális|  Neurális|
|szuahéli| `sw`    |Csak statisztikai|  Statisztikai|
|Klingon|   `tlh`   |Csak statisztikai|  Statisztikai|
|Klingon (plqaD)|   `tlh-Qaak`  |Csak statisztikai|  Statisztikai|
|koreai |`ko`   |Rendelkezésre álló neurális|  Neurális|
|lett|   `lv`    |Rendelkezésre álló neurális|  Neurális|
|litván|    `lt`    |Rendelkezésre álló neurális|  Neurális|
|Madagaszkári|  `mg`    |Csak statisztikai|  Statisztikai|
|maláj| `ms`    |Csak statisztikai   |Statisztikai|
|máltai|   `mt`    |Csak statisztikai|  Statisztikai|
|norvég| `nb`    |Rendelkezésre álló neurális|  Neurális|
|perzsa|   `fa`    |Rendelkezésre álló neurális|  Neurális|
|lengyel|    `pl`    |Rendelkezésre álló neurális|  Neurális|
|portugál|    `pt`    |Rendelkezésre álló neurális|  Neurális|
|Queretaro Otomi|   `otq`   |Csak statisztikai|  Statisztikai|
|román|  `ro`    |Rendelkezésre álló neurális|  Neurális|
|orosz|   `ru`    |Rendelkezésre álló neurális|  Neurális|
|Samoa|    `sm`    |Csak statisztikai|  Statisztikai|
|szerb (cirill betűs)|    `sr-Cyrl`   |Csak statisztikai|  Statisztikai|
|szerb (latin betűs)|   `sr-Latn`   |Csak statisztikai   |Statisztikai|
|szlovák|    `sk`    |Rendelkezésre álló neurális|  Neurális|
|szlovén| `sl`    |Rendelkezésre álló neurális|  Neurális|
|spanyol|   `es`    |Rendelkezésre álló neurális|  Neurális|
|svéd|   `sv`    |Rendelkezésre álló neurális   |Neurális|
|Tahitian|  `ty`    |Csak statisztikai|  Statisztikai|
|tamil| `ta`    |Csak statisztikai|  Statisztikai|
|telugu|    `te`    |Csak neurális|   Neurális|
|thai|  `th`    |Rendelkezésre álló neurális|  Neurális|
|Tongan|    `to`    |Csak statisztikai|  Statisztikai|
|török|   `tr`    |Rendelkezésre álló neurális   |Neurális|
|ukrán| `uk`    |Rendelkezésre álló neurális|  Neurális|
|urdu|  `ur`    |Csak statisztikai|  Statisztikai|
|vietnami|    `vi`    |Rendelkezésre álló neurális|  Neurális|
|walesi| `cy`    |Rendelkezésre álló neurális|  Neurális|
|A Maya alkalmazáshoz Yucatec|  `yua`   |Csak statisztikai|  Statisztikai|

## <a name="transliteration"></a>Transliteration

A Átbetűzés metódus a következő nyelveket támogatja. A "to/from", "<->" kifejezés azt jelzi, hogy a nyelv a felsorolt parancsfájlokból vagy azok egyikére is átadható. A "-->" érték azt jelzi, hogy a nyelv csak az egyik parancsfájlból a másikba való átírására használható.

| Nyelv    | Nyelvkód | Parancsfájl | /Feladó | Parancsfájl|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| arab | `ar` | arab`Arab` | <--> | Latin betűs`Latn` |
|Bengáli  | `bn` | bengáli`Beng` | <--> | Latin betűs`Latn` |
| kínai (egyszerűsített) | `zh-Hans` | kínai (egyszerűsített)`Hans`| <--> | Latin betűs`Latn` |
| kínai (egyszerűsített) | `zh-Hans` | kínai (egyszerűsített)`Hans`| <--> | kínai (hagyományos)`Hant`|
| kínai (hagyományos) | `zh-Hant` | kínai (hagyományos)`Hant`| <--> | Latin betűs`Latn` |
| kínai (hagyományos) | `zh-Hant` | kínai (hagyományos)`Hant`| <--> | kínai (egyszerűsített)`Hans` |
| gudzsaráti | `gu`  | gudzsaráti`Gujr` | --> | Latin betűs`Latn` |
| héber | `he` | héber`Hebr` | <--> | Latin betűs`Latn` |
| hindi | `hi` | Devanagari`Deva` | <--> | Latin betűs`Latn` |
| japán | `ja` | japán`Jpan` | <--> | Latin betűs`Latn` |
| kannada | `kn` | Kannada `Knda` | --> | Latin betűs`Latn` |
| malajálam | `ml` | malajálam`Mlym` | --> | Latin betűs`Latn` |
| maráthi | `mr` | Devanagari`Deva` | --> | Latin betűs`Latn` |
| orija | `or` | Oriya`Orya` | <--> | Latin betűs`Latn` |
| pandzsábi | `pa` | Gurmukhi`Guru`  | <--> | Latin betűs`Latn`  |
| szerb (cirill betűs) | `sr-Cyrl` | Cirill betűs`Cyrl`  | --> | Latin betűs`Latn` |
| szerb (latin betűs) | `sr-Latn` | Latin betűs`Latn` | --> | Cirill betűs`Cyrl`|
| tamil | `ta` | tamil`Taml` | --> | Latin betűs`Latn` |
| telugu | `te` | telugu`Telu` | --> | Latin betűs`Latn` |
| thai | `th` | thai`Thai` | <--> | Latin betűs`Latn` |

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

## <a name="detect"></a>Detect

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
| olasz      | `it`          |
| japán      | `ja`          |
|szuahéli| `sw`    |
| koreai      | `ko`          |
| lett      | `lv`          |
| litván      | `lt`          |
|Madagaszkári|  `mg`    |
| norvég      | `nb`          |
| lengyel      | `pl`          |
| portugál      | `pt`          |
| román      | `ro`          |
| orosz      | `ru`          |
|Samoa|    `sm`    |
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
