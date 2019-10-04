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
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: bd1e665114fff4d5b7b0b2dca267207bdeebab56
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949551"
---
# <a name="language-and-region-support-for-luis"></a>A LUIS nyelvéhez és régiójához támogatása

A LUIS különféle funkciókat a szolgáltatáson belül van. Nem minden funkciója el az azonos nyelven elérhetőek. Ellenőrizze, hogy a nyelvi kulturális környezet céloz meg az Önt érdeklő funkciók támogatottak. LUIS-alkalmazásokon kulturális környezet-specifikus, és nem módosítható, hogy be van állítva.

## <a name="multi-language-luis-apps"></a>A többnyelvű LUIS-alkalmazások

Ha például egy csevegőrobotot többnyelvű LUIS ügyfélalkalmazás van szüksége, több lehetősége van. Ha LUIS támogatja az összes nyelv, az egyes nyelvekhez a LUIS-alkalmazások fejlesztése. Minden egyes LUIS alkalmazás rendelkezik egy egyedi Alkalmazásazonosító, és a végpont napló. Ha meg kell adnia a nyelv értelmezése nem támogatja a LUIS, nyelvet használhatja [Microsoft Translator API](../Translator/translator-info-overview.md) lefordítani az utterance (kifejezés) egy támogatott nyelvre, küldje el az utterance (kifejezés), a LUIS-végponthoz, és megkapta az eredményül kapott értékeket.

## <a name="languages-supported"></a>Támogatott nyelvek

A LUIS tisztában van azzal a kimondott szöveg a következő nyelveken:

| Nyelv |Területi beállítás  |  Előre összeállított tartomány | Előre összeállított entitások | Kifejezések listája – javaslatok | **[Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Vélemények és<br>A kulcsszavak)|
|--|--|:--:|:--:|:--:|:--:|
| Amerikai angol |`en-US` | ✔ | ✔  |✔|✔|
| *[kínai](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| holland |`nl-NL` |✔|  -   |-|✔|
| francia (Franciaország) |`fr-FR` |✔| ✔ |✔ |✔|
| francia (Kanada) |`fr-CA` |-|   -   |-|✔|
| német |`de-DE` |✔| ✔ |✔ |✔|
| hindi | `hi-IN`|-|-|-|-|
| olasz |`it-IT` |✔| ✔ |✔|✔|
| *[japán](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Csak a kulcsfontosságú kifejezések|
| koreai |`ko-KR` |✔|   -   |-|Csak a kulcsfontosságú kifejezések|
| portugál (brazíliai) |`pt-BR` |✔| ✔ |✔ |nem minden alárendelt kulturális környezetek|
| spanyol (spanyolországi) |`es-ES` |✔| ✔ |✔|✔|
| spanyol (Mexikó)|`es-MX` |-|  -   |✔|✔|
| török | `tr-TR` |✔|-|-|Csak hangulat|


Nyelvi támogatás esetében eltérő [előre összeállított entitások](luis-reference-prebuilt-entities.md) és [előre összeállított tartományok](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>\* Kínai támogatja a jegyzeteket

 - Az a `zh-cn` kulturális környezet, LUIS vár helyett a hagyományos karakterkészlet egyszerűsített kínai karakterkészlet.
 - Leképezések, az entitások, a funkciók és a reguláris kifejezések nevei a kínai vagy latin karaktereket lehet.
 - A `zh-cn` kulturális környezet által támogatott előre összeépített tartományokkal kapcsolatos információkért tekintse meg az előre [elkészített tartományok referenciáját](luis-reference-prebuilt-domains.md) .
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>\* Japán támogatja a jegyzeteket

 - Mivel a LUIS nem biztosít szintaktikai analysis, és nem lesz a következő Keigo és az informális Japán közötti különbségek megértése, kell foglalják magukban képzési példaként használt alkalmazásait formalitást különböző szintjeit.
     - でございます nem ugyanaz, mint です.
     - です nem ugyanaz, mint だ.

### <a name="text-analytics-support-notes"></a>** A text analytics támogatja a jegyzeteket
Szövegelemzés tartalmaz keyPhrase előre létrehozott entitás- és vélemények elemzése. Csak portugál tápfolyadékok cseréjét esetében támogatott: `pt-PT` és `pt-BR`. Minden más kultúrák támogatottak az elsődleges kulturális környezet szintjén. További információ a Text Analytics [támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages).

### <a name="speech-api-supported-languages"></a>Beszédfelismerési API támogatott nyelvek
Tekintse meg a beszédfelismerés [támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) Speech Diktálás üzemmód nyelveken.

### <a name="bing-spell-check-supported-languages"></a>A Bing Spell Check, támogatott nyelvek
Tekintse meg a Bing Spell Check [támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) támogatott nyelvek és állapot listáját.

## <a name="rare-or-foreign-words-in-an-application"></a>Ritka vagy külső szavak egy alkalmazásban
Az a `en-us` kulturális környezet, LUIS megtanulja különbséget tenni a legtöbb angol szavak, beleértve a szleng felismerését. Az a `zh-cn` kulturális környezet, LUIS megtanulja különbséget tenni a legtöbb kínai karaktereket. Egy ritka kifejezése használatakor `en-us` karakter vagy `zh-cn`, és láthatja, hogy a LUIS úgy tűnik, nem tesz különbséget az adott szó vagy karakter lehet, adja hozzá az adott szó vagy a karakter egy [kifejezéslista funkció](luis-how-to-add-features.md). Például szavak kívül az alkalmazás –, hogy külső szavak--kulturális környezete kell adni egy kifejezéslista szolgáltatás. 

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hibrid nyelvek
Hibrid nyelvek szó azoktól, például az angol és kínai két kulturális környezetek egyesítése. Ezeken a nyelveken nem támogatottak a LUIS, mivel az alkalmazás egyetlen kulturális környezetet alapul.

## <a name="tokenization"></a>A jogkivonatok
Machine learning alkalmazásához, LUIS bontja az utterance (kifejezés) [jogkivonatok](luis-glossary.md#token) kulturális környezet alapján.

|Nyelv|  minden szóközt vagy különleges karaktert | karakter szint|összetett szavakat|[tokenekre entitást adott vissza](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|kínai||✔||✔|
|holland|||✔|✔|
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

|Kulturális környezet|Version|Cél|
|--|--|--|
|német<br>`de-de`|1.0.0|A szavakat Tokenizes egy gépi tanuláson alapuló tokenizer, amely az összetett szavakat egyetlen összetevőjére próbálja bontani.<br>Ha a felhasználó @no__t – 0 értéket ad meg, a rendszer `Ich fahre einen kranken wagen` értékre vált. A `kranken` és a `wagen` jelölésének engedélyezése a különböző entitások egymástól függetlenül.|
|német<br>`de-de`|1.0.2|Tokenizes a szavakat a szóközök felosztásával.<br> Ha a felhasználó a `Ich fahre einen krankenwagen` értéket adja meg, akkor egyetlen token marad. Így a `krankenwagen` egyetlen entitásként van megjelölve. |

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
