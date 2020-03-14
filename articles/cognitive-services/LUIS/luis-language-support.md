---
title: Nyelvi támogatás – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS különféle funkciókat a szolgáltatáson belül van. Nem minden funkciója el az azonos nyelven elérhetőek. Ellenőrizze, hogy a nyelvi kulturális környezet céloz meg az Önt érdeklő funkciók támogatottak. LUIS-alkalmazásokon kulturális környezet-specifikus, és nem módosítható, hogy be van állítva.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 4b6d954d06f09bef5240bddc4860ddbc83513d69
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220852"
---
# <a name="language-and-region-support-for-luis"></a>A LUIS nyelvéhez és régiójához támogatása

A LUIS különféle funkciókat a szolgáltatáson belül van. Nem minden funkciója el az azonos nyelven elérhetőek. Ellenőrizze, hogy a nyelvi kulturális környezet céloz meg az Önt érdeklő funkciók támogatottak. LUIS-alkalmazásokon kulturális környezet-specifikus, és nem módosítható, hogy be van állítva.

## <a name="multi-language-luis-apps"></a>A többnyelvű LUIS-alkalmazások

Ha például egy csevegőrobotot többnyelvű LUIS ügyfélalkalmazás van szüksége, több lehetősége van. Ha LUIS támogatja az összes nyelv, az egyes nyelvekhez a LUIS-alkalmazások fejlesztése. Minden egyes LUIS alkalmazás rendelkezik egy egyedi Alkalmazásazonosító, és a végpont napló. Ha nem támogatja a nyelvi megértést a LUIS nyelvhez, a [Microsoft Translator API](../Translator/translator-info-overview.md) -val lefordíthatja a teljes változatot egy támogatott nyelvre, beküldheti a kitekintést a Luis-végpontba, és megkaphatja az eredményül kapott pontszámokat.

## <a name="languages-supported"></a>Támogatott nyelvek

A LUIS tisztában van azzal a kimondott szöveg a következő nyelveken:

| Nyelv |Területi beállítás  |  Előre összeállított tartomány | Előre összeállított entitások | Kifejezések listája – javaslatok | **[text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Vélemények és<br>A kulcsszavak)|
|--|--|:--:|:--:|:--:|:--:|
| Amerikai angol |`en-US` | ✔ | ✔  |✔|✔|
| Arab (előzetes verzió – modern standard arab) |`ar-AR`|-|-|-|-|
| *[kínai](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holland |`nl-NL` |✔|  -   |-|✔|
| francia (Franciaország) |`fr-FR` |✔| ✔ |✔ |✔|
| francia (Kanada) |`fr-CA` |-|   -   |-|✔|
| német |`de-DE` |✔| ✔ |✔ |✔|
| hindi | `hi-IN`|-|-|-|-|
| olasz |`it-IT` |✔| ✔ |✔|✔|
| [japán](#japanese-support-notes) * |`ja-JP` |✔| ✔ |✔|Csak a kulcsfontosságú kifejezések|
| koreai |`ko-KR` |✔|   -   |-|Csak a kulcsfontosságú kifejezések|
| portugál (brazíliai) |`pt-BR` |✔| ✔ |✔ |nem minden alárendelt kulturális környezetek|
| spanyol (spanyolországi) |`es-ES` |✔| ✔ |✔|✔|
| spanyol (Mexikó)|`es-MX` |-|  -   |✔|✔|
| török | `tr-TR` |✔|-|-|Csak hangulat|

A nyelvi támogatás az [előre elkészített entitások](luis-reference-prebuilt-entities.md) és az [előre összeépített tartományok](luis-reference-prebuilt-domains.md)esetében változhat.

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>\* Japán támogatja a jegyzeteket

 - Mivel a LUIS nem biztosít szintaktikai analysis, és nem lesz a következő Keigo és az informális Japán közötti különbségek megértése, kell foglalják magukban képzési példaként használt alkalmazásait formalitást különböző szintjeit.
     - でございます nem ugyanaz, mint です.
     - です nem ugyanaz, mint だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Beszédfelismerési API támogatott nyelvek
Tekintse meg a beszédfelismerés [által támogatott nyelvek](../speech-service/speech-to-text.md) beszédfelismerési mód nyelveit.

### <a name="bing-spell-check-supported-languages"></a>A Bing Spell Check, támogatott nyelvek
A támogatott nyelvek és állapotok listáját a Bing Spell Check [támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) című részben tekintheti meg.

## <a name="rare-or-foreign-words-in-an-application"></a>Ritka vagy külső szavak egy alkalmazásban
A `en-us` kultúrában LUIS megtanulja a legtöbb angol szót megkülönböztetni, beleértve a szlenget is. A `zh-cn` kultúrában LUIS megtanulja a legtöbb kínai karakter megkülönböztetését. Ha `en-us` vagy karaktert használó ritka szót használ `zh-cn`ban, és láthatja, hogy a LUIS úgy tűnik, hogy nem tudja megkülönböztetni ezt a szót vagy karaktert, ezt a szót vagy karaktert felveheti egy [kifejezés-lista szolgáltatásba](luis-how-to-add-features.md). Például szavak kívül az alkalmazás –, hogy külső szavak--kulturális környezete kell adni egy kifejezéslista szolgáltatás.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hibrid nyelvek
Hibrid nyelvek szó azoktól, például az angol és kínai két kulturális környezetek egyesítése. Ezeken a nyelveken nem támogatottak a LUIS, mivel az alkalmazás egyetlen kulturális környezetet alapul.

## <a name="tokenization"></a>A jogkivonatok
A gépi tanulás elvégzéséhez LUIS a kulturális környezet alapján megszakítja a [jogkivonatokat](luis-glossary.md#token) .

|Nyelv|  minden szóközt vagy különleges karaktert | karakter szint|összetett szavakat|[visszaadott jogkivonat-entitás](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Arab|||||
|Kínai||✔||✔|
|Holland|||✔|✔|
|Angol (en-us)|✔ ||||
|Francia (fr-FR)|✔||||
|Francia (fr, CA)|✔||||
|német|||✔|✔|
| hindi |✔|-|-|-|-|
|olasz|✔||||
|japán||||✔|
|koreai||✔||✔|
|portugál (brazíliai)|✔||||
|Spanyol (es-ES)|✔||||
|Spanyol (es – MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Egyéni tokenizer-verziók

A következő kultúrákban egyéni tokenizer verziók szerepelnek:

|Kulturális környezet|Verzió|Cél|
|--|--|--|
|német<br>`de-de`|1.0.0|A szavakat Tokenizes egy gépi tanuláson alapuló tokenizer, amely az összetett szavakat egyetlen összetevőjére próbálja bontani.<br>Ha a felhasználó a `Ich fahre einen krankenwagen`t adja meg, a rendszer `Ich fahre einen kranken wagen`ra vált. `kranken` és `wagen` egymástól függetlenül történő megjelölésének engedélyezése a különböző entitások szerint.|
|német<br>`de-de`|1.0.2|Tokenizes a szavakat a szóközök felosztásával.<br> Ha a felhasználó bekerül `Ich fahre einen krankenwagen`ként, akkor egyetlen token marad. Így `krankenwagen` egyetlen entitásként van megjelölve. |

### <a name="migrating-between-tokenizer-versions"></a>Áttelepítés tokenizer-verziók között
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID.

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

A jogkivonatok létrehozása az alkalmazás szintjén történik. A verzió szintű jogkivonatok létrehozása nem támogatott.

[Importálja a fájlt új alkalmazásként](luis-how-to-start-new-app.md)a verzió helyett. Ez a művelet azt jelenti, hogy az új alkalmazáshoz egy másik alkalmazás-azonosító tartozik, de a fájlban megadott tokenizer-verziót használja.
