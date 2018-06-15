---
title: A Microsoft Translator API támogatott nyelv |} Microsoft Docs
description: A Microsoft Translator szöveg API által támogatott nyelvek megtekintése.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 10/30/2017
ms.author: v-jansko
ms.openlocfilehash: f388e9e39809774f307c0d1752e29f34b041ed13
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35350067"
---
# <a name="supported-languages-in-the-microsoft-translator-text-api"></a>A Microsoft Translator szöveg API támogatott nyelvek 
A Microsoft Translator szöveg API a következő nyelveket támogatja szöveg a fordításhoz. Neurális gépi fordítás (NMT) kiváló minőségű AI táplált gép fordítások új szabványa, és az alapértelmezett V3 a fordító szöveg API használatával elérhető a Neurális rendszer esetén érhető el. Neurális gépi fordítás érhető el a V2 a "generalnn" kategóriája használatával. 

[További információ a gépi fordítás működése](https://www.microsoft.com/translator/mt.aspx)

| Nyelv    | Fordítási típusa |Nyelvkód |
|:----------- |:-------:|:-------------:|
| afrikaans      | Statisztikai |`af`          |
| arab      | Neurális | `ar`          |
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
| thai      | Neurális |`th`          |
| Tongan      | Statisztikai |`to`          |
| török       |  Neurális |`tr`          |
| ukrán      | Neurális |`uk`          |
| urdu      | Statisztikai |`ur`          |
| vietnami      | Neurális |`vi`          |
| walesi      | Neurális |`cy`          |
| Yucatec Maya      | Statisztikai |`yua`          |

## <a name="transliteration"></a>Transliteration

A Transliterate metódus a következő nyelveket támogatja. A "/", a "<>--" azt jelenti, hogy a nyelvi átírt, a vagy valamelyik felsorolt parancsfájlok. A "-->" azt jelzi, hogy a nyelvi is csak át kell írni egy parancsfájlt a a másikra.

| Nyelv    | Nyelvkód | Szkript | És a | Szkript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| arab | ar | arab | <--> | Latin |
|Bengáli  | BN | bengáli | <--> | Latin |
| kínai (egyszerűsített) | zh-Hans | kínai (egyszerűsített) | <--> | Latin |
| kínai (egyszerűsített) | zh-Hans | kínai (egyszerűsített) | <--> | kínai (hagyományos) |
| kínai (hagyományos) | zh-Hant | kínai (hagyományos) | <--> | Latin |
| kínai (hagyományos) | zh-Hant | kínai (hagyományos) | <--> | kínai (egyszerűsített) |
| gudzsaráti | Gu  | gudzsaráti | --> | Latin |
| héber | Ezután | héber | <--> | Latin |
| hindi | szia | Dévanágari | <--> | Latin |
| japán | ja | japán | <--> | Latin |
| kannada | KN | kannada | --> | Latin |
| Malasian | ml | malajálam | --> | Latin |
| maráthi | MR | Dévanágari | --> | Latin |
| orija | vagy | orija | <--> | Latin |
| pandzsábi | szolgáltatói | Gurmukhi | <--> | Latin  |
| szerb (cirill betűs) | az SR-Cyrl | Cirill  | --> | Latin |
| szerb (latin betűs) | az SR-Latn | Latin | --> | Cirill |
| tamil | TA | tamil | --> | Latin |
| telugu | Te | telugu | --> | Latin |
| thai | . | thai | <--> | Latin |

## <a name="dictionary"></a>Könyvtár

A szótár vagy a Keresés és példákat módszerekkel angol arról a következő nyelveket támogatja. 

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

## <a name="languages-detected-by-the-detect-method"></a>A hibakeresés metódus által észlelt nyelvek

A következő nyelvek észlelik a hibakeresés metódust. Előfordulhat, hogy észlelni észleli a nyelveket, amelyek a Microsoft Translator nem fordítható le. 

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
| Chinese_Simplified |
| Chinese_Traditional |
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
| Haitian_Creole |
| héber |
| hindi |
| magyar |
| izlandi |
| indonéz |
| ír |
| olasz |
| japán |
| koreai |
| Kurdish_Arabic |
| Kurdish_Latin |
| Latin |
| lett |
| litván |
| macedón |
| maláj |
| máltai |
| norvég |
| Norwegian_Nynorsk |
| pasto |
| perzsa |
| lengyel |
| portugál |
| román |
| orosz |
| Serbian_Cyrillic |
| Serbian_Latin |
| szlovák |
| szlovén |
| Szomáli |
| spanyol |
| szuahéli |
| svéd |
| Tagalog |
| thai |
| török |
| ukrán |
| urdu |
| Uzbek_Cyrillic |
| Uzbek_Latin |
| vietnami |
| walesi |
| Yiddish |

## <a name="access-the-list-programmatically"></a>Programozott hozzáférés a lista

Elérheti V3.0 szöveg API a nyelvek művelet programozott módon támogatott nyelvek listáját. Angol, vagy bármely más támogatott nyelvi szolgáltatás, a nyelvi kódot, valamint a nyelv nevének a listában tekintheti meg. Amint elérhetővé válnak az új nyelvek a lista automatikusan frissíti a Microsoft Translator szolgáltatás.

[Nyelvek művelet referenciadokumentációt megtekintése](/reference/languages.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Hozzáférés a Microsoft Translator webhelyen listája

A Microsoft Translator webhely nyelvek gyorsan át, a fordító szöveg és Diktálásfelismerési API-k által támogatott összes nyelvet jeleníti meg. Ez a lista nem tartalmazza a nyelvkódjait például developer-specifikus adatait.

[Nyelvek listáját lásd:](https://www.microsoft.com/translator/languages.aspx)
