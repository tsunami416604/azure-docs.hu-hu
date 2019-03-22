---
title: Nyelvi támogatás
titleSuffix: Azure Cognitive Services
description: A LUIS különféle funkciókat a szolgáltatáson belül van. Nem minden funkciója el az azonos nyelven elérhetőek. Ellenőrizze, hogy a nyelvi kulturális környezet céloz meg az Önt érdeklő funkciók támogatottak. LUIS-alkalmazásokon kulturális környezet-specifikus, és nem módosítható, hogy be van állítva.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/19/2019
ms.author: diberry
ms.openlocfilehash: 735835d16eb14c3847f36ecb6f46c08c0a8928ef
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339516"
---
# <a name="language-and-region-support-for-luis"></a>A LUIS nyelvéhez és régiójához támogatása

A LUIS különféle funkciókat a szolgáltatáson belül van. Nem minden funkciója el az azonos nyelven elérhetőek. Ellenőrizze, hogy a nyelvi kulturális környezet céloz meg az Önt érdeklő funkciók támogatottak. LUIS-alkalmazásokon kulturális környezet-specifikus, és nem módosítható, hogy be van állítva.

## <a name="multi-language-luis-apps"></a>A többnyelvű LUIS-alkalmazások

Ha például egy csevegőrobotot többnyelvű LUIS ügyfélalkalmazás van szüksége, több lehetősége van. Ha LUIS támogatja az összes nyelv, az egyes nyelvekhez a LUIS-alkalmazások fejlesztése. Minden egyes LUIS alkalmazás rendelkezik egy egyedi Alkalmazásazonosító, és a végpont napló. Ha meg kell adnia a nyelv értelmezése nem támogatja a LUIS, nyelvet használhatja [Microsoft Translator API](../Translator/translator-info-overview.md) lefordítani az utterance (kifejezés) egy támogatott nyelvre, küldje el az utterance (kifejezés), a LUIS-végponthoz, és megkapta az eredményül kapott értékeket.

## <a name="languages-supported"></a>Támogatott nyelvek

A LUIS tisztában van azzal a kimondott szöveg a következő nyelveken:

| Nyelv |Területi beállítás  |  Előre összeállított tartomány | Előre összeállított entitások | A kifejezés lista javaslatok | **[Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Vélemények és<br>A kulcsszavak)|
|--|--|:--:|:--:|:--:|:--:|
| Amerikai angol |`en-US` | ✔ | ✔  |✔|✔|
| *[kínai](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| holland |`nl-NL` |-|  -   |-|✔|
| francia (Franciaország) |`fr-FR` |-| ✔ |✔ |✔|
| francia (Kanada) |`fr-CA` |-|   -   |-|✔|
| német |`de-DE` |-| ✔ |✔ |✔|
| olasz |`it-IT` |-| ✔ |✔|✔|
| *[japán](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Csak a kulcsfontosságú kifejezések|
| koreai |`ko-KR` |-|   -   |-|Csak a kulcsfontosságú kifejezések|
| portugál (brazíliai) |`pt-BR` |-| ✔ |✔ |nem minden alárendelt kulturális környezetek|
| spanyol (spanyolországi) |`es-ES` |-| ✔ |✔|✔|
| spanyol (Mexikó)|`es-MX` |-|  -   |✔|✔|
| török | `tr-TR` |-|-|-|Csak a vélemények|


Nyelvi támogatás esetében eltérő [előre összeállított entitások](luis-reference-prebuilt-entities.md) és [előre összeállított tartományok](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>* Kínai támogatja a jegyzeteket

 - Az a `zh-cn` kulturális környezet, LUIS vár helyett a hagyományos karakterkészlet egyszerűsített kínai karakterkészlet.
 - Leképezések, az entitások, a funkciók és a reguláris kifejezések nevei a kínai vagy latin karaktereket lehet.
 - Tekintse meg a [előre összeállított tartományok referencia](luis-reference-prebuilt-domains.md) , amelyen előre összeállított tartományok használata támogatott, az információ a `zh-cn` kulturális környezet.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>* Japán támogatja a jegyzeteket

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
Az a `en-us` kulturális környezet, LUIS megtanulja különbséget tenni a legtöbb angol szavak, beleértve a szleng felismerését. Az a `zh-cn` kulturális környezet, LUIS megtanulja különbséget tenni a legtöbb kínai karaktereket. Egy ritka kifejezése használatakor `en-us` karakter vagy `zh-cn`, és láthatja, hogy a LUIS úgy tűnik, nem tesz különbséget az adott szó vagy karakter lehet, adja hozzá az adott szó vagy a karakter egy [kifejezéslista funkció](luis-how-to-add-features.md). Például szavak kívül az alkalmazás –, hogy külső szavak--kulturális környezete kell adni egy kifejezéslista szolgáltatás. Ez a kifejezés a lista nem cserélhető, annak jelzésére, hogy a ritka szavak készletét képezi egy osztályt, amely a LUIS kell további ismeri fel, de azok nem szinonimák vagy cserélhető, egymással kell megjelölni.

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
|olasz|✔||||
|japán||||✔|
|koreai||✔||✔|
|portugál (brazíliai)|✔||||
|Spanyol (es-ES)|✔||||
|Spanyol (es – MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Egyéni jogkivonatokat létrehozó verziók

A következő kulturális környezetek egyéni tokenizer verziójával rendelkezik:

|Kulturális környezet|Verzió|Cél|
|--|--|--|
|német<br>`de-de`|1.0.0|Szavak tokenizes halmazra őket egy machine learning-alapú jogkivonatokat létrehozó, amely próbál meg bontja a egyetlen összetevőből összetett szavak használatával.<br>Ha a felhasználó `Ich fahre einen krankenwagen` az utterance (kifejezés), mint szolgáltatás engedélyezve van `Ich fahre einen kranken wagen`. A jelölés, így `kranken` és `wagen` különböző entitásokként egymástól függetlenül.|
|német<br>`de-de`|1.0.1|A tárolóhelyek halmazra tokenizes a szavakat.<br> Ha a felhasználó `Ich fahre einen krankenwagen` utterance (kifejezés) egy, az egyes marad. Így `krankenwagen` egyetlen entitás van megjelölve. |

### <a name="migrating-between-tokenizer-versions"></a>Jogkivonatokat létrehozó verziók közötti migrálás

Az első lehetősége, hogy módosítsa a jogkivonatokat létrehozó verzióját az alkalmazás fájljában, majd importálja a verziót. Ez a művelet módosítja, hogy megcímkézzen tokenekre vannak, de lehetővé teszi, hogy az ugyanazon alkalmazás azonosítóját. 

Tokenizer JSON 1.0.0. Figyelje meg, hogy a tulajdonság értéke `tokenizerVersion`. 

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

JSON tokenizer 1.0.1-es verziója. Figyelje meg, hogy a tulajdonság értéke `tokenizerVersion`. 

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

A második lehetősége, hogy [importálja a fájlt új alkalmazásként](luis-how-to-start-new-app.md#import-an-app-from-file), egy verzió helyett. Ez a művelet azt jelenti, az új alkalmazás egy másik alkalmazás Azonosítóval rendelkezik, de a fájlban megadott jogkivonatokat létrehozó verzióját használja. 