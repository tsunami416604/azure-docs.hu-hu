---
title: Nyelvi támogatás – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS számos funkcióval rendelkezik a szolgáltatáson belül. Nem minden szolgáltatás azonos nyelvi paritású. Győződjön meg arról, hogy az Ön által érintett funkciók támogatottak-e a megcélzott nyelvi kultúrában. A LUIS-alkalmazás Culture-specifikus, és a beállítása után nem módosítható.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: diberry
ms.openlocfilehash: 383cade6e8bb5f66aa240564912056ec345a1ee6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500190"
---
# <a name="language-and-region-support-for-luis"></a>A LUIS nyelv és régió támogatása

A LUIS számos funkcióval rendelkezik a szolgáltatáson belül. Nem minden szolgáltatás azonos nyelvi paritású. Győződjön meg arról, hogy az Ön által érintett funkciók támogatottak-e a megcélzott nyelvi kultúrában. A LUIS-alkalmazás Culture-specifikus, és a beállítása után nem módosítható.

## <a name="multi-language-luis-apps"></a>Többnyelvű LUIS-alkalmazások

Ha több nyelvű LUIS-ügyfélalkalmazás, például Csevegőrobot van szüksége, néhány lehetőség közül választhat. Ha a LUIS támogatja az összes nyelvet, minden nyelvhez fejleszt egy LUIS-alkalmazást. Minden LUIS-alkalmazás egyedi alkalmazás-AZONOSÍTÓval és végponti naplóval rendelkezik. Ha nem támogatja a nyelvi megértést a LUIS nyelvhez, a [Microsoft Translator API](../Translator/translator-info-overview.md) -val lefordíthatja a teljes változatot egy támogatott nyelvre, beküldheti a kitekintést a Luis-végpontba, és megkaphatja az eredményül kapott pontszámokat.

## <a name="languages-supported"></a>Támogatott nyelvek

LUIS a következő nyelveken ismeri fel a hosszúságú kimondott szöveg:

| Nyelv |Területi beállítás  |  Előre elkészített tartomány | Előre elkészített entitás | Kifejezések listája – javaslatok | **[text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Hangulat és<br>Kulcsszavak|
|--|--|:--:|:--:|:--:|:--:|
| Amerikai angol |`en-US` | ✔ | ✔  |✔|✔|
| *[kínai](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| holland |`nl-NL` |✔|  -   |-|✔|
| Francia (franciaországi) |`fr-FR` |✔| ✔ |✔ |✔|
| Francia (Kanada) |`fr-CA` |-|   -   |-|✔|
| német |`de-DE` |✔| ✔ |✔ |✔|
| hindi | `hi-IN`|-|-|-|-|
| olasz |`it-IT` |✔| ✔ |✔|✔|
| [japán](#japanese-support-notes) * |`ja-JP` |✔| ✔ |✔|Csak a kulcs kifejezése|
| koreai |`ko-KR` |✔|   -   |-|Csak a kulcs kifejezése|
| Portugál (brazíliai) |`pt-BR` |✔| ✔ |✔ |nem minden alkultúra|
| Spanyol (Spanyolország) |`es-ES` |✔| ✔ |✔|✔|
| Spanyol (Mexikó)|`es-MX` |-|  -   |✔|✔|
| török | `tr-TR` |✔|-|-|Csak hangulat|

A nyelvi támogatás az [előre elkészített entitások](luis-reference-prebuilt-entities.md) és az [előre összeépített tartományok](luis-reference-prebuilt-domains.md)esetében változhat.

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>\* Japán támogatási megjegyzések

 - Mivel a LUIS nem ad meg szintaktikai elemzést, és nem fogja megérteni a Keigo és az informális Japán közötti különbséget, be kell építenie a különböző formaságokat, mint például az alkalmazásaihoz való betanítási szintet.
     - a でございます nem ugyanaz, mint a です.
     - a です nem ugyanaz, mint a だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Beszédfelismerési API által támogatott nyelvek
Tekintse meg a beszédfelismerés [által támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) beszédfelismerési mód nyelveit.

### <a name="bing-spell-check-supported-languages"></a>Bing Spell Check támogatott nyelvek
A támogatott nyelvek és állapotok listáját a Bing Spell Check [támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) című részben tekintheti meg.

## <a name="rare-or-foreign-words-in-an-application"></a>Ritka vagy idegen szavak egy alkalmazásban
A `en-us` kultúrában LUIS megtanulja a legtöbb angol szót megkülönböztetni, beleértve a szlenget is. A `zh-cn` kultúrában LUIS megtanulja a legtöbb kínai karakter megkülönböztetését. Ha `en-us` vagy karaktert használó ritka szót használ `zh-cn`ban, és láthatja, hogy a LUIS úgy tűnik, hogy nem tudja megkülönböztetni ezt a szót vagy karaktert, ezt a szót vagy karaktert felveheti egy [kifejezés-lista szolgáltatásba](luis-how-to-add-features.md). Például az alkalmazás kultúráján kívüli szavak – azaz idegen szavak – egy kifejezés-lista szolgáltatásba kerülnek. 

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hibrid nyelvek
A hibrid nyelvek két kulturális környezetből (például angol és kínai) származó szavakat egyesítenek. A LUIS nem támogatja ezeket a nyelveket, mivel az alkalmazások egyetlen kultúrán alapulnak.

## <a name="tokenization"></a>Jogkivonatok létrehozása
A gépi tanulás elvégzéséhez LUIS a kulturális környezet alapján megszakítja a [jogkivonatokat](luis-glossary.md#token) .

|Nyelv|  minden szóköz vagy speciális karakter | karakter szintje|összetett szavak|[visszaadott jogkivonat-entitás](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|kínai||✔||✔|
|holland|||✔|✔|
|Angol (en-us)|✔ ||||
|Francia (FR-FR)|✔||||
|Francia (FR-CA)|✔||||
|német|||✔|✔|
| hindi |✔|-|-|-|-|
|olasz|✔||||
|japán||||✔|
|koreai||✔||✔|
|Portugál (brazíliai)|✔||||
|Spanyol (es-ES)|✔||||
|Spanyol (es-MX)|✔||||

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

[Importálja a fájlt új alkalmazásként](luis-how-to-start-new-app.md#import-an-app-from-file)a verzió helyett. Ez a művelet azt jelenti, hogy az új alkalmazáshoz egy másik alkalmazás-azonosító tartozik, de a fájlban megadott tokenizer-verziót használja. 
