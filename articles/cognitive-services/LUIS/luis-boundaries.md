---
title: Nyelvi ismertetése (LUIS) határok |} Microsoft Docs
titleSuffix: Azure
description: A cikkben LUIS ismert határain.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 52bda6a13422ce8f759c40bd454a6b15e92d7a5d
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110296"
---
# <a name="luis-boundaries"></a>LUIS határok
LUIS több határ területet tartalmaz. Az első a [modell határ](#model-boundaries), amely meghatározza, hogy leképezések, entitások és LUIS szolgáltatásai. A második terület [kvótakorlát](#key-limits) kulcs típusa alapján. A határok harmadik terület a [kombinációja billentyűzet](#keyboard-controls) a LUIS webhely vezérlése. A negyedik terület a [globális régió leképezése](luis-reference-regions.md) között a szerzői webhely LUIS és a LUIS [végpont](luis-glossary.md#endpoint) API-k. 


## <a name="model-boundaries"></a>Modell határok

|Terület|Korlát|
|--|:--|--|
| [Alkalmazás neve][luis-get-started-create-app] | * Maximális alapértelmezett karakter |
| [Kötegelt tesztelése][batch-testing]| 10 adatkészleteket, 1000 utterances adatkészlet egyes|
| **[Összetett](./luis-concept-entity-types.md)|legfeljebb 10 gyermek 100 |
| Explicit listája | alkalmazásonként 50|
| **[Hierarchikus](./luis-concept-entity-types.md) |legfeljebb 10 gyermek 100 |
| [Leképezések][intents]|500 alkalmazásonként<br>[Küldési alapú](https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch) alkalmazásnak megfelelő 500 feladó források|
| [Lista entitások](./luis-concept-entity-types.md) | Szülő: 50, gyermek: 20 000 elemeket. Kanonikus név * karaktert maximális alapértelmezett. Már rendelkezik hossza korlátozás. |
| [Minták](luis-concept-patterns.md)|500 minták alkalmazásonként.<br>Minta hossza legfeljebb 400 karakter lehet.<br>3 Pattern.any entitás / minta<br>Legfeljebb 2 beágyazott választható szövegek minta|
| [Pattern.any](./luis-concept-entity-types.md)|100 alkalmazásonként, 3 pattern.any entitás / minta |
| [Kifejezéslista][phrase-list]|10 kifejezés listák, 5000 elemek száma listája|
| [Előre elkészített entitások](./Pre-builtEntities.md) | nem korlátozott|
| [Reguláris kifejezés entitások](./luis-concept-entity-types.md)|20 entitások<br>500 karaktert max. egy entitás Reguláriskifejezés-mintának|
| [Szerepkörök](luis-concept-roles.md)|300 szerepkörök alkalmazásonként. 10 szerepkörök entitásonként|
| **[Egyszerű](./luis-concept-entity-types.md)| 100 entitást|
| [utterance][utterances] | 500 karakternél|
| [Utterances][utterances] | 15 000 alkalmazásonként|
| [Verzió neve][luis-how-to-manage-versions] | 10 karakterek alfanumerikus és időszak korlátozva (.) |

* Alapértelmezett karakter maximális értéke 50 karakter hosszú lehet. 

** A teljes egyszerű, hierarchikus és összetett entitások száma nem haladhatja meg a 100. A hierarchikus entitások, összetett entitások, egyszerű entitások és hierarchikus gyermekek entitások teljes száma nem haladhatja meg a 330. 

## <a name="intent-and-entity-naming"></a>Leképezés és az entitás elnevezése
A célt és az entitás neve a következő karakterek nem használhatók:

|Karakter|Name (Név)|
|--|--|
|`{`|Bal oldali kapcsos zárójelet tartalmazniuk|
|`}`|Jobb oldali kapcsos zárójelet|
|`[`|Nyitó szögletes zárójel|
|`]`|Szögletes zárójel|
|`\`|Fordított perjel|

## <a name="key-limits"></a>Korlátozza a kulcs
A szerzői műveletekhez kulcs különböző korlátozhatja a jelentéskészítő és -végpont rendelkezik. A LUIS szolgáltatási végpont kulcsa csak érvényes végpont lekérdezések.

|Kulcs|Szerzői műveletek|Végpont|Cél|
|--|--|--|--|
|Szerzői/alapszintű|1 millió/hónap, 5/másodperc|1 hó/hónap, 5/másodperc|Az LUIS alkalmazás készítése|
|[Előfizetés] [ pricing] - F0 - ingyenes szint |érvénytelen|10 év/hónap, 5/másodperc|A LUIS végpont lekérdezése|
|[Előfizetés] [ pricing] - S0 – az alapszintű csomag|érvénytelen|50/másodperc|A LUIS végpont lekérdezése|
|[Integráció a céggel kapcsolatos véleményeket elemzés](publishapp.md#enable-sentiment-analysis)|érvénytelen|nem kell fizetni|Véleményeket információt, beleértve a kulcs kifejezés adatok kinyerése hozzáadása |
|Beszéd-integráció|érvénytelen|Végpont kérelmek $5.50 USD/1 év|Alakítsa át a szóbeli utterance szöveg utterance, LUIS eredményeket adjon.|

## <a name="keyboard-controls"></a>Billentyűzet vezérlők

|Bevitel a billentyűzetről | Leírás | 
|--|--|
|Vezérlő + E|Váltás a jogkivonatokat és entitások utterances listán között|

## <a name="website-sign-in-time-period"></a>Jelentkezzen be időszakban a webhely

A rendszer a bejelentkezési elérést **60 perc**. Ebben az időszakban, miután elérhetővé válik a ezt a hibát. Jelentkezzen be újra kell.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
