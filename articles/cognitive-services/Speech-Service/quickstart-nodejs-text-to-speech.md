---
title: 'Gyors útmutató: szöveg-beszéd átalakítás, Node.js-Speech szolgáltatás átalakítása'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan alakíthatja át a szöveg – beszéd szöveget Node.js és a szöveg-beszéd REST API használatával. Az útmutatóban foglalt mintaszöveg a Speech szintézis Markup Language (SSML) nyelvre van strukturálva. Ez lehetővé teszi a beszédfelismerési válasz hangjának és nyelvének kiválasztását.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: 38f8df1e721d2e29d4871a05263d9f4559aaedc4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406809"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Gyors útmutató: szöveg – beszéd konvertálása Node.js használatával

Ebből a rövid útmutatóból megtudhatja, hogyan alakíthatja át a szöveg – beszéd szöveget Node.js és a szöveg-beszéd REST API használatával. Az útmutatóban szereplő kérelem törzse a [Speech szintézis Markup Language (SSML) nyelvre](speech-synthesis-markup.md)van strukturálva, amely lehetővé teszi a válasz hangjának és nyelvének kiválasztását.

Ehhez a rövid útmutatóhoz egy Speech Service-erőforrással rendelkező [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) van szükség. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* <a href="https://nodejs.org/en/" target="_blank">Node 8.12. x vagy újabb<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"></span> </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"></span> </a>vagy a kedvenc szövegszerkesztője
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be ingyen!](get-started.md)

## <a name="create-a-project-and-require-dependencies"></a>Projekt létrehozása és függőségek megkövetelése

Hozzon létre egy új Node.js projektet a kedvenc IDE vagy szerkesztő használatával. Ezután másolja a következő kódrészletet egy `tts.js` nevű fájlba a projektjében.

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

A szöveg-beszéd REST APIhoz hozzáférési jogkivonat szükséges a hitelesítéshez. Hozzáférési jogkivonat lekéréséhez Exchange szükséges. Ez a függvény a beszédfelismerési szolgáltatás előfizetési kulcsát a végpont használatával cseréli ki egy hozzáférési jogkivonatra `issueToken` .

Ez a példa azt feltételezi, hogy a Speech Service-előfizetés az USA nyugati régiójában található. Ha más régiót használ, frissítse a értékét `uri` . A teljes listát lásd: [régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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
> A hitelesítéssel kapcsolatos további információkért lásd: [hitelesítés hozzáférési jogkivonattal](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

A következő szakaszban létrehozjuk a függvényt a szöveg-beszéd API meghívásához és a szintetizált beszéd válasz mentéséhez.

## <a name="make-a-request-and-save-the-response"></a>Kérelem elkészítése és a válasz mentése

Itt hozza létre a kérést a szöveg-beszéd API-hoz, és mentse a beszédfelismerési választ. Ez a példa feltételezi, hogy az USA nyugati végpontját használja. Ha az erőforrás egy másik régióban van regisztrálva, győződjön meg róla, hogy frissíti a-t `uri` . További információ: [Speech Service Regions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ezután hozzá kell adnia a kéréshez szükséges fejléceket. Győződjön meg arról, hogy `User-Agent` az erőforrás nevét (a Azure Portalban található) frissíti, és állítsa az `X-Microsoft-OutputFormat` előnyben részesített hang kimenetére. A kimeneti formátumok teljes listáját itt tekintheti meg: [hang kimenetek](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Ezután hozza létre a kérelem törzsét a Speech szintézis Markup Language (SSML) használatával. Ez a minta határozza meg a struktúrát, és a `text` korábban létrehozott bemenetet használja.

>[!NOTE]
> Ez a példa a `JessaRUS` hangbetűkészletet használja. A Microsoft által biztosított hangok és nyelvek teljes listájáért lásd a [nyelvi támogatást](language-support.md)ismertető témakört.
> Ha szeretne létrehozni egy egyedi, felismerhető hangot a márka számára, tekintse meg az [Egyéni hangbetűkészletek létrehozása](how-to-customize-voice-font.md)című témakört.

Végül egy kérést küld a szolgáltatásnak. Ha a kérelem sikeres, és a rendszer egy 200 állapotkódot ad vissza, a beszédfelismerési válasz a következő lesz: `TTSOutput.wav` .

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

Már majdnem kész. Az utolsó lépés egy aszinkron függvény létrehozása. Ez a függvény beolvassa az előfizetési kulcsot egy környezeti változóból, megkéri a szöveget, lekérdezi a jogkivonatot, megvárja a kérés befejeződését, majd átalakítja a szöveget a beszédre, és mentse a hangot. wav néven.

Ha nem ismeri a környezeti változókat, vagy inkább egy karakterláncként szeretné tesztelni az előfizetési kulcs hardcoded, cserélje le az `process.env.SPEECH_SERVICE_KEY` előfizetési kulcsot karakterláncként.

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

## <a name="run-the-sample-app"></a>A mintaalkalmazás futtatása

Ekkor készen áll a szöveg-beszéd minta alkalmazás futtatására. A parancssorból (vagy a terminál-munkamenetből) navigáljon a projekt könyvtárába, és futtassa a következő parancsot:

```console
node tts.js
```

Ha a rendszer kéri, írja be a szöveget, amit szeretne szöveggé konvertálni. Ha a művelet sikeres, a beszédfelismerési fájl a Project mappában található. Játssza le kedvenc médialejátszó használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ügyeljen arra, hogy eltávolítsa a mintául szolgáló alkalmazás forráskódjának bizalmas adatait, például az előfizetési kulcsokat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedjen meg Node.js mintákkal a GitHubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Lásd még

* [Szöveg – beszéd API-hivatkozás](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Egyéni hangbetűkészletek létrehozása](how-to-customize-voice-font.md)
* [Hangminták rögzítése egyéni hang létrehozásához](record-custom-voice-samples.md)
