---
title: Nyelvi támogatás - LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS számos szolgáltatással rendelkezik a szolgáltatáson belül. Nem minden funkció azonos nyelvi paritáson van. Győződjön meg arról, hogy a kívánt funkciók támogatottak a megcélzott nyelvi kultúrában. A LUIS-alkalmazások kulturális környezet-specifikusak, és nem módosíthatók, ha be van állítva.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220852"
---
# <a name="language-and-region-support-for-luis"></a>A LUIS nyelvi és területi támogatása

A LUIS számos szolgáltatással rendelkezik a szolgáltatáson belül. Nem minden funkció azonos nyelvi paritáson van. Győződjön meg arról, hogy a kívánt funkciók támogatottak a megcélzott nyelvi kultúrában. A LUIS-alkalmazások kulturális környezet-specifikusak, és nem módosíthatók, ha be van állítva.

## <a name="multi-language-luis-apps"></a>Többnyelvű LUIS-alkalmazások

Ha szüksége van egy többnyelvű LUIS ügyfélalkalmazás, például egy chatbot, van néhány lehetőség. Ha a LUIS támogatja az összes nyelvet, minden nyelvhez kifejleszt egy LUIS-alkalmazást. Minden LUIS-alkalmazás rendelkezik egy egyedi alkalmazásazonosítóval és végpontnaplóval. Ha nyelvi megértést kell biztosítania egy olyan nyelvhez, amelyet a LUIS nem támogat, a [Microsoft Translator API](../Translator/translator-info-overview.md) használatával lefordíthatja az utterance (kifejezés) egy támogatott nyelvre, elküldheti az utterance (kifejezés) a LUIS-végpontnak, és megkaphatja az eredményül kapott pontszámokat.

## <a name="languages-supported"></a>Támogatott nyelvek

A LUIS a következő nyelveken ismeri meg a kimondott szövegeket:

| Nyelv |Területi beállítás  |  Előre összeállított tartomány | Előre összeállított entitás | Kifejezéslista-javaslatok | **[Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Hangulat és<br>Kulcsszavak)|
|--|--|:--:|:--:|:--:|:--:|
| Amerikai angol |`en-US` | ✔ | ✔  |✔|✔|
| Arab (előnézet - modern szabványos arab) |`ar-AR`|-|-|-|-|
| *[kínai](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holland |`nl-NL` |✔|  -   |-|✔|
| Francia (Franciaország) |`fr-FR` |✔| ✔ |✔ |✔|
| Francia (Kanada) |`fr-CA` |-|   -   |-|✔|
| Német |`de-DE` |✔| ✔ |✔ |✔|
| Hindi | `hi-IN`|-|-|-|-|
| Olasz |`it-IT` |✔| ✔ |✔|✔|
| *[japán](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Csak a kulcsmondat|
| Koreai |`ko-KR` |✔|   -   |-|Csak a kulcsmondat|
| Portugál (Brazília) |`pt-BR` |✔| ✔ |✔ |nem minden szubkultúra|
| Spanyol (Spanyolország) |`es-ES` |✔| ✔ |✔|✔|
| Spanyol (Mexikó)|`es-MX` |-|  -   |✔|✔|
| Török | `tr-TR` |✔|-|-|Csak hangulat|

A nyelvi támogatás az [előre összeállított entitások](luis-reference-prebuilt-entities.md) és [az előre összeállított tartományok](luis-reference-prebuilt-domains.md)esetében eltérő lehet.

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>*Japán támogatási megjegyzések

 - Mivel a LUIS nem biztosít szintaktikai elemzést, és nem fogja megérteni a különbséget a Keigo és az informális japán között, be kell építenie a különböző formalitási szinteket az alkalmazások képzési példáiként.
     - A nem ugyanaz, mint a 啦ン.
     - A nem ugyanaz, mint a.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>A beszédfelismerési API által támogatott nyelvek
Lásd: [Beszédfelismerési nyelvek](../speech-service/speech-to-text.md) a beszéddiktálási mód nyelveihez.

### <a name="bing-spell-check-supported-languages"></a>A Bing helyesírás-ellenőrzése támogatott nyelvek
A támogatott nyelvek és állapotok listáját a Bing [helyesírás-ellenőrzése a támogatott nyelvekről](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) című témakörben található.

## <a name="rare-or-foreign-words-in-an-application"></a>Ritka vagy idegen szavak egy alkalmazásban
A `en-us` kultúrában a LUIS megtanulja megkülönböztetni a legtöbb angol szót, beleértve a szleng. A `zh-cn` kultúrában a LUIS megtanulja megkülönböztetni a legtöbb kínai karaktert. Ha egy ritka szót `en-us` vagy `zh-cn`karaktert használ a alkalmazásban, és úgy látja, hogy a LUIS úgy tűnik, nem tudja megkülönböztetni az adott szót vagy karaktert, hozzáadhatja a szót vagy karaktert egy [kifejezéslista-szolgáltatáshoz.](luis-how-to-add-features.md) Például az alkalmazás kultúráján kívüli szavakat - azaz idegen szavakat - hozzá kell adni egy kifejezéslista funkcióhoz.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hibrid nyelvek
A hibrid nyelvek két kultúrából, például az angolból és a kínaiból származó szavakat kombinálják. Ezeket a nyelveket a LUIS nem támogatja, mert egy alkalmazás egyetlen kultúrán alapul.

## <a name="tokenization"></a>Tokenizálás
Gépi tanulás végrehajtásához a LUIS egy utterance (kifejezés) a kultúra alapú [jogkivonatokra bontja.](luis-glossary.md#token)

|Nyelv|  minden szóköz vagy különleges karakter | karakterszint|összetett szavak|[tokenizált entitás visszaadva](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Arab|||||
|Kínai||✔||✔|
|Holland|||✔|✔|
|Angol (en-us)|✔ ||||
|Francia (fr-FR)|✔||||
|Francia (fr-CA)|✔||||
|Német|||✔|✔|
| Hindi |✔|-|-|-|-|
|Olasz|✔||||
|Japán||||✔|
|Koreai||✔||✔|
|Portugál (Brazília)|✔||||
|Spanyol (es-ES)|✔||||
|Spanyol (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Egyéni tokenizer verziók

A következő kultúrák egyéni tokenizer verziókkal rendelkeznek:

|Kulturális környezet|Verzió|Cél|
|--|--|--|
|Német<br>`de-de`|1.0.0|Tokenizes szavak felosztásával őket egy gépi tanulás-alapú tokenizer, amely megpróbálja lebontani a kompozit szavakat az egyes összetevők.<br>Ha a felhasználó `Ich fahre einen krankenwagen` utterance (kifejezés) ként `Ich fahre einen kranken wagen`adja meg, a rendszer a rendszerre fordul. Lehetővé teszi `kranken` a `wagen` jelölést, és függetlenül a különböző szervezetek.|
|Német<br>`de-de`|1.0.2|A szavak tokenizes-e szóközökre osztva.<br> ha a felhasználó `Ich fahre einen krankenwagen` utterance (kifejezés) lép be, egyetlen jogkivonat marad. Így `krankenwagen` van megjelölve, mint egy entitás. |

### <a name="migrating-between-tokenizer-versions"></a>Tokenizer verziók közötti áttelepítés
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

Tokenizálás történik az alkalmazás szintjén. A verziószintű tokenizálás nem támogatott.

[Importálja a fájlt új alkalmazásként](luis-how-to-start-new-app.md)verzió helyett. Ez a művelet azt jelenti, hogy az új alkalmazás egy másik alkalmazásazonosítóval rendelkezik, de a fájlban megadott tokenizer verziót használja.
