---
title: Korlátok
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ez a cikk az Azure Cognitive Services Language Understanding (LUIS) ismert korlátait tartalmazza. A LUIS több határ területeken rendelkezik. Modell határ szándék fog vonatkozni, az entitások és a szolgáltatások a LUIS szabályozza. A kvótakorlát kulcs típusa alapján. Billentyűkombináció szabályozza a LUIS-webhelyet.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/12/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 51f5355e365d0dcf7a103deab3356f015e75641e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897513"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>A LUIS-modell és a kulcsok határok
A LUIS több határ területeken rendelkezik. Az első a [modell határ](#model-boundaries), amely szabályozza a szándék fog vonatkozni, az entitások és az intelligens HANGFELISMERÉSI szolgáltatások. A második terület [kvótakorlát](#key-limits) kulcs típusa alapján. A határokat egy harmadik területe a [kombináció billentyűzet](#keyboard-controls) szabályozni a LUIS-webhelyet. A negyedik terület a [világ régió hozzárendelése](luis-reference-regions.md) webhely készítése a LUIS és a LUIS között [végpont](luis-glossary.md#endpoint) API-k. 


## <a name="model-boundaries"></a>Modell határok

Ha az alkalmazás meghaladja a LUIS-modell korlátait és határokat, fontolja meg egy [LUIS dispatch](luis-concept-enterprise.md#dispatch-tool-and-model) alkalmazás vagy a használatával egy [LUIS tároló](luis-container-howto.md). 

|Terület|Korlát|
|--|:--|
| [Alkalmazás neve][luis-get-started-create-app] | * Maximális alapértelmezett karakter |
| [Batch-tesztelés][batch-testing]| 10-adatkészletek, 1000 utterances Adatkészletenként|
| Explicit listája | 50 alkalmazásonként|
| [Leképezések][intents]|500-as alkalmazásonként: 499 egyéni leképezések és a szükséges _nincs_ szándékot.<br>[Feladó-alapú](https://aka.ms/dispatch-tool) alkalmazás rendelkezik a megfelelő 500 dispatch-források.|
| [Entitások listája](./luis-concept-entity-types.md) | Szülő: 50, a gyermek: 20 000 elem. Kanonikus név * alapértelmezett karakterek maximális száma. Szinonima hossza korlátozás rendelkeznie. |
| [Gép megismert entitások](./luis-concept-entity-types.md):<br> Összetett,<br>  Hierarchikus<br> Egyszerű|A korlát 100 szülő entitások (nem hierarchikus gyermekek is beleértve) vagy az entitásokat (beleértve a hierarchikus gyermekek), amelyik korlátozza a felhasználó a találatok először 330.<br><br>Egy példa a hierarchia 30 hierarchiák minden 10 gyermekkel rendelkező kifejezések lenne.  A gyermekek fog felhasználni összesen 300, és a hierarchia elemek fog felhasználni a maradék 30. |
| [Minták](luis-concept-patterns.md)|500 minták alkalmazásonként.<br>Minta legfeljebb 400 karakter.<br>3 Pattern.any entitások mintában<br>A beágyazott 2 opcionális szövegek mintában maximális|
| [Pattern.any](./luis-concept-entity-types.md)|100, alkalmazásonként mintában 3 pattern.any entitások |
| [Kifejezéslista][phrase-list]|10 kifejezés listák, 5000 tétel / listája|
| [Előre összeállított entitások](./luis-prebuilt-entities.md) | korlátlan|
| [Reguláris kifejezés entitások](./luis-concept-entity-types.md)|20 entitások<br>maximum 500 karakter. egy entitás mintával|
| [Szerepkörök](luis-concept-roles.md)|300 szerepkörök alkalmazásonként. 10 szerepkörök / entitás|
| [Utterance (kifejezés)][utterances] | 500 karakternél|
| [Kimondott szöveg][utterances] | 15 000 - alkalmazásonként nem a szándék szerint kimondott szöveg száma nincs korlátozva|
| [Verziók](luis-concept-version.md)| korlátlan |
| [Verziónév][luis-how-to-manage-versions] | 10 karakter hosszúságú alfanumerikus karaktereket, illetve időszak korlátozódik (.) |

* Alapértelmezett karakter legfeljebb 50 karakter hosszú lehet. 

## <a name="intent-and-entity-naming"></a>Leképezés és entitások elnevezése
A leképezés és az entitás neve ne használja a következő karaktereket:

|Karakter|Name (Név)|
|--|--|
|`{`|Bal oldali kapcsos zárójel|
|`}`|Jobb oldali kapcsos zárójel|
|`[`|Nyitó zárójel|
|`]`|Záró zárójel|
|`\`|Fordított perjel|

## <a name="key-usage"></a>Kulcshasználat

Nyelv megismerése rendelkezik, külön kulcsokat, egy típust a létrehozásához és lekérdezéséhez az előrejelzési végpont egy típust. Kulcstípusok közti különbségekkel kapcsolatos további tudnivalókért lásd: [szerzői műveletek és a lekérdezés előrejelzési végpont kulcsok a LUIS](luis-concept-keys.md).

## <a name="key-limits"></a>Kulcs korlátok

Az Authoring Tool kulcs létrehozási és -végpont eltérő korlátokkal rendelkeznek. A LUIS végponti kulcs je platná pouze Pro végpont lekérdezéseket.


|Kulcs|Szerzői műveletek|Végpont|Cél|
|--|--|--|--|
|Szerzői nyelv ismertetése/alapszintű|1 millió/hó, 5/s|1 ezer/hó, 5/s|A LUIS-alkalmazás készítése|
|Language Understanding [előfizetés] [ pricing] - F0 – ingyenes csomag |érvénytelen|10 ezer/hó, 5/s|A LUIS-végpontra lekérdezése|
|Language Understanding [előfizetés] [ pricing] - S0 - alapszintű csomag|érvénytelen|50/mp|A LUIS-végpontra lekérdezése|
|A cognitive Services [előfizetés] [ pricing] - S0 -, Standard csomag|érvénytelen|50/mp|A LUIS-végpontra lekérdezése|
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
