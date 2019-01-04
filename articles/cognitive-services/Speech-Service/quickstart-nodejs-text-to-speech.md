---
title: 'Gyors útmutató: Szöveg-beszéd átalakítás, node.js-szel – beszédszolgáltatások konvertálása'
titleSuffix: Azure Cognitive Services
description: Ebből a gyorsútmutatóból megtudhatja, hogyan átalakítandó szöveg-hang transzformációs Node.js és a szöveg-hang transzformációs REST API használatával. A jelen útmutatóban szereplő minta szöveg van felépítve, beszéd összefoglaló Markup Language (SSML). Ez lehetővé teszi, hogy válassza ki a beszédfelismerési és nyelvi beszédfelismerési válasz.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: e2319262b669cd5a03fa3e50bf3e534e7974c72d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53551266"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Gyors útmutató: Átalakítás szöveg-hang transzformációs Node.js használatával

Ebből a gyorsútmutatóból megtudhatja, hogyan átalakítandó szöveg-hang transzformációs Node.js és a szöveg-hang transzformációs REST API használatával. Ebben az útmutatóban a kérelem törzsében van strukturálva, [Speech összefoglaló Markup Language (SSML)](speech-synthesis-markup.md), amely lehetővé teszi, hogy a válasz nyelv és válassza ki.

Ez a rövid útmutatóhoz egy [Azure Cognitive Services-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) beszédszolgáltatás erőforrással. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Node 8.12.x vagy újabb](https://nodejs.org/en/)
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével
* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcs. [Itt igényelhet ingyenesen! ](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Hozzon létre egy projektet, és a szükséges függőségek

Hozzon létre egy új Node.js-projektet, a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével használatával. Ezután másolja a következő kódrészletet egy `tts.js` nevű fájlba a projektjében.

```javascript
// Requires request for HTTP requests
const request = require('request');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');

```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `npm install request readline-sync`.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Állítsa az előfizetési kulcsot, és hozzon létre egy kérdés Szövegfelolvasás

A következő néhány szakaszban funkciók engedélyezés kezeléséhez, az szöveg-hang transzformációs API-t és érvényesíteni a választ fog létrehozni. Először hozzáadása egy előfizetési kulcsot, és hozzon létre egy szövegbevitel kérése.

```javascript
/*
 * These lines will attempt to read your subscription key from an environment
 * variable. If you prefer to hardcode the subscription key for ease of use,
 * replace process.env.SUBSCRIPTION_KEY with your subscription key as a string.  
 */
const subscriptionKey = process.env.SUBSCRIPTION_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};

// Prompts the user to input text.
let text = readline.question('What would you like to convert to speech? ');
```

## <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

A szöveg-hang transzformációs REST API egy hozzáférési jogkivonatot a hitelesítéshez szükséges. A hozzáférési jogkivonatot kapjon egy exchange szükség. Ez a minta kicserél egy hozzáférési jogkivonatot a Speech Service előfizetői azonosítóját a `issueToken` végpont.

Ez a függvény két argumentumot, az beszédszolgáltatások előfizetési kulcs és a egy visszahívási függvény vesz igénybe. A függvény kapott hozzáférési jogkivonatot, miután átadja az értéket a visszahívási függvény. A következő szakaszban a függvény, amely az szöveg-hang transzformációs API-t, és mentse a szintetizált válasz hozunk létre.

Ez a példa feltételezi, hogy a beszédfelismerési szolgáltatás előfizetését az USA nyugati régiójában. Ha egy másik régiót használ, módosítsa a `uri`. A teljes listát lásd: [régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Másolja a projektbe a következő kódot:

```javascript
function textToSpeech(subscriptionKey, saveAudio) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };
    // This function retrieve the access token and is passed as callback
    // to request below.
    function getToken(error, response, body) {
        console.log("Getting your token...\n")
        if (!error && response.statusCode == 200) {
            //This is the callback to our saveAudio function.
            // It takes a single argument, which is the returned accessToken.
            saveAudio(body)
        }
        else {
          throw new Error(error);
        }
    }
    request(options, getToken)
}
```

> [!NOTE]
> A hitelesítés további információkért lásd: [hitelesítés hozzáférési jogkivonatot a](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Kérés és a válasz mentése

Ide fog a kérést a szöveg-hang transzformációs API és a speech válasz mentése. Ez a példa feltételezi, hogy az USA nyugati RÉGIÓJA végpont használata. Ha az erőforrás egy másik régióba regisztrálva van, ellenőrizze, hogy frissíti a `uri`. További információkért lásd: [beszédszolgáltatás régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Következő lépésként hozzá kell a kéréshez szükséges fejlécek. Győződjön meg arról, hogy frissítenie `User-Agent` az erőforrás (az Azure Portalon található), és a készlet nevére `X-Microsoft-OutputFormat` , az előnyben részesített hangkimeneti. Kimeneti formátumok teljes listáját lásd: [hang kimenete](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs).

Ezután hozhat létre a kérelem törzsében Speech összefoglaló Markup Language (SSML) használatával. Ez a példa a struktúrát határozza meg, és használja a `text` adjon meg a korábban létrehozott.

>[!NOTE]
> Ebben a példában a `JessaRUS` hangtípusú. A Microsoft teljes listáját adott beszédhangot/nyelvek, lásd: [nyelvi támogatás](language-support.md).
> Ha érdekli a saját márkáját egy egyedi, könnyen felismerhető névre hangalapú létrehozása, [létrehozása egyéni hangtípust](how-to-customize-voice-font.md).

Végül paritásadatok egy kérelmet a szolgáltatáshoz. Ha a kérelem sikeres, és egy 200 állapotkódot adott vissza, a beszéd válasz kiírt `sample.wav`.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function saveAudio(accessToken) {
    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: '<speak version=\'1.0\' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang=\'en-US\'>\n<voice  name=\'Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)\'>' + text + '</voice> </speak>'
    };
    // This function makes the request to convert speech to text.
    // The speech is returned as the response.
    function convertText(error, response, body){
      if (!error && response.statusCode == 200) {
        console.log("Converting text-to-speech. Please hold...\n")
      }
      else {
        throw new Error(error);
      }
      console.log("Your file is ready.\n")
    }
    // Pipe the response to file.
    request(options, convertText).pipe(fs.createWriteStream('sample.wav'));
}
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Már majdnem kész. Az utolsó lépés az, hogy a hívás a `textToSpeech` függvény.

```javascript
// Start the sample app.
textToSpeech(subscriptionKey, saveAudio);
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ennyi az egész, készen áll a szöveg-hang transzformációs mintaalkalmazás futtatásához. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
node tts.js
```

Amikor a rendszer kéri, írjon be bármilyen kívánt szöveg-hang transzformációs konvertálása. Ha ez sikeres, a beszéd fájl található a projektmappába. A kedvenc media player használatával lejátszani.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ellenőrizze, hogy a bizalmas adatok eltávolítása a mintaalkalmazás forráskódja, például a előfizetési kulcsok.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szöveget beszéddé átalakító API-referencia](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)

## <a name="see-also"></a>Lásd még

* [Egyéni hangtípust létrehozása](how-to-customize-voice-font.md)
* [Hozzon létre egy egyéni beszédfelismerési rekord hangalapú minták](record-custom-voice-samples.md)
