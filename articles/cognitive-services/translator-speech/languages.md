---
title: Támogatott nyelvek – Translator Speech API
titlesuffix: Azure Cognitive Services
description: A Translator Speech API által támogatott nyelvek megtekintése.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 3/5/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2f5b48f2dbc2d109c03613676c6a119fd971603b
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965446"
---
# <a name="languages-supported-by-the-translator-speech-api"></a>A Translator Speech API által támogatott nyelvek

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

A beszéd fordításához a következő nyelvek támogatottak. Ha mindkét nyelv támogatja a beszédfelismerést, a beszéd vagy beszéd szövege elérhető. Ha a célnyelv nem támogatott a beszéd fordításához, csak a szöveg fordítása érhető el.

| Beszédfelismerési nyelvet    |
|:----------- |
| Arab (Modern Standard)      |
| Brazíliai Portugál     |
| Kínai (Mandarin)      |
| Angol      |
| francia      |
| német      |
| olasz      |
| japán      |
| orosz      |
| spanyol      |

A Translator Speech API a következő nyelveket támogatja a szöveg fordításához való beszéd céljának nyelve.

| Szöveg nyelvét    | Nyelvkód |
|:----------- |:-------------:|
| afrikaans      | `af`          |
| arab       | `ar`          |
| Bengáli      | `bn`          |
| bosnyák (latin betűs)      | `bs`          |
| bolgár      | `bg`          |
| Kantoni (hagyományos)      | `yue`          |
| katalán      | `ca`          |
| kínai (egyszerűsített)      | `zh-Hans`          |
| kínai (hagyományos)      | `zh-Hant`          |
| horvát      | `hr`          |
| cseh      | `cs`          |
| dán      | `da`          |
| holland      | `nl`          |
| Angol      | `en`          |
| észt      | `et`          |
| Fijian      | `fj`          |
| filippínó      | `fil`          |
| finn      | `fi`          |
| francia      | `fr`          |
| német      | `de`          |
| görög      | `el`          |
| Haitian Creole      | `ht`          |
| héber      | `he`          |
| hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| magyar      | `hu`          |
|izlandi|`is`          |
| indonéz      | `id`          |
| olasz      | `it`          |
| japán      | `ja`          |
| szuahéli      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| koreai      | `ko`          |
| lett      | `lv`          |
| litván      | `lt`          |
| Madagaszkári      | `mg`          |
| maláj      | `ms`          |
| máltai      | `mt`          |
| norvég      | `nb`          |
| perzsa      | `fa`          |
| lengyel      | `pl`          |
| portugál      | `pt`          |
| Queretaro Otomi      | `otq`          |
| román      | `ro`          |
| orosz      | `ru`          |
| Samoa      | `sm`          |
| szerb (cirill betűs)      | `sr-Cyrl`          |
| szerb (latin betűs)      | `sr-Latn`          |
| szlovák     | `sk`          |
| szlovén      | `sl`          |
| spanyol      | `es`          |
| svéd      | `sv`          |
| Tahitian      | `ty`          |
| tamil      | `ta`          |
| thai      | `th`          |
| Tongan      | `to`          |
| török      | `tr`          |
| ukrán      | `uk`          |
| urdu      | `ur`          |
| vietnami      | `vi`          |
| walesi      | `cy`          |
| A Maya alkalmazáshoz Yucatec      | `yua`          |

## <a name="access-the-list-programmatically"></a>A lista programozott módon való elérése

A támogatott nyelvek listáját programozott módon is elérheti a languages erőforrás használatával. A lista a nyelvi kódot és a nyelv nevét is megadja angol nyelven, vagy bármely más támogatott nyelvet. Ezt a listát a Translator Speech szolgáltatás automatikusan frissíti, mivel új nyelvek válnak elérhetővé.

A nyelvek erőforrás visszaadja a támogatott nyelvek listáját a beszéd, a szöveg és a beszéd szöveg számára. A nyelvek erőforráshoz nem szükséges a hitelesítés.

[A languages metódus kipróbálásához látogasson el az API-referenciára](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>A lista elérése a Microsoft Translator webhelyén

A nyelvek gyors áttekintéséhez a Microsoft Translator webhelyén a Translator Text és a Speech API-k által támogatott összes nyelv látható. Ez a lista nem tartalmazza a fejlesztői adatokat, például a nyelvi kódokat.

[A nyelvek listája](https://www.microsoft.com/translator/languages.aspx)
