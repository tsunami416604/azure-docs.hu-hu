---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 09/05/2019
ms.author: aahi
ms.openlocfilehash: 3623d18e53881768ea6409c970065de231d233c3
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750224"
---
<a name="HOLTop"></a>


[Dokumentáció](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [csomag (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [minták](https://github.com/Azure-Samples/samples-cognitive-services-python-sdk/)


## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)

## <a name="setting-up"></a>Beállítás

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure-erőforrás létrehozása

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után az ügyféloldali kódtár a következővel telepíthető:

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-fájlt, és importálja a következő könyvtárakat.

[!code-python[import statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Hozzon létre változókat az erőforrás Azure-végpontjának és előfizetési kulcsának létrehozásához. Szerezze be ezeket az értékeket a környezeti változók TEXT_ANALYTICS_SUBSCRIPTION_KEY és TEXT_ANALYTICS_ENDPOINT. Ha ezeket a környezeti változókat az alkalmazás szerkesztésének megkezdése után hozta létre, akkor be kell állítania és újra meg kell nyitnia azt a szerkesztőt, IDE vagy rendszerhéjt, amelyet a változók eléréséhez használ.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

[!code-python[endpoint and key variables](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=initialVars)]

## <a name="object-model"></a>Objektummodell

Az Text Analytics-ügyfél egy [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél számos módszert biztosít a szöveg elemzéséhez, egyetlen sztringként vagy kötegként. 

A rendszer elküldi a szöveget az API-nak `documents`-listaként, amely a használt módszertől függően `id`, `text`és `language` attribútumok kombinációját tartalmazó objektumokat `dictionary`. A `text` attribútum tárolja a forrás `language`elemezni kívánt szöveget, és a `id` értéke lehet. 

A válasz objektum az egyes dokumentumok elemzési információit tartalmazó lista. 

## <a name="code-examples"></a>Példák a kódokra

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Pythonhoz készült Text Analytics ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Entitások felismerése](#entity-recognition)
* [Fő kifejezés kibontása](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre egy új [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) a végponttal, valamint egy, a kulcsot tartalmazó `CognitiveServicesCredentials` objektumot.

[!code-python[client authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

## <a name="sentiment-analysis"></a>Hangulatelemzés

Hitelesítse az ügyfél objektumát, és hívja meg a [hangulat ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) függvényt. Ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint a hangulat pontszámát. Ha a pontszám közelebb van a 0 értékhez, a negatív érzést jelez, míg az 1. számú pontszám pozitív hangulatot jelez.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Kimenet

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Nyelvfelismerés

A korábban létrehozott ügyfél használatával hívja meg a [detect_language ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) , és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az első visszaadott nyelvet.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Kimenet

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Entitások felismerése

A korábban létrehozott ügyfél használatával hívja meg az [entitások ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) függvényt, és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint a benne található entitásokat.

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

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

A korábban létrehozott ügyfél használatával hívja meg a [key_phrases ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) függvényt, és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az abban található fő kifejezéseket.

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
