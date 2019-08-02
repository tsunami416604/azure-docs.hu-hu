---
title: Korlátok – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk az Azure Cognitive Services Language Understanding (LUIS) ismert korlátait tartalmazza. A LUIS több határ területeken rendelkezik. Modell határ szándék fog vonatkozni, az entitások és a szolgáltatások a LUIS szabályozza. A kvótakorlát kulcs típusa alapján. Billentyűkombináció szabályozza a LUIS-webhelyet.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6d4991a0a05bbdd7143987bfa227cc40732cda35
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639246"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>A LUIS-modell és-kulcsok határai
A LUIS több határ területeken rendelkezik. Az első a [modell határ](#model-boundaries), amely szabályozza a szándék fog vonatkozni, az entitások és az intelligens HANGFELISMERÉSI szolgáltatások. A második terület [kvótakorlát](#key-limits) kulcs típusa alapján. A határokat egy harmadik területe a [kombináció billentyűzet](#keyboard-controls) szabályozni a LUIS-webhelyet. A negyedik terület a [világ régió hozzárendelése](luis-reference-regions.md) webhely készítése a LUIS és a LUIS között [végpont](luis-glossary.md#endpoint) API-k. 


## <a name="model-boundaries"></a>Modell határok

Ha az alkalmazása meghaladja a LUIS-modell korlátait és határait, érdemes lehet [Luis küldő](luis-concept-enterprise.md#dispatch-tool-and-model) alkalmazást vagy [Luis](luis-container-howto.md)-tárolót használni. 

|Terület|Korlát|
|--|:--|
| [Alkalmazás neve][luis-get-started-create-app] | \* Maximális alapértelmezett karakter |
| [Kötegelt tesztelés][batch-testing]| 10-adatkészletek, 1000 utterances Adatkészletenként|
| Explicit listája | 50 alkalmazásonként|
| Külső entitások | Nincs korlát |
| [Leképezések][intents]|500/alkalmazás: 499 az egyéni leképezések és a kötelező _nincs_ szándék.<br>A [küldő-alapú](https://aka.ms/dispatch-tool) alkalmazás megfelelő 500 elküldési forrással rendelkezik.|
| [Entitások listája](./luis-concept-entity-types.md) | Szülő 50, gyermek: 20 000 elem. Kanonikus név * alapértelmezett karakterek maximális száma. Szinonima hossza korlátozás rendelkeznie. |
| [Gépi megtanult entitások + szerepkörök](./luis-concept-entity-types.md):<br> összetett<br>egyszerű<br>entitás szerepköre|Legfeljebb 100 szülő entitás vagy 330 entitás, amely a felhasználó által elsőként megjelenő korlátot korlátozza. A szerepkör entitásként számít a határ szempontjából. Ilyen például egy olyan egyszerű entitás, amely 2 szerepkörrel rendelkezik: 1 összetett + 1 egyszerű + 2 szerepkör = 4 az 330 entitásból.|
| [Előnézet – dinamikus lista entitásai](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 a ~ 1k/Query előrejelzési végpontra vonatkozó kérelem listája|
| [Minták](luis-concept-patterns.md)|500 minták alkalmazásonként.<br>Minta legfeljebb 400 karakter.<br>3 Pattern.any entitások mintában<br>A beágyazott 2 opcionális szövegek mintában maximális|
| [Pattern.any](./luis-concept-entity-types.md)|100, alkalmazásonként mintában 3 pattern.any entitások |
| [Kifejezések listája][phrase-list]|10 kifejezés listák, 5000 tétel / listája|
| [Előre összeállított entitások](./luis-prebuilt-entities.md) | korlátlan|
| [Reguláris kifejezés entitások](./luis-concept-entity-types.md)|20 entitások<br>maximum 500 karakter. egy entitás mintával|
| [Szerepkörök](luis-concept-roles.md)|300 szerepkörök alkalmazásonként. 10 szerepkörök / entitás|
| [Utterance][utterances] | 500 karakternél|
| [Hosszúságú kimondott szöveg][utterances] | 15 000/alkalmazás – a hosszúságú kimondott szöveg száma nem korlátozható a szándék alapján|
| [Verziók](luis-concept-version.md)| korlátlan |
| [Verzió neve][luis-how-to-manage-versions] | 10 karakter hosszúságú alfanumerikus karaktereket, illetve időszak korlátozódik (.) |

\* Alapértelmezett karakter legfeljebb 50 karakter hosszú lehet. 

<a name="intent-and-entity-naming"></a>

## <a name="object-naming"></a>Objektum elnevezése

Ne használja az alábbi karaktereket a következő nevekben.

|Object|Karakterek kizárása|
|--|--|
|Cél, entitás és szerepkör neve|`:`<br>`$`|
|Verzió neve|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Kulcshasználat

A nyelv értelmezése külön kulcsokkal rendelkezik, egy típust a szerzői műveletekhez, és egy típust az előrejelzési végpont lekérdezéséhez. Ha többet szeretne megtudni a főbb típusok közötti különbségekről, olvassa el az [előrejelzési végpont kulcsainak készítése a Luis-ben](luis-concept-keys.md)című témakört.

## <a name="key-limits"></a>Kulcs korlátok

Az Authoring Tool kulcs létrehozási és -végpont eltérő korlátokkal rendelkeznek. A LUIS végponti kulcs je platná pouze Pro végpont lekérdezéseket.


|Kulcs|Szerzői műveletek|Végpont|Cél|
|--|--|--|--|
|Language Understanding authoring/Starter|1 millió/hó, 5/s|1 ezer/hó, 5/s|A LUIS-alkalmazás készítése|
|Language Understanding [előfizetés][pricing] – ingyenes F0-réteg |érvénytelen|10 ezer/hó, 5/s|A LUIS-végpontra lekérdezése|
|Language Understanding [előfizetés][pricing] – S0 – alapszintű|érvénytelen|50/mp|A LUIS-végpontra lekérdezése|
|Kognitív szolgáltatás [][pricing] előfizetése – standard szintű S0|érvénytelen|50/mp|A LUIS-végpontra lekérdezése|
|[Vélemények elemzése integráció](luis-how-to-publish-app.md#enable-sentiment-analysis)|érvénytelen|díjmentes|Vélemények információk, beleértve a kulcsszókeresés adatok hozzáadása |
|[Beszédfelismerési integráció](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|érvénytelen|Ezer 5.50 USD/1 végpont kérelmek|Használja a beszélt utterance (kifejezés) konvertálása szöveges utterance (kifejezés) és a LUIS eredményeket adja vissza|

## <a name="keyboard-controls"></a>Billentyűzet vezérlők

|Bevitelt a billentyűzetről | Leírás | 
|--|--|
|Vezérlő + E|Váltás a jogkivonatok és utterances listán entitások között|

## <a name="website-sign-in-time-period"></a>Webhely bejelentkezési időtartama

A bejelentkezési hozzáférés **60 perc**. Ez az időtartam, miután ezt a hibát kap. Újra be kell jelentkeznie.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
