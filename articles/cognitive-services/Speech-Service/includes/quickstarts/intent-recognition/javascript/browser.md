---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 4889d9b0f142206bf26a69f275cb60598fba577f
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035720"
---
## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez.

```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Microsoft Cognitive Services Speech SDK JavaScript Quickstart</title>
    <meta charset="utf-8" />
    </head>
    <body style="font-family:'Helvetica Neue',Helvetica,Arial,sans-serif; font-size:13px;">
    </body>
    </html>
```
## <a name="add-ui-elements"></a>FELHASZNÁLÓIFELÜLET-elemek hozzáadása

Most hozzáadunk néhány alapszintű felhasználói felületet a beviteli mezőkhöz, hivatkozhatunk a Speech SDK JavaScriptre, és ha elérhető, egy engedélyezési tokent is megragadunk.

```html  
<body style="font-family:'Helvetica Neue',Helvetica,Arial,sans-serif; font-size:13px;">
  <div id="content" style="display:none">
    <table width="100%">
      <tr>
        <td></td>
        <td><h1 style="font-weight:500;">Microsoft Cognitive Services Speech SDK JavaScript Quickstart</h1></td>
      </tr>
      <tr>
        <td align="right"><a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started" target="_blank">Subscription</a>:</td>
        <td><input id="subscriptionKey" type="text" size="40" value="subscription"></td>
      </tr>
      <tr>
        <td align="right">Region</td>
        <td><input id="serviceRegion" type="text" size="40" value="YourServiceRegion"></td>
      </tr>
      <tr>
        <td align="right">Application ID:</td>
        <td><input id="appId" type="text" size="60" value="YOUR_LANGUAGE_UNDERSTANDING_APP_ID"></td>
      </tr>
      <tr>
        <td></td>
        <td><button id="startIntentRecognizeAsyncButton">Start Intent Recognition</button></td>
      </tr>
      <tr>
        <td align="right" valign="top">Input Text</td>
        <td><textarea id="phraseDiv" style="display: inline-block;width:500px;height:200px"></textarea></td>
      </tr>
      <tr>
        <td align="right" valign="top">Result</td>
        <td><textarea id="statusDiv" style="display: inline-block;width:500px;height:100px"></textarea></td>
      </tr>
    </table>
  </div>

  <script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>

  <script>
  // Note: Replace the URL with a valid endpoint to retrieve
  //       authorization tokens for your subscription.
  var authorizationEndpoint = "token.php";

  function RequestAuthorizationToken() {
    if (authorizationEndpoint) {
      var a = new XMLHttpRequest();
      a.open("GET", authorizationEndpoint);
      a.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
      a.send("");
      a.onload = function() {
                var token = JSON.parse(atob(this.responseText.split(".")[1]));
                serviceRegion.value = token.region;
                authorizationToken = this.responseText;
                subscriptionKey.disabled = true;
                subscriptionKey.value = "using authorization token (hit F5 to refresh)";
                console.log("Got an authorization token: " + token);
      }
    }
  }
  </script>

  <script>
    // status fields and start button in UI
    var phraseDiv;
    var statusDiv;
    var startIntentRecognizeAsyncButton;

    // subscription key, region, and appId for LUIS services.
    var subscriptionKey, serviceRegion, appId;
    var authorizationToken;
    var SpeechSDK;
    var recognizer;

    document.addEventListener("DOMContentLoaded", function () {
      startIntentRecognizeAsyncButton = document.getElementById("startIntentRecognizeAsyncButton");
      subscriptionKey = document.getElementById("subscriptionKey");
      serviceRegion = document.getElementById("serviceRegion");
      appId = document.getElementById("appId");
      phraseDiv = document.getElementById("phraseDiv");
      statusDiv = document.getElementById("statusDiv");

      startIntentRecognizeAsyncButton.addEventListener("click", function () {
        startIntentRecognizeAsyncButton.disabled = true;
        phraseDiv.innerHTML = "";
        statusDiv.innerHTML = "";
      });

      if (!!window.SpeechSDK) {
        SpeechSDK = window.SpeechSDK;
        startIntentRecognizeAsyncButton.disabled = false;

        document.getElementById('content').style.display = 'block';
        document.getElementById('warning').style.display = 'none';

        // in case we have a function for getting an authorization token, call it.
        if (typeof RequestAuthorizationToken === "function") {
          RequestAuthorizationToken();
        }
      }
    });
  </script>
```
 
## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

Az objektumok inicializálásához `SpeechRecognizer` létre kell hoznia egy olyan konfigurációt, amely az előfizetési kulcsot és az előfizetési régiót használja. Szúrja be ezt a kódot a `startRecognizeOnceAsyncButton.addEventListener()` metódusba.

> [!NOTE]
> A Speech SDK alapértelmezés szerint az en-us nyelv használatával ismeri fel a nyelvet, a forrás nyelvének kiválasztásával kapcsolatos információkért lásd: nyelv [megadása a beszédhez szöveghez](../../../../how-to-specify-source-language.md) .


```JavaScript
        // if we got an authorization token, use the token. Otherwise use the provided subscription key
        var speechConfig;
        if (authorizationToken) {
          speechConfig = SpeechSDK.SpeechConfig.fromAuthorizationToken(authorizationToken, serviceRegion.value);
        } else {
          if (subscriptionKey.value === "" || subscriptionKey.value === "subscription") {
            alert("Please enter your Microsoft Cognitive Services Speech subscription key!");
            return;
          }
          startIntentRecognizeAsyncButton.disabled = false;
          speechConfig = SpeechSDK.SpeechConfig.fromSubscription(subscriptionKey.value, serviceRegion.value);
        }

        speechConfig.speechRecognitionLanguage = "en-US";
```

## <a name="create-an-audio-configuration"></a>Hang konfigurációjának létrehozása

Most létre kell hoznia egy `AudioConfig` objektumot, amely a bemeneti eszközre mutat. Szúrja be ezt a kódot a `startIntentRecognizeAsyncButton.addEventListener()` metódusba közvetlenül a beszédfelismerési konfiguráció alatt.

```JavaScript
        var audioConfig = SpeechSDK.AudioConfig.fromDefaultMicrophoneInput();
```

## <a name="initialize-a-intentrecognizer"></a>IntentRecognizer inicializálása

Most hozzuk létre az `IntentRecognizer` objektumot a `SpeechConfig` `AudioConfig` korábban létrehozott és objektumok használatával. Szúrja be ezt a kódot a `startIntentRecognizeAsyncButton.addEventListener()` metódusba.

```JavaScript
        recognizer = new SpeechSDK.IntentRecognizer(speechConfig, audioConfig);
```

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel és szándékok hozzáadása

Hozzá kell rendelnie egy `LanguageUnderstandingModel` -t a szándék-felismerőhöz, és fel kell vennie a felismerni kívánt leképezéseket. Az előre elkészített tartományból szándékait fogjuk használni a Home Automation számára.

Szúrja be a kódot a alá `IntentRecognizer` . Győződjön meg arról, hogy a helyére `"YourLanguageUnderstandingAppId"` a Luis-alkalmazás azonosítóját írja. 

```JavaScript
        if (appId.value !== "" && appId.value !== "YOUR_LANGUAGE_UNDERSTANDING_APP_ID") {
          var lm = SpeechSDK.LanguageUnderstandingModel.fromAppId(appId.value);

          recognizer.addAllIntents(lm);
        }
```
## <a name="recognize-an-intent"></a>Szándék felismerése

Az `IntentRecognizer` objektumból meg fogja hívni a `recognizeOnceAsync()` metódust. Ez a módszer lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy egyetlen kifejezést küld az észleléshez, és ha a kifejezést azonosította a beszédfelismerés felismerésének leállításához.

Szúrja be ezt a kódot a modell mellett:

