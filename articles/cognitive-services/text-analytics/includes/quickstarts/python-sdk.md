---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: 0803a847e9e864b361917df9f1a9c6b059ca2fe9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203413"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

[3-as verzió referenciadokumentációja](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b2/azure.ai.textanalytics.html) | [3-as verzió kódtárforráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [3-as verzió csomagja (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [3-as verzió mintái](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

[2-es verzió referenciadokumentációja](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [2-es verzió kódtárforráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [2-es verzió csomagja (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [2-es verzió mintái](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Ingyenes létrehozás](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Hozzon létre egy Text Analytics-erőforrást,"  target="_blank">hozzon létre egy Text Analytics-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span></a> az Azure Portalon a kulcs és a végpont beszerzéséhez. 
    * Ahhoz, hogy az alkalmazást a Text Analytics API-hoz csatlakoztathassa, szüksége lesz egy kulcsra és egy végpontra a létrehozott erőforrásból. Ezt a rövid útmutató egy későbbi részében teheti meg.
    * A szolgáltatás kipróbálásához használhatja az ingyenes tarifacsomagot, amelyet frissíthet fizetős szintre az éles használathoz.

## <a name="setting-up"></a>Beállítás

### <a name="install-the-client-library"></a>Telepítse az ügyfélkódtárat

A Python telepítése után az ügyfélkódtárat a következővel telepítheti:

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

> [!TIP]
> Szeretné egyben megtekinteni a teljes gyorsútmutatós kódfájlt? Megtalálhatja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), amely a gyorsútmutató kódmintáit is tartalmazza. 

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Szeretné egyben megtekinteni a teljes gyorsútmutatós kódfájlt? Megtalálhatja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py), amely a gyorsútmutató kódmintáit is tartalmazza. 

---

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-fájlt, és hozzon létre változókat az erőforrás Azure-végpontjához és előfizetési kulcsához.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektummodell

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

A Text Analytics ügyfél egy `TextAnalyticsClient` objektum, amely a kulcs használatával hitelesíti magát az Azure-ban. Az ügyfél számos módszert biztosít a kötegelt szövegelemzéshez. 

Kötegelt feldolgozás során a rendszer az API-nak `documents` listaként küldi el a szöveget, amely a használt módszertől függően `id`, `text` és `language` attribútumok kombinációját tartalmazó `dictionary` objektumokból áll. A `text` attribútum az elemzendő szöveget tárolja az eredeti `language` nyelven, az `id` bármilyen érték lehet. 

A válaszobjektum az egyes dokumentumok elemzési információit tartalmazó lista. 

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

A Text Analytics-ügyfél egy [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) objektum, amely a kulcs használatával hitelesíti magát az Azure-ban. Az ügyfél számos módszert biztosít a szövegelemzéshez, egyetlen sztringként vagy kötegelve. 

A rendszer az API-nak `documents` listájaként küldi el a szöveget, amely a használt módszertől függően `id`, `text` és `language` attribútumok kombinációját tartalmazó `dictionary` objektumokból áll. A `text` attribútum az elemzendő szöveget tárolja az eredeti `language` nyelven, az `id` bármilyen érték lehet. 

---

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek megmutatják, hogyan végezheti el a következő feladatokat a Python Text Analytics ügyfélkódtára segítségével:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Nevesített entitások felismerése](#named-entity-recognition-ner) 
* [Entitáskapcsolás](#entity-linking)
* [Kulcskifejezések kinyerése](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

Függvény létrehozása a `TextAnalyticsClient` objektum példányosításához a fenti `key` és `endpoint` segítségével. Ezután hozzon létre egy új ügyfelet. 

```python
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiKeyCredential

def authenticate_client():
    ta_credential = TextAnalyticsApiKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Függvény létrehozása a `TextAnalyticsClient` objektum példányosításához a fenti `key` és `endpoint` segítségével. Ezután hozzon létre egy új ügyfelet. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Hangulatelemzés

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

Hozzon létre egy új függvényt `sentiment_analysis_example()` néven, amely az ügyfelet használja argumentumként, majd meghívja az `analyze_sentiment()` függvényt. A visszaadott válaszobjektum tartalmazni fogja a teljes bemeneti dokumentum hangulati címkéjét és pontszámát, illetve az egyes mondatokhoz tartozó hangulatelemzést.


```python
def sentiment_analysis_example(client):

    document = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(inputs=document)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Length: {}]".format(sentence.grapheme_length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))

            
sentiment_analysis_example(client)
```

### <a name="output"></a>Kimenet

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00

[Length: 30]
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

[Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Hitelesítsen egy ügyfélobjektumot és hívja meg a [sentiment()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) függvényt. Haladjon végig az eredményeken, és jelenítse meg az egyes dokumentumok azonosítóit és a dokumentumszintű hangulati pontszámot. A 0-hoz közelebbi értékek negatív, az 1-hez közelebbiek pozitív érzelmekre utalnak.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Kimenet

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>Nyelvfelismerés

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

Hozzon létre egy új függvényt `language_detection_example()` néven, amely az ügyfelet használja argumentumként, majd meghívja az `detect_language()` függvényt. A visszaadott válaszobjektum tartalmazza az észlelt nyelvet a `primary_language` alatt, ha sikeres volt, és egy `error` objektumot, ha nem.

> [!Tip]
> Bizonyos esetekben nehéz lehet a nyelveket megkülönböztetni a bemenet alapján. A `country_hint` paraméterrel megadhat egy 2 karakteres országkódot. Alapértelmezés szerint az API a „US” országkódot használja alapértelmezett countryHint paraméterként. Ha ez a működés nem megfelelő, állítsa az értéket üres karakterláncra (`country_hint : ""`). 

```python
def language_detection_example(client):
    try:
        document = ["Ce document est rédigé en Français."]
        response = client.detect_language(inputs = document, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Kimenet

```console
Language:  French
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Az előbb létrehozott ügyfél segítségével hívja meg a [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) függvényt, és kérje le az eredményt. Ezután haladjon végig az eredményeken, és jelenítse meg az egyes dokumentumok azonosítóit és elsőként visszaadott nyelvét.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Kimenet

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Nevesített entitások felismerése (NER)

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

> [!NOTE]
> A `3.0-preview`-s verzióban:
> * A NER külön módszereket tartalmaz a személyes adatok észlelésére. 
> * Az entitáskapcsolás és a NER két külön kérés.

Hozzon létre egy új függvényt `entity_recognition_example` néven, amely az ügyfelet használja argumentumként, majd meghívja a `recognize_entities()` függvényt, és végighalad az eredményeken. A visszaadott válaszobjektum tartalmazza az észlelt entitások listáját az `entity` alatt, ha sikeres volt, és egy `error` objektumot, ha nem. Minden észlelt entitás esetében jelenítse meg az entitás kategóriáját és alkategóriáját, ha létezik.

```python
def entity_recognition_example(client):

    try:
        document = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(inputs= document)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tLength: \t", entity.grapheme_length, "\tConfidence Score: \t", round(entity.score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Kimenet

```console
Named Entities:

    Text:    Seattle        Category:        Location       SubCategory:     GPE
    Length:          7      Confidence Score:        0.92

    Text:    last week      Category:        DateTime       SubCategory:     DateRange
    Length:          9      Confidence Score:        0.8
```

## <a name="using-ner-to-detect-personal-information"></a>A NER használata személyes adatok észlelésére

Hozzon létre egy új függvényt `entity_pii_example()` néven, amely az ügyfelet használja argumentumként, majd meghívja a `recognize_pii_entities()` függvényt, és lekéri az eredményt. Ezután haladjon végig az eredményeken, és jelenítse meg az entitásokat.

```python
def entity_pii_example(client):

        document = ["Insurance policy for SSN on file 123-12-1234 is here by approved."]


        result = client.recognize_pii_entities(inputs= document)[0]
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tCategory: ", entity.category,"\tSubCategory: ", entity.subcategory)
            print("\t\tLength: ", entity.grapheme_length, "\tScore: {0:.2f}".format(entity.score), "\n")
        
entity_pii_example(client)
```

### <a name="output"></a>Kimenet

```console
Personally Identifiable Information Entities:
    Text:  123-12-1234      Category:  U.S. Social Security Number (SSN)    SubCategory:  None
        Length:  11     Score: 0.85
```


## <a name="entity-linking"></a>Entitáskapcsolás

Hozzon létre egy új függvényt `entity_linking_example()` néven, amely az ügyfelet használja argumentumként, majd meghívja a `recognize_linked_entities()` függvényt, és végighalad az eredményeken. A visszaadott válaszobjektum tartalmazza az észlelt entitások listáját az `entities` alatt, ha sikeres volt, és egy `error` objektumot, ha nem. Mivel a társított entitások egyedileg vannak azonosítva, ugyanannak az entitásnak az előfordulásai egy `entity` objektum alatt vannak csoportosítva, `match` objektumok listájaként.

```python
def entity_linking_example(client):

    try:
        document = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(inputs= document)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.2f}".format(match.score), "\tLength: {}\n".format(match.grapheme_length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Kimenet

```console
Linked Entities:

    Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.78     Length: 11

    Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.55     Length: 10

        Text: Gates
        Score: 0.55     Length: 5

    Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.53     Length: 10

    Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.47     Length: 9

        Text: Microsoft
        Score: 0.47     Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.25     Length: 7

    Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.28     Length: 5

```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

> [!NOTE]
> A 2.1-es verzióban az entitáskapcsolás a NER-válasz részét képezi.

Az előbb létrehozott ügyfél segítségével hívja meg az [entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) függvényt, és kérje le az eredményt. Ezután haladjon végig az eredményeken, és jelenítse meg az egyes dokumentumok azonosítóját és a benne található entitásokat.

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

### <a name="output"></a>Kimenet

```console
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése


#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

Hozzon létre egy új függvényt `key_phrase_extraction_example()` néven, amely az ügyfelet használja argumentumként, majd meghívja az `extract_key_phrases()` függvényt. Az eredmény tartalmazza az észlelt kulcskifejezések listáját a `key_phrases` alatt, ha sikeres volt, és egy `error` objektumot, ha nem. Jelenítse meg az észlelt kulcskifejezéseket.

```python
def key_phrase_extraction_example(client):

    try:
        document = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(inputs= document)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Kimenet

```console
    Key Phrases:
         cat
         veterinarian
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Az előbb létrehozott ügyfél segítségével hívja meg a [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) függvényt, és kérje le az eredményt. Ezután haladjon végig az eredményeken, és jelenítse meg az egyes dokumentumok azonosítóját és a bennük található kulcskifejezéseket.

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


### <a name="output"></a>Kimenet

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

---