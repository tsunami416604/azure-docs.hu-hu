---
title: 'Gyors útmutató: Meghívja a Text Analytics szolgáltatást, a Python SDK használatával'
titleSuffix: Azure Cognitive Services
description: Get information és kód minták segítségével gyorsan Ismerkedés a szövegelemzési API-val az Azure Cognitive Servicesben.
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: aahi
ms.openlocfilehash: b319abf22f9aa4cdd9a5fef91be0628672d47bd4
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297783"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-python-sdk"></a>Gyors útmutató: Meghívja a Text Analytics szolgáltatást, a Python SDK használatával 
<a name="HOLTop"></a>

Ez a rövid útmutató segítségével megkezdheti a Text Analytics SDK-val nyelvi elemzése a Pythonhoz készült. Bár a Text Analytics REST API kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba anélkül szerializálásához és deszerializálásához JSON. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py).

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.x](https://www.python.org/)

* A Text Analytics [pythonhoz készült SDK](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) telepítheti a csomagot:

    `pip install --upgrade azure-cognitiveservices-language-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Rendelkeznie kell a [végpontját és hozzáférési kulcsát](../How-tos/text-analytics-how-to-access-key.md) , amely jött létre az Ön számára a regisztrációhoz.

## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazás a kedvenc szerkesztőjében, vagy IDE. Ezután adja hozzá a következő importálási utasításokat a fájlhoz.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="authenticate-your-credentials"></a>A hitelesítő adatokat

> [!Tip]
> A titkos kulcsok az éles rendszereket a telepítés biztonságának használatát javasoljuk [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

Miután kiválasztotta a Szövegelemzés előfizetéshez kulcs egy olyan változót, hozza létre a `CognitiveServicesCredentials` objektum vele.

```python
subscription_key = "enter-your-key-here"
credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="create-a-text-analytics-client"></a>A Text Analytics-ügyfél létrehozása

Hozzon létre egy új `TextAnalyticsClient` rendelkező objektum `credentials` és `text_analytics_url` paraméterként. A megfelelő Azure-régiót használni a Szövegelemzés előfizetéshez (például `westcentralus`).

```
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>Hangulatelemzés

Az API-hoz a hasznos listája áll `documents`, amely vannak szótárak tartalmazó egy `id` és a egy `text` attribútum. A `text` attribútumtárak elemezni, a szöveg és a `id` bármilyen érték lehet. 

```python
documents = [
  {
    "id": "1", 
    "language": "en", 
    "text": "I had the best day of my life."
  },
  {
    "id": "2", 
    "language": "en", 
    "text": "This was a waste of my time. The speaker put me to sleep."
  },  
  {
    "id": "3", 
    "language": "es", 
    "text": "No tengo dinero ni nada que dar..."
  },  
  {
    "id": "4", 
    "language": "it", 
    "text": "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
  }
]
```

Hívja a `sentiment()` funkciót, és az eredményt kapja. Ezután Iterál végig az eredményeket, és nyomtassa ki minden egyes dokumentum-Azonosítót és véleménypontszámot. 0 közelebb pontszámot azt jelzi, hogy egy negatív véleményt jelölnek, míg 1 közelebb pontszámot azt jelzi, hogy egy pozitív véleményt.

```python
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
     print("Document Id: ", document.id, ", Sentiment Score: ", "{:.2f}".format(document.score))
```

### <a name="output"></a>Kimenet

```console
Document Id:  1 , Sentiment Score:  0.87
Document Id:  2 , Sentiment Score:  0.11
Document Id:  3 , Sentiment Score:  0.44
Document Id:  4 , Sentiment Score:  1.00
```

## <a name="language-detection"></a>Nyelvfelismerés

Hozzon létre egy szótárak, a dokumentumot tartalmazó elemezni szeretné. A `text` attribútumtárak elemezni, a szöveg és a `id` bármilyen érték lehet. 

```python
documents = [
    { 
        'id': '1', 
        'text': 'This is a document written in English.' 
    },
    {
        'id': '2', 
        'text': 'Este es un document escrito en Español.' 
    },
    { 
        'id': '3', 
        'text': '这是一个用中文写的文件' 
    }
]
``` 

Az ügyfél korábban létrehozott használja, hívja `detect_language()` és az eredményt kapja. Ezután Iterál végig az eredményeket, és nyomtassa ki minden egyes dokumentum-Azonosítót és az első visszaadott nyelv.

```python
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id , ", Language: ", document.detected_languages[0].name)
```

### <a name="output"></a>Kimenet

```console
Document Id:  1 , Language:  English
Document Id:  2 , Language:  Spanish
Document Id:  3 , Language:  Chinese_Simplified
```

## <a name="entity-recognition"></a>Entitások felismerése

Hozzon létre egy Tulajdonságtár, amely tartalmazza a dokumentumok elemezni szeretné. A `text` attribútumtárak elemezni, a szöveg és a `id` bármilyen érték lehet. 


```python
documents = [
    {
        "id": "1",
        "language": "en", 
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    },
    {
        "id": "2",
        "language": "es", 
        "text": "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    }
]
```

Az ügyfél korábban létrehozott használja, hívja `entities()` funkciót, és az eredményt kapja. Ezután Iterál végig az eredményeket, és nyomtassa ki minden egyes dokumentum-Azonosítót és a benne tárolt entitásokat.

```python
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ",entity.name, "\tType: ", entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```


### <a name="output"></a>Kimenet

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
Document Id:  2
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  21     Length:  9  Score:  1.00
         NAME:  Redmond (Washington)    Type:  Location     Sub-type:  None
            Offset:  60     Length:  7  Score:  0.99
         NAME:  21 kilómetros   Type:  Quantity     Sub-type:  Dimension
            Offset:  71     Length:  13     Score:  0.80
         NAME:  Seattle     Type:  Location     Sub-type:  None
            Offset:  88     Length:  7  Score:  1.00
```

## <a name="key-phrase-extraction"></a>A kulcsfontosságú kifejezések kinyerése

Hozzon létre egy Tulajdonságtár, amely tartalmazza a dokumentumok elemezni szeretné. A `text` attribútumtárak elemezni, a szöveg és a `id` bármilyen érték lehet. 


```python
documents = [
    {
        "id": "1", 
        "language": "ja", 
        "text": "猫は幸せ"
    },
    {
        "id": "2", 
        "language": "de", 
        "text": "Fahrt nach Stuttgart und dann zum Hotel zu Fu."
    },
    {
        "id": "3", 
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    },
    {
        "id": "4", 
        "language": "es", 
        "text": "A mi me encanta el fútbol!"
    }
]
```

Az ügyfél korábban létrehozott használja, hívja `key_phrases()` funkciót, és az eredményt kapja. Ezután Iterál végig az eredményeket, és nyomtassa ki minden egyes dokumentum-Azonosítót és a benne tárolt kulcskifejezéseket.

```python
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t",phrase)
```

### <a name="output"></a>Kimenet

```console
Document Id:  1
    Phrases:
         幸せ
Document Id:  2
    Phrases:
         Stuttgart
         Hotel
         Fahrt
         Fu
Document Id:  3
    Phrases:
         cat
         veterinarian
Document Id:  4
    Phrases:
         fútbol
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még

* [Mi az a szövegelemzési API-t?](../overview.md)
* [Felhasználói bemutató példák](../text-analytics-user-scenarios.md)
* [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
