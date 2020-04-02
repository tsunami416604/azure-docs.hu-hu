---
title: Korlátok - LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk az Azure Cognitive Services nyelvi ismert (LUIS) ismert korlátait tartalmazza. A LUIS több határterületnek is rendelkezik. A modellhatár szabályozza a leképezéseket, entitásokat és szolgáltatásokat a LUIS-ban. Kvótakorlátok a kulcstípus alapján. A billentyűzetkombináció vezérli a LUIS webhelyet.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6c021e68f8b76d8b0d3e6e9ff21c242580f53313
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520946"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>A LUIS-modell és -kulcsok határai
A LUIS több határterületnek is rendelkezik. Az első a [modell határa](#model-boundaries), amely szabályozza a leképezések, entitások és szolgáltatások a LUIS. A második terület a kulcstípuson alapuló [kvótakorlátok.](#key-limits) A harmadik terület a határok a [billentyűzet kombinációja](#keyboard-controls) a LUIS-webhely vezérléséhez. A negyedik terület a [világ régió leképezése](luis-reference-regions.md) a LUIS authoring webhely és a LUIS [végpont](luis-glossary.md#endpoint) API-k között.


## <a name="model-boundaries"></a>Modellhatárok

Ha az alkalmazás túllépi a LUIS-modell korlátait és határait, fontolja meg egy [LUIS-küldő](luis-concept-enterprise.md#dispatch-tool-and-model) alkalmazás vagy egy [LUIS-tároló](luis-container-howto.md)használatát.

|Terület|Korlát|
|--|:--|
| [Alkalmazás neve][luis-get-started-create-app] | *Alapértelmezett karakter max |
| Alkalmazások| 500 alkalmazás Azure-szerzői erőforrásonként |
| [Kötegelt tesztelés][batch-testing]| 10 adatkészlet, 1000 kimondott szöveg adatkészletenként|
| Explicit lista | 50 kérelemenként|
| Külső entitások | nincs enek korlátozás |
| [Leképezések][intents]|500 alkalmazásonként: 499 egyéni leképezések, és a szükséges _Nincs_ szándék.<br>[A diszpécser alapú](https://aka.ms/dispatch-tool) alkalmazásnak 500 diszpécserforrása van.|
| [Listaentitások](./luis-concept-entity-types.md) | Szülő: 50, gyermek: 20.000 elem. A kanonikus név *alapértelmezett karakter max. A szinonimaértékek nem rendelkeznek hosszkorlátozással. |
| [Gépmegtanult entitások + szerepkörök:](./luis-concept-entity-types.md)<br> Kompozit<br>Egyszerű<br>entitás szerepköre|Legfeljebb 100 fölérendelt entitás, vagy 330 entitás, attól függően, hogy melyik korlát korlátozza a felhasználó találatai először. A szerepkör entitásnak számít ennek a határnak a alkalmazásában. Egy példa egy összetett egy egyszerű entitás, amely 2 szerepkörök: 1 összetett + 1 egyszerű + 2 szerepkör = 4 a 330 entitások.<br>Az alösszetevők legfeljebb 5 szintbe ágyazhatók be.|
|Modell mint funkció| Egy adott modellhez leíróként (funkcióként) használható modellek maximális száma 10 modell. Egy adott modell leírójaként (jellemzőjeként) használt kifejezéslisták maximális száma 10 kifejezéslistának kell lennie.|
| [Előzetes verzió – Dinamikus listaentitások](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 lista ~1k lekérdezés-előrejelzési végpontkérésenként|
| [Minták](luis-concept-patterns.md)|500 minta alkalmazásonként.<br>A minta maximális hossza 400 karakter.<br>3 Pattern.any entitások mintánként<br>Legfeljebb 2 egymásba ágyazott, választható szöveg a mintában|
| [Minta.minden](./luis-concept-entity-types.md)|100 alkalmazásonként, 3 pattern.any entitás mintánként |
| [Kifejezéslista][phrase-list]|500 kifejezéslista. 10 globális kifejezéslista a modell funkciókorlátként való listában. A nem felcserélhető kifejezéslista legfeljebb 5000 kifejezést tartalmazza. A cserélhető kifejezéslista legfeljebb 50 000 kifejezést tartalmazza. Az összes kifejezés maximális száma 500 000 kifejezésenként.|
| [Előre összeállított entitások](./luis-prebuilt-entities.md) | nincs korlátozás|
| [Reguláriskifejezés-entitások](./luis-concept-entity-types.md)|20 jogalany<br>500 karakter max. reguláris kifejezés entitásmintánként|
| [Szerepkörök](luis-concept-roles.md)|300 szerepkör alkalmazásonként. 10 szerepkör entitásonként|
| [Kimondott szöveg][utterances] | 500 karakter|
| [Beszédmódok][utterances] | 15 000 alkalmazásonként – nincs korlátozva a szándékonkénti kimondott szövegek száma|
| [Verziók](luis-concept-version.md)| 100 verzió alkalmazásonként |
| [Verziónév][luis-how-to-manage-versions] | Alfanumerikus és pont (.) karakterre korlátozott 10 karakter |

*Az alapértelmezett karakter max.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Név egyedisége

Használja a következő elnevezési egyediségi szabályokat.

A LUIS-alkalmazásokban a következőknek egyedinek kell lenniük:

* verzió neve
* Szándék
* Entitás
* roles

Az alkalmazott hatókörön belül a következőknek egyedinek kell lenniük:

* kifejezéslista

## <a name="object-naming"></a>Objektum elnevezése

Ne használja a következő karaktereket a következő nevekben.

|Objektum|Karakterek kizárása|
|--|--|
|Szándék, entitás és szerepkörnevek|`:`<br>`$` <br> `&`|
|Verziónév|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Kulcs használat

A Language Understand külön kulcsokkal, egy szerzői típussal és egy típussal rendelkezik az előrejelzési végpont lekérdezéséhez. A kulcstípusok közötti különbségekről a [LUIS szerzői és lekérdezés-előrejelzési végpontkulcsai](luis-concept-keys.md)című témakörben olvashat bővebben.

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>Erőforráskulcs-korlátok

Az erőforráskulcsok különböző korlátozásokat rendelkeznek a szerzői és végponti. A LUIS előrejelzési lekérdezési végpontkulcs csak végpontlekérdezésekre érvényes.

* 500 alkalmazás Azure-szerzői erőforrásonként

|Kulcs|Tartalomkészítés|Végpont|Cél|
|--|--|--|--|
|Kezdő|1 millió/hó, 5/másodperc|1 ezer/hó, 5/másodperc|A LUIS-alkalmazás készítése|
|F0 - Ingyenes szint |1 millió/hó, 5/másodperc|10 ezer/hó, 5/másodperc|A LUIS-végpont lekérdezése|
|S0 - Alapszintű szint|-|50/másodperc|A LUIS-végpont lekérdezése|
|S0 - Standard szint|-|50/másodperc|A LUIS-végpont lekérdezése|
|[Hangulatelemzés integrációja](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|A hangulati információk hozzáadása, beleértve a kulcskifejezés-adatok kinyerését is, egy másik Azure-erőforrás megkövetelése nélkül érhető el. |
|[Beszédintegráció](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1 000 végpontkérelem egységköltségenként|A szóbeli utterance (kifejezés) konvertálása szöveges utterance (kifejezés) szövegkimondott szöveggé, és luis-eredményeket ad vissza|

[További információ az árképzésről.][pricing]

## <a name="keyboard-controls"></a>Billentyűzetvezérlők

|Billentyűzet bevitele | Leírás |
|--|--|
|Control+E|váltás a jogkivonatok és az entitások között az utterances list|

## <a name="website-sign-in-time-period"></a>Webhely bejelentkezési időszaka

A bejelentkezési hozzáférés **60 percig**tart. Ezen időszak után megkapja ezt a hibát. Újra be kell jelentkeznie.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
