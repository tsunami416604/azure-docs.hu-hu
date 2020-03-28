---
title: 'Rövid útmutató: Szövegfelolvasás konvertálása, Node.js – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban megtudhatja, hogyan konvertálhatja a szövegfelolvasást a Node.js és a Text-To-Speech REST API használatával. Az útmutatóban szereplő mintaszöveg beszédszintetizáló nyelvi (SSML) formában van felépítve. Ez lehetővé teszi a beszédválasz hangjának és nyelvének kiválasztását.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: b120acd25874585a744fb774aafe15d32d7baf08
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74976502"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Rövid útmutató: Szövegfelolvasás konvertálása a Node.js használatával

Ebben a rövid útmutatóban megtudhatja, hogyan konvertálhatja a szövegfelolvasást a Node.js és a text-to-speech REST API használatával. Az útmutatóban szereplő kérelemtörzs [beszédszintetizáló nyelvi (SSML)](speech-synthesis-markup.md)néven jelenik meg, amely lehetővé teszi a válasz hangjának és nyelvének kiválasztását.

Ez a rövid útmutató egy [Azure Cognitive Services-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) igényel egy beszédszolgáltatási erőforrással. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Node 8.12.x vagy újabb](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)vagy kedvenc szövegszerkesztő
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Kap egy részére szabad!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Projekt létrehozása és függőségek megkövetelése

Hozzon létre egy új Node.js projektsegítségével a kedvenc IDE vagy szerkesztő. Ezután másolja a következő kódrészletet egy `tts.js` nevű fájlba a projektjében.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `npm install request request-promise xmlbuilder readline-sync`.

## <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

A szöveg-beszéd REST API hitelesítéshez hozzáférési jogkivonatot igényel. A hozzáférési jogkivonat beszerezéséhez cserére van szükség. Ez a függvény a beszédfelismerési szolgáltatás előfizetési kulcsát egy hozzáférési jogkivonata a `issueToken` végpont használatával cseréli.

Ez a minta feltételezi, hogy a beszédfelismerési szolgáltatás előfizetése az USA nyugati régiójában található. Ha másik régiót használ, frissítse a `uri`értékét. A teljes listát a [Régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Másolja a projektbe a következő kódot:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> A hitelesítéssel kapcsolatos további információkért lásd: [Hitelesítés hozzáférési jogkivonattal](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

A következő szakaszban létrehozunk egy függvényt a szövegfelolvasó API hívásához és a szintetizált beszédválasz mentéséhez.

## <a name="make-a-request-and-save-the-response"></a>Kérés kérése és mentése

Itt fogja építeni a kérelmet a szöveg-beszéd API-t, és mentse a beszédválasz. Ez a minta feltételezi, hogy a nyugat-amerikai végpontot használja. Ha az erőforrás egy másik régióban van `uri`regisztrálva, győződjön meg arról, hogy frissíti a . További információt a [Beszédszolgáltatási területek című témakörben talál.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)

Ezután hozzá kell adnia a szükséges fejléceket a kérelemhez. Győződjön meg `User-Agent` arról, hogy frissíti az erőforrás nevét (az Azure Portalon található), és állítsa be `X-Microsoft-OutputFormat` a kívánt hangkimenetre. A kimeneti formátumok teljes listáját a [Hangkimenetek](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Ezután készítse el a kérelem törzsét a beszédszintetizáló nyelvi (SSML) használatával. Ez a minta határozza meg `text` a szerkezetet, és a korábban létrehozott bemenetet használja.

>[!NOTE]
> Ez a `JessaRUS` minta a hangbetűtípust használja. A Microsoft által biztosított hangok/nyelvek teljes listáját a Nyelvi támogatás című témakörben [tetszése idáig című](language-support.md)témakörben található.
> Ha egyedi, felismerhető hangot szeretne létrehozni a márkához, olvassa el [az Egyéni hangbetűtípusok létrehozása](how-to-customize-voice-font.md).

Végül, akkor, hogy egy kérelmet a szolgáltatás. Ha a kérés sikeres, és egy 200-as állapotkódot `TTSOutput.wav`ad vissza, a beszédválasz a .

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

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
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Már majdnem kész. Az utolsó lépés egy aszinkron függvény létrehozása. Ez a függvény egy környezeti változóból olvassa be az előfizetési kulcsot, szöveget kér, jogkivonatot kér, megvárja a kérés befejezését, majd konvertálja a szövegfelolvasót, és a hangot .wav formátumban menti.

Ha nem ismeri a környezeti változókat, vagy inkább az előfizetési kulcs `process.env.SPEECH_SERVICE_KEY` karakterláncként kódolt tesztelése, cserélje le az előfizetési kulcsot karakterláncként.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ez az, készen áll a szövegfelolvasó mintaalkalmazás futtatására. A parancssorból (vagy terminálmunkamenetből) keresse meg a projektkönyvtárat, és futtassa a következőket:

```console
node tts.js
```

Amikor a rendszer kéri, írja be, amit szövegből beszédbe szeretne konvertálni. Ha sikeres, a beszédfájl a projekt mappájában található. Játssza le kedvenc médialejátszójával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Győződjön meg arról, hogy eltávolít minden bizalmas információt a mintaalkalmazás forráskódjából, például az előfizetési kulcsokból.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Node.js minták felfedezése a GitHubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Lásd még

* [Szövegfelolvasó API-hivatkozások](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Egyéni hangbetűtípusok létrehozása](how-to-customize-voice-font.md)
* [Hangminták rögzítése egyéni hang létrehozásához](record-custom-voice-samples.md)
