---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 199111d88d1d5d0dfd3b52f62ad60297e9771c20
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806475"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech-erőforrás létrehozása<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md)

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez. Hozzon létre egy index. js fájlt, és adja hozzá ezt a kódot.

Ügyeljen rá, hogy a, a és a értékeit adja meg `subscriptionKey` `servcieRegion` `filename` .

```JavaScript
(function() {
  // <code>
  "use strict";
  
  // pull in the required packages.
  var sdk = require("microsoft-cognitiveservices-speech-sdk");
  var fs = require("fs");
  
  // replace with your own subscription key,
  // service region (e.g., "westus"), and
  // the name of the file you want to run
  // through the speech recognizer.
  var subscriptionKey = "YourSubscriptionKey";
  var serviceRegion = "YourServiceRegion"; // e.g., "westus"
  var filename = "YourAudioFile.wav"; // 16000 Hz, Mono
 
}());
  
```
## <a name="load-the-file-into-an-pushaudioinputstream"></a>A fájl betöltése egy PushAudioInputStream

A NodeJS a Speech SDK nem támogatja natív módon a fájlok közvetlen elérését, ezért betöltjük a fájlt, és behelyezzük a-ba `PushAudioInputStream` . Ez a kisebb fájlok esetében is jól működik, de az egy nagyobb mennyiségű `PullAudioInputStream` memória használata sokkal hatékonyabbá válik.

```JavaScript
// create the push stream we need for the speech sdk.
  var pushStream = sdk.AudioInputStream.createPushStream();
  
  // open the file and push it to the push stream.
  fs.createReadStream(filename).on('data', function(arrayBuffer) {
    pushStream.write(arrayBuffer.slice());
  }).on('end', function() {
    pushStream.close();
  });
```

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

Az objektumok inicializálásához `SpeechRecognizer` létre kell hoznia egy olyan konfigurációt, amely az előfizetési kulcsot és az előfizetési régiót használja. Szúrja be a következő kódot.

> [!NOTE]
> A Speech SDK alapértelmezés szerint az en-us nyelv használatával ismeri fel a nyelvet, a forrás nyelvének kiválasztásával kapcsolatos információkért lásd: nyelv [megadása a beszédhez szöveghez](../../../../how-to-specify-source-language.md) .

 ```JavaScript
   // now create the audio-config pointing to our stream and
  // the speech config specifying the language.
  var speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
  
  // setting the recognition language to English.
  speechConfig.speechRecognitionLanguage = "en-US";
  
```
## <a name="create-an-audio-configuration"></a>Hang konfigurációjának létrehozása

Most létre kell hoznia egy `AudioConfig` objektumot, amely az Ön számára mutat `PushAudioInputStream` . Szúrja be a kódot közvetlenül a beszédfelismerési konfiguráció alá.

```JavaScript
    var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
```

## <a name="initialize-a-speechrecognizer"></a>SpeechRecognizer inicializálása

Most hozzuk létre az `SpeechRecognizer` objektumot a `SpeechConfig` `AudioConfig` korábban létrehozott és objektumok használatával.

```JavaScript
  // create the speech recognizer.
  var recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
  
```
## <a name="recognize-a-phrase-and-display-results"></a>Kifejezés felismerése és eredmények megjelenítése

Az `SpeechRecognizer` objektumból meg fogja hívni a `recognizeOnceAsync()` metódust. Ez a módszer lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy egyetlen kifejezést küld az észleléshez, és ha a kifejezést azonosította a beszédfelismerés felismerésének leállításához.

A visszaadott szöveget vagy hibákat is írunk a konzolba, és végül lezárjuk a felismerőt.
```JavaScript
 // we are done with the setup
  console.log("Now recognizing from: " + filename);
  
  // start the recognizer and wait for a result.
  recognizer.recognizeOnceAsync(
    function (result) {
      console.log(result);
  
      recognizer.close();
      recognizer = undefined;
    },
    function (err) {
      console.trace("err - " + err);
  
      recognizer.close();
      recognizer = undefined;
    });
```
## <a name="check-your-code"></a>A kód megkeresése
```JavaScript
(function() {
  "use strict";
  
  // pull in the required packages.
  var sdk = require("microsoft-cognitiveservices-speech-sdk");
  var fs = require("fs");
  
  // replace with your own subscription key,
  // service region (e.g., "westus"), and
  // the name of the file you want to run
  // through the speech recognizer.
  var subscriptionKey = "YourSubscriptionKey";
  var serviceRegion = "YourServiceRegion"; // e.g., "westus"
  var filename = "YourAudioFile.wav"; // 16000 Hz, Mono
  
  // create the push stream we need for the speech sdk.
  var pushStream = sdk.AudioInputStream.createPushStream();
  
  // open the file and push it to the push stream.
  fs.createReadStream(filename).on('data', function(arrayBuffer) {
    pushStream.write(arrayBuffer.slice());
  }).on('end', function() {
    pushStream.close();
  });
  
  // now create the audio-config pointing to our stream and
  // the speech config specifying the language.
  var speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
  
  // setting the recognition language to English.
  speechConfig.speechRecognitionLanguage = "en-US";
  
  var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
  
  // create the speech recognizer.
  var recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
  
  // we are done with the setup
  console.log("Now recognizing from: " + filename);
  
  // start the recognizer and wait for a result.
  recognizer.recognizeOnceAsync(
    function (result) {
      console.log(result);
  
      recognizer.close();
      recognizer = undefined;
    },
    function (err) {
      console.trace("err - " + err);
  
      recognizer.close();
      recognizer = undefined;
    });

}());
```
## <a name="run-the-sample-locally"></a>A minta futtatása helyben

A kód végrehajtása a NodeJs használatával
```bash
node index.js
```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
