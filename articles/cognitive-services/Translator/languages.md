---
title: Translator Text API támogatott nyelvek
titlesuffix: Azure Cognitive Services
description: Tekintse meg a Translator Text API által támogatott nyelveket.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: v-jansko
ms.openlocfilehash: 9318a562fad00f61d50fdce19b8b84b62c294cba
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124848"
---
# <a name="supported-languages-in-the-translator-text-api"></a>Támogatott nyelvek a Translator Text API

A Translator Text API a következő nyelveket támogatja a szöveg a fordítás. Neurális gépi fordítás (NMT) magas színvonalú mesterséges Intelligenciával rendelkező gép fordítások új szabványa, és használja, a Translator Text API v3-as, akkor egy Neurális rendszeren érhető el az alapértelmezett érhető el. Neurális gépi fordítás érhető el a v2-ben a "generalnn" kategóriája használatával.

[További információ arról, hogyan működik a gépi fordítás](https://www.microsoft.com/translator/mt.aspx)

| Nyelv    | Fordítási típusa |Nyelvkód |
|:----------- |:-------:|:-------------:|
| afrikaans      | Statisztikai |`af`          |
| arab      | Neurális | `ar`          |
| Arab, Levantine    | Neurális | `apc`
| Bengáli      | Neurális |`bn`          |
| bosnyák (latin betűs)      | Statisztikai |`bs`          |
| bolgár     |  Neurális |`bg`          |
| Kantoni (hagyományos)      | Statisztikai |`yue`          |
| katalán      | Statisztikai |`ca`          |
| kínai (egyszerűsített)        |  Neurális |`zh-Hans`          | 
| kínai (hagyományos)        |  Neurális |`zh-Hant`          |
| horvát      | Neurális |`hr`          |
| cseh        |  Neurális |`cs`          |
| dán        |  Neurális |`da`          |
| holland        |  Neurális |`nl`          |
| Angol       |  Neurális |`en`          |
| észt      | Neurális |`et`          |
| Fijian      | Statisztikai |`fj`          |
| filippínó      | Statisztikai |`fil`          |
| finn      | Neurális |`fi`          |
| francia        |  Neurális |`fr`          |
| német       |  Neurális |`de`          |
| görög      | Neurális |`el`          |
| Haitian Creole      | Statisztikai |`ht`          |
| héber      | Neurális |`he`          |
| hindi        |  Neurális |`hi`          |
| Hmong Daw      | Statisztikai |`mww`          |
| magyar      | Neurális |`hu`          |
| izlandi      |  Neurális |`is`           |
| indonéz      | Statisztikai |`id`          |
| olasz        |  Neurális |`it`          |
| japán        |  Neurális |`ja`          |
| szuahéli      | Statisztikai |`sw`          |
| Klingon      | Statisztikai |`tlh`          |
| Klingon (plqaD)      | Statisztikai |`tlh-Qaak`          |
| koreai        |  Neurális |`ko`          |
| lett      | Neurális |`lv`          |
| litván      | Neurális |`lt`          |
| Madagaszkári      | Statisztikai |`mg`          |
| maláj      | Statisztikai |`ms`          |
| máltai      | Statisztikai |`mt`          |
| norvég        |  Neurális |`nb`          |
| perzsa      | Statisztikai |`fa`          |
| lengyel        |  Neurális |`pl`          |
| portugál        |  Neurális |`pt`          |
| Queretaro Otomi      | Statisztikai |`otq`          |
| román        |  Neurális |`ro`          |
| orosz        |  Neurális |`ru`          |
| Samoa      | Statisztikai |`sm`          |
| szerb (cirill betűs)      | Statisztikai |`sr-Cyrl`          |
| szerb (latin betűs)      | Statisztikai |`sr-Latn`          |
| szlovák     | Neurális |`sk`          |
| szlovén      | Neurális |`sl`          |
| spanyol        |  Neurális |`es`          |
| svéd        |  Neurális |`sv`          |
| Tahitian      | Statisztikai |`ty`          |
| tamil      | Statisztikai |`ta`          |
| telugu   | Neurális   | `te` |
| thai      | Neurális |`th`          |
| Tongan      | Statisztikai |`to`          |
| török       |  Neurális |`tr`          |
| ukrán      | Neurális |`uk`          |
| urdu      | Statisztikai |`ur`          |
| vietnami      | Neurális |`vi`          |
| walesi      | Neurális |`cy`          |
| A Maya alkalmazáshoz Yucatec      | Statisztikai |`yua`          |

## <a name="transliteration"></a>Átbetűzésű

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
| Malasian | ml | malajálam | --> | Latin |
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
