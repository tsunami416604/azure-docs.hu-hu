---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 617ab4114fd200c0b4306e3c27825bafe06b1764
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80240117"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

[v3 Referenciadokumentáció](https://aka.ms/azsdk-python-textanalytics-ref-docs) | [v3 Könyvtár forráskód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3 csomag (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3 minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

[v2 Referenciadokumentáció](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [v2 Könyvtár forráskód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [v2 csomag (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2 minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* Miután rendelkezik az <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure-előfizetéssel, hozzon <span class="docon docon-navigate-external x-hidden-focus"></span> </a> létre egy Text Analytics-erőforrást"  target="_blank">az Azure Portalon a kulcs és a végpont leéséhez. Üzembe helyezése után kattintson **az Ugrás az erőforrásra**gombra.
    * Szüksége lesz a kulcs és a végpont a létrehozott erőforrásból az alkalmazás és a Text Analytics API csatlakoztatásához. A kulcs és a végpont beillesztése az alábbi kódba később a rövid útmutatóban.
    * Használhatja az ingyenes tarifacsomag ( )`F0`kipróbálni a szolgáltatást, és frissítse később egy fizetett szint éles környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="install-the-client-library"></a>Az ügyféltár telepítése

A Python telepítése után telepítheti az ügyfélkönyvtárat a következőkkel:

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódfájlt? Ebben a rövid útmutatóban a kódpéldákat tartalmazó [gitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py)található. 

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódfájlt? Ebben a rövid útmutatóban a kódpéldákat tartalmazó [gitHubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py)található. 

---

### <a name="create-a-new-python-application"></a>Új python-alkalmazás létrehozása

Hozzon létre egy új Python-fájlt, és hozzon létre változókat az erőforrás Azure-végpont- és előfizetési kulcsához.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektummodell

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

A Text Analytics-ügyfél egy `TextAnalyticsClient` olyan objektum, amely a kulcs használatával hitelesíti magát az Azure-ban. Az ügyfél számos módszert biztosít a szöveg kötegként történő elemzéséhez. 

Ha a kötegelt feldolgozás szövegét `documents`az API-nak a rendszer `id`listájaként küldi el, amelyek a használt módszertől függően a , a és `text`az `language` attribútumok kombinációját tartalmazó `dictionary` objektumok. Az `text` attribútum az eredeti `language`helyen tárolja az elemzendő szöveget, és az `id` bármilyen érték lehet. 

A válaszobjektum egy lista, amely az egyes dokumentumok elemzési adatait tartalmazza. 

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

A Text Analytics-ügyfél egy [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) objektum, amely a kulcs használatával hitelesíti magát az Azure-ban. Az ügyfél számos módszert biztosít a szöveg elemzésére, egyetlen karakterláncként vagy kötegként. 

A program a szöveget a `documents`használt `dictionary` módszertől függően `id`a `text`, `language` a kombinációját és attribútumokat tartalmazó objektumok listájaként küldi el az API-nak. Az `text` attribútum az eredeti `language`helyen tárolja az elemzendő szöveget, és az `id` bármilyen érték lehet. 

---

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell elvégezni a következő feladatokat a Pythonszöveg-elemzési ügyfélkódtárjával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Elnevezett entitás felismerése](#named-entity-recognition-ner) 
* [Entitáscsatolás](#entity-linking)
* [Kulcskifejezés kinyerése](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

Hozzon létre egy függvényt, amely az objektumot a `TextAnalyticsClient` fent létrehozott `key` és `endpoint` alkalmazással hozza létre. Ezután hozzon létre egy új ügyfelet. 

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

Hozzon létre egy függvényt, amely az objektumot a `TextAnalyticsClient` fent létrehozott `key` és `endpoint` alkalmazással hozza létre. Ezután hozzon létre egy új ügyfelet. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Hangulatelemzés

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

Hozzon létre `sentiment_analysis_example()` egy új függvényt, amely az `analyze_sentiment()` ügyfelet argumentumként veszi fel, majd meghívja a függvényt. A visszaadott válaszobjektum tartalmazza a teljes bemeneti dokumentum hangulatfeliratát és pontszámát, valamint az egyes mondatok hangulatelemzését.


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

Hitelesítsen egy ügyfélobjektumot, és hívja meg a [sentiment()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) függvényt. Végighaladhat az eredményekközött, és kinyomtathatja az egyes dokumentumok azonosítóját és a hangulatpontszámát. A 0-hoz közelebbi pontszám negatív érzést, míg az 1-hez közelebbi pontszám pozitív véleményt jelez.

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

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

Hozzon létre `language_detection_example()` egy új függvényt, amely az `detect_language()` ügyfelet argumentumként veszi fel, majd meghívja a függvényt. A visszaadott válaszobjektum tartalmazza `primary_language` az észlelt nyelvet, ha sikeres, és ha `error` nem.

> [!Tip]
> Bizonyos esetekben nehéz lehet a bevitel alapján félretenni a nyelveket. A `country_hint` paraméter segítségével kétbetűs országkódot adhat meg. Alapértelmezés szerint az API az "US" az alapértelmezett countryHint, hogy távolítsa el ezt a `country_hint : ""`viselkedést vissza állíthatja ezt a paramétert beállításával ezt az értéket üres karakterlánc . 

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

A korábban létrehozott ügyfél használatával hívja meg [a detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) és az eredmény. Ezután végighaladhat az eredményeken, és nyomtassa ki az egyes dokumentumok azonosítóját és az első visszaadott nyelvet.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Kimenet

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Elnevezett entitás felismerése (NER)

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

> [!NOTE]
> Verzióban `3.0-preview`:
> * A NER külön módszereket tartalmaz a személyes adatok felderítésére. 
> * Az entitásösszekapcsolás külön kérés, mint a NER.

Hozzon létre `entity_recognition_example` egy új függvényt, amely az `recognize_entities()` ügyfelet argumentumként veszi fel, majd meghívja a függvényt, és végigvezeti az eredményeket. A visszaadott válaszobjektum tartalmazza az észlelt `entity` entitások listáját, ha sikeres, és egy `error` ha nem. Minden észlelt entitáshoz nyomtassa ki a kategória és az alkategória, ha létezik.

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

## <a name="using-ner-to-detect-personal-information"></a>A NER használata a személyes adatok felderítésére

Hozzon létre `entity_pii_example()` egy új függvényt, amely az `recognize_pii_entities()` ügyfelet argumentumként veszi fel, majd meghívja a függvényt, és megkapja az eredményt. Ezután végighaladaz eredményeken, és nyomtassa ki az entitásokat.

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

Hozzon létre `entity_linking_example()` egy új függvényt, amely az `recognize_linked_entities()` ügyfelet argumentumként veszi fel, majd meghívja a függvényt, és végigvezeti az eredményeket. A visszaadott válaszobjektum tartalmazza az észlelt `entities` entitások listáját, ha sikeres, és egy `error` ha nem. Mivel a csatolt entitások egyedi azonosítása történik, ugyanazon entitás `entity` előfordulásai `match` objektum alatt objektumok listájaként vannak csoportosítva.

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
> A 2.1-es verzióban az entitásösszekapcsolás szerepel a NER-válaszban.

A korábban létrehozott ügyfél használatával hívja meg az [entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) függvényt, és kapja meg az eredményt. Ezután végighaladhat az eredményeken, és kinyomtathatja az egyes dokumentumok azonosítóját és a benne lévő entitásokat.

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


#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

Hozzon létre `key_phrase_extraction_example()` egy új függvényt, amely az `extract_key_phrases()` ügyfelet argumentumként veszi fel, majd meghívja a függvényt. Az eredmény tartalmazza az észlelt kulcskifejezések `key_phrases` listáját, ha `error` sikeresek, és ha nem. Nyomtasson ki minden észlelt kulcskifejezést.

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

A korábban létrehozott ügyfél használatával hívja meg a [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) függvényt, és kapja meg az eredményt. Ezután végighaladhat az eredményeken, és nyomtassa ki az egyes dokumentumok azonosítóját és a benne található legfontosabb kifejezéseket.

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