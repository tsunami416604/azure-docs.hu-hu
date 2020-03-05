---
title: Korlátok – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk az Azure Cognitive Services Language Understanding (LUIS) ismert korlátait tartalmazza. A LUIS több határ területeken rendelkezik. Modell határ szándék fog vonatkozni, az entitások és a szolgáltatások a LUIS szabályozza. A kvótakorlát kulcs típusa alapján. Billentyűkombináció szabályozza a LUIS-webhelyet.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d584b00caef628eb9dfd085b1fdce2bb7b353988
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273514"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>A LUIS-modell és-kulcsok határai
A LUIS több határ területeken rendelkezik. Az első a [modell határa](#model-boundaries), amely a Luis szándékait, entitásait és szolgáltatásait vezérli. A második régió a kulcs típusa alapján [korlátozza a kvótákat](#key-limits) . A határ harmadik területe a LUIS webhely vezérlésére szolgáló [billentyűkombináció](#keyboard-controls) . A negyedik terület az a [régió](luis-reference-regions.md) , amely a Luis authoring webhelye és a Luis [Endpoint](luis-glossary.md#endpoint) API-k között van.


## <a name="model-boundaries"></a>Modell határok

Ha az alkalmazása meghaladja a LUIS-modell korlátait és határait, érdemes lehet [Luis küldő](luis-concept-enterprise.md#dispatch-tool-and-model) alkalmazást vagy [Luis-tárolót](luis-container-howto.md)használni.

|Terület|Korlát|
|--|:--|
| [Alkalmazás neve][luis-get-started-create-app] | \* Maximális alapértelmezett karakter |
| Alkalmazások| 500 alkalmazás/Azure authoring Resource |
| [Kötegelt tesztelés][batch-testing]| 10-adatkészletek, 1000 utterances Adatkészletenként|
| Explicit listája | 50 alkalmazásonként|
| Külső entitások | Nincs korlát |
| [Leképezések][intents]|500/alkalmazás: 499 egyéni szándék, és a szükséges _none_ szándék.<br>A [küldő-alapú](https://aka.ms/dispatch-tool) alkalmazás megfelelő 500 elküldési forrással rendelkezik.|
| [Entitások listázása](./luis-concept-entity-types.md) | Szülő: 50, gyermek: 20 000 elem. Kanonikus név * alapértelmezett karakterek maximális száma. Szinonima hossza korlátozás rendelkeznie. |
| [Gépi megtanult entitások + szerepkörök](./luis-concept-entity-types.md):<br> összetett<br>egyszerű<br>entitás szerepköre|Legfeljebb 100 szülő entitás vagy 330 entitás, amely a felhasználó által elsőként megjelenő korlátot korlátozza. A szerepkör entitásként számít a határ szempontjából. Ilyen például egy egyszerű entitást tartalmazó kompozit, amely 2 szerepkörrel rendelkezik: 1 kompozit + 1 egyszerű + 2 szerepkör = 4 az 330 entitások közül.<br>Az alösszetevők akár 5 szintre is ágyazhatók.|
|Modell szolgáltatásként| Egy adott modellhez a leíróként (funkcióként) használható modellek maximális száma 10 modell. Egy adott modellhez tartozó leíróként (funkcióként) használt kifejezések maximális száma 10 kifejezés típusú listának.|
| [Előnézet – dinamikus lista entitásai](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 a ~ 1k/Query előrejelzési végpontra vonatkozó kérelem listája|
| [Minták](luis-concept-patterns.md)|500 minták alkalmazásonként.<br>Minta legfeljebb 400 karakter.<br>3 Pattern.any entitások mintában<br>A beágyazott 2 opcionális szövegek mintában maximális|
| [Minta. any](./luis-concept-entity-types.md)|100, alkalmazásonként mintában 3 pattern.any entitások |
| [Kifejezések listája][phrase-list]|500-kifejezések listája. A nem felcserélhető phraselist legfeljebb 5 000 kifejezéssel rendelkezik. A felcserélhető Phraselist legfeljebb 50 000 kifejezéssel rendelkezik. Az 500 000-mondatok alkalmazásával kapcsolatos teljes mondatok maximális száma.|
| [Előre elkészített entitások](./luis-prebuilt-entities.md) | korlátlan|
| [Reguláris kifejezések entitásai](./luis-concept-entity-types.md)|20 entitások<br>maximum 500 karakter. egy entitás mintával|
| [Szerepkörök](luis-concept-roles.md)|300 szerepkörök alkalmazásonként. 10 szerepkörök / entitás|
| [Utterance][utterances] | 500 karakternél|
| [Hosszúságú kimondott szöveg][utterances] | 15 000/alkalmazás – a hosszúságú kimondott szöveg száma nem korlátozható a szándék alapján|
| [Verziók](luis-concept-version.md)| 100 verzió/alkalmazás |
| [Verzió neve][luis-how-to-manage-versions] | 10 karakter hosszúságú alfanumerikus karaktereket, illetve időszak korlátozódik (.) |

\* Alapértelmezett karakter legfeljebb 50 karakter hosszú lehet.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Név egyedisége

Használja az alábbi elnevezési egyediségi szabályokat.

A következőnek egyedinek kell lennie a LUIS-alkalmazásban:

* Verzió neve
* szándék
* entitás
* roles

A következőnek egyedinek kell lennie az alkalmazott hatókörön belül:

* kifejezések listája

## <a name="object-naming"></a>Objektum elnevezése

Ne használja az alábbi karaktereket a következő nevekben.

|Objektum|Karakterek kizárása|
|--|--|
|Cél, entitás és szerepkör neve|`:`<br>`$` <br> `&`|
|Verzió neve|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Kulcshasználat

A nyelv értelmezése külön kulcsokkal rendelkezik, egy típust a szerzői műveletekhez, és egy típust az előrejelzési végpont lekérdezéséhez. Ha többet szeretne megtudni a főbb típusok közötti különbségekről, olvassa el az [előrejelzési végpont kulcsainak készítése a Luis-ben](luis-concept-keys.md)című témakört.

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>Erőforrás-kulcsok korlátai

Az erőforrás-kulcsok különböző korlátozásokkal rendelkeznek a szerzői és a végponthoz. A LUIS-előrejelzési lekérdezés végpontjának kulcsa csak végponti lekérdezések esetén érvényes.

* 500 alkalmazás/Azure authoring Resource

|Paraméter|Tartalomkészítés|Végpont|Cél|
|--|--|--|--|
|Kezdő|1 millió/hó, 5/s|1 ezer/hó, 5/s|A LUIS-alkalmazás készítése|
|F0 – ingyenes réteg |1 millió/hó, 5/s|10 ezer/hó, 5/s|A LUIS-végpontra lekérdezése|
|S0 – alapszintű csomag|-|50/mp|A LUIS-végpontra lekérdezése|
|S0 – standard szint|-|50/mp|A LUIS-végpontra lekérdezése|
|[Az érzelmek elemzésének integrációja](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|Ha további Azure-erőforrásra van szükség, az adatelemzési adatok, például a kulcs kifejezésének kibontása is elérhető. |
|[Beszédfelismerési integráció](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1000 végponti kérelmek egységenkénti áron|Használja a beszélt utterance (kifejezés) konvertálása szöveges utterance (kifejezés) és a LUIS eredményeket adja vissza|

[További információ a díjszabásról.][pricing]

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
