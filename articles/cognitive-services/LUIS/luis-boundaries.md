---
title: Határok és a korlátok a Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: Ez a cikk az Azure Cognitive Services Language Understanding (LUIS) ismert korlátait tartalmazza. A LUIS több határ területeken rendelkezik. Modell határ szándék fog vonatkozni, az entitások és a szolgáltatások a LUIS szabályozza. A kvótakorlát kulcs típusa alapján. Billentyűkombináció szabályozza a LUIS-webhelyet.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 215f8305c19f0b12a8b240abb16a30f0ce852502
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45629015"
---
# <a name="luis-boundaries"></a>A LUIS határok
A LUIS több határ területeken rendelkezik. Az első a [modell határ](#model-boundaries), amely szabályozza a szándék fog vonatkozni, az entitások és az intelligens HANGFELISMERÉSI szolgáltatások. A második terület [kvótakorlát](#key-limits) kulcs típusa alapján. A határokat egy harmadik területe a [kombináció billentyűzet](#keyboard-controls) szabályozni a LUIS-webhelyet. A negyedik terület a [világ régió hozzárendelése](luis-reference-regions.md) webhely készítése a LUIS és a LUIS között [végpont](luis-glossary.md#endpoint) API-k. 


## <a name="model-boundaries"></a>Modell határok

|Terület|Korlát|
|--|:--|--|
| [Alkalmazás neve][luis-get-started-create-app] | * Maximális alapértelmezett karakter |
| [Batch-tesztelés][batch-testing]| 10-adatkészletek, 1000 utterances Adatkészletenként|
| **[Összetett](./luis-concept-entity-types.md)|legfeljebb 10 gyermekkel rendelkező 100 |
| Explicit listája | 50 alkalmazásonként|
| **[Hierarchikus](./luis-concept-entity-types.md) |legfeljebb 10 gyermekkel rendelkező 100 |
| [Leképezések][intents]|500-as alkalmazásonként<br>[Feladó-alapú](https://aka.ms/dispatch-tool) alkalmazás rendelkezik a megfelelő 500 dispatch-források|
| [Entitások listája](./luis-concept-entity-types.md) | Szülő: 50, gyermek: 20 000 elem. Kanonikus név * alapértelmezett karakterek maximális száma. Szinonima hossza korlátozás rendelkeznie. |
| [Minták](luis-concept-patterns.md)|500 minták alkalmazásonként.<br>Minta legfeljebb 400 karakter.<br>3 Pattern.any entitások mintában<br>A beágyazott 2 opcionális szövegek mintában maximális|
| [Pattern.any](./luis-concept-entity-types.md)|100, alkalmazásonként mintában 3 pattern.any entitások |
| [Kifejezéslista][phrase-list]|10 kifejezés listák, 5000 tétel / listája|
| [Előre összeállított entitások](./luis-prebuilt-entities.md) | korlátlan|
| [Reguláris kifejezés entitások](./luis-concept-entity-types.md)|20 entitások<br>maximum 500 karakter. egy entitás mintával|
| [Szerepkörök](luis-concept-roles.md)|300 szerepkörök alkalmazásonként. 10 szerepkörök / entitás|
| **[Egyszerű](./luis-concept-entity-types.md)| 100 entitást|
| [Utterance (kifejezés)][utterances] | 500 karakternél|
| [Kimondott szöveg][utterances] | 15 000 alkalmazásonként|
| [Verziók](luis-concept-version.md)| korlátlan |
| [Verziónév][luis-how-to-manage-versions] | 10 karakter hosszúságú alfanumerikus karaktereket, illetve időszak korlátozódik (.) |

* Alapértelmezett karakter legfeljebb 50 karakter hosszú lehet. 

** Az egyszerű, hierarchikus és összetett entitások teljes száma legfeljebb 100 lehet. Hierarchikus entitások, az összetett entitások, a egyszerű entitások és a hierarchikus gyermekek entitások teljes száma nem haladhatja meg a 330. 

## <a name="intent-and-entity-naming"></a>Leképezés és entitások elnevezése
A leképezés és az entitás neve ne használja a következő karaktereket:

|Karakter|Name (Név)|
|--|--|
|`{`|Bal oldali kapcsos zárójel|
|`}`|Jobb oldali kapcsos zárójel|
|`[`|Nyitó zárójel|
|`]`|Záró zárójel|
|`\`|Fordított perjel|

## <a name="key-limits"></a>Kulcs korlátok
Az Authoring Tool kulcs létrehozási és -végpont eltérő korlátokkal rendelkeznek. A LUIS végponti kulcs je platná pouze Pro végpont lekérdezéseket.

|Kulcs|Szerzői műveletek|Végpont|Cél|
|--|--|--|--|
|Szerzői/alapszintű|1 millió/hó, 5/s|1 ezer/hó, 5/s|A LUIS-alkalmazás készítése|
|[Előfizetés] [ pricing] - F0 – ingyenes csomag |érvénytelen|10 ezer/hó, 5/s|A LUIS-végpontra lekérdezése|
|[Előfizetés] [ pricing] - S0 - alapszintű csomag|érvénytelen|50/mp|A LUIS-végpontra lekérdezése|
|[Vélemények elemzése integráció](luis-how-to-publish-app.md#enable-sentiment-analysis)|érvénytelen|díjmentes|Vélemények információk, beleértve a kulcsszókeresés adatok hozzáadása |
|Beszéd-integráció|érvénytelen|Ezer 5.50 USD/1 végpont kérelmek|Használja a beszélt utterance (kifejezés) konvertálása szöveges utterance (kifejezés) és a LUIS eredményeket adja vissza|

## <a name="keyboard-controls"></a>Billentyűzet vezérlők

|Bevitelt a billentyűzetről | Leírás | 
|--|--|
|Vezérlő + E|Váltás a jogkivonatok és utterances listán entitások között|

## <a name="website-sign-in-time-period"></a>Jelentkezzen be az adott időszakban a webhely

A bejelentkezési hozzáférés **60 perc**. Ez az időtartam, miután ezt a hibát kap. Jelentkezzen be újra kell.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