```JavaScript
        recognizer.recognizeOnceAsync(
          function (result) {
            window.console.log(result);
  
            phraseDiv.innerHTML = result.text + "\r\n";
  
            statusDiv.innerHTML += "(continuation) Reason: " + SpeechSDK.ResultReason[result.reason];
            switch (result.reason) {
              case SpeechSDK.ResultReason.RecognizedSpeech:
                statusDiv.innerHTML += " Text: " + result.text;
                break;
              case SpeechSDK.ResultReason.RecognizedIntent:
                statusDiv.innerHTML += " Text: " + result.text + " IntentId: " + result.intentId;
                
                // The actual JSON returned from Language Understanding is a bit more complex to get to, but it is available for things like
                // the entity name and type if part of the intent.
                statusDiv.innerHTML += " Intent JSON: " + result.properties.getProperty(SpeechSDK.PropertyId.LanguageUnderstandingServiceResponse_JsonResult);
                phraseDiv.innerHTML += result.properties.getProperty(SpeechSDK.PropertyId.LanguageUnderstandingServiceResponse_JsonResult) + "\r\n";
                break;
              case SpeechSDK.ResultReason.NoMatch:
                var noMatchDetail = SpeechSDK.NoMatchDetails.fromResult(result);
                statusDiv.innerHTML += " NoMatchReason: " + SpeechSDK.NoMatchReason[noMatchDetail.reason];
                break;
              case SpeechSDK.ResultReason.Canceled:
                var cancelDetails = SpeechSDK.CancellationDetails.fromResult(result);
                statusDiv.innerHTML += " CancellationReason: " + SpeechSDK.CancellationReason[cancelDetails.reason];
              
              if (cancelDetails.reason === SpeechSDK.CancellationReason.Error) {
                statusDiv.innerHTML += ": " + cancelDetails.errorDetails;
              }
            break;
            }
            statusDiv.innerHTML += "\r\n";
            startIntentRecognizeAsyncButton.disabled = false;
          },
          function (err) {
            window.console.log(err);
    
            phraseDiv.innerHTML += "ERROR: " + err;
            startIntentRecognizeAsyncButton.disabled = false;
          });
```

## <a name="check-your-code"></a>A kód megkeresése

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-intent-recognition.html)]

## <a name="create-the-token-source-optional"></a>A jogkivonatforrás létrehozása (nem kötelező)

Ha egy webkiszolgálón szeretné futtatni a weblapot, opcionálisan megadhat egy jogkivonatforrást a bemutató alkalmazáshoz.
Így az előfizetői azonosítója soha nem fog kikerülni a kiszolgálóról, közben viszont a felhasználóknak úgy teheti lehetővé a beszédfelismerési képességek használatát, hogy ők maguk semmilyen engedélyezési kódot nem adnak meg.

Hozzon létre egy új fájlt `token.php` néven. Ebben a példában feltételezzük, hogy a webkiszolgáló támogatja a PHP programozási nyelvet a curl enabled használatával. Írja be a következő kódot:

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> Az engedélyezési jogkivonatok csupán korlátozott élettartammal rendelkeznek.
> Ez az egyszerűsített példa nem mutatja be, hogyan lehet frissíteni az engedélyezési jogkivonatokat. Felhasználóként manuálisan betöltheti az oldalt újból, vagy megnyomhatja az F5 billentyűt a frissítéshez.

## <a name="build-and-run-the-sample-locally"></a>A minta helyi buildelése és futtatása

Az alkalmazás elindításához kattintson duplán az index.html fájlra, vagy nyissa meg az index.html fájlt kedvenc webböngészőjében. Egy egyszerű grafikus felhasználói felületet fog bevezetni, amely lehetővé teszi a LUIS-kulcs, a [Luis-régió](../../../../regions.md)és a Luis-alkalmazás azonosítójának megadását. Miután megadta a mezőket, a megfelelő gombra kattintva elindíthatja a felismerést a mikrofon használatával.

> [!NOTE]
> Ez a metódus nem működik a Safari böngészőben.
> A Safari webhelyen a minta weblapot webkiszolgálón kell üzemeltetni; A Safari nem teszi lehetővé a helyi fájlokból betöltött webhelyek használatát a mikrofon használatához.

## <a name="build-and-run-the-sample-via-a-web-server"></a>A minta buildelése és futtatása webkiszolgáló használatával

Az alkalmazás elindításához nyissa meg a kedvenc webböngészőjét, és mutasson arra a nyilvános URL-címre, amelyen a mappát üzemelteti, adja meg a [Luis-régiót](../../../../regions.md) és a Luis-alkalmazás azonosítóját, és indítson el egy felismerést a mikrofon használatával. Ha be van állítva, a jogkivonat-forrástól kapja meg a tokent, és megkezdi a kimondott parancsok felismerését.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](footer.md)]
