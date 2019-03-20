---
title: 'Gyors útmutató: Szöveg-beszéd átalakítás, node.js-szel – beszédszolgáltatások konvertálása'
titleSuffix: Azure Cognitive Services
description: Ebből a gyorsútmutatóból megtudhatja, hogyan átalakítandó szöveg-hang transzformációs Node.js és a szöveg-hang transzformációs REST API használatával. A jelen útmutatóban szereplő minta szöveg van felépítve, beszéd összefoglaló Markup Language (SSML). Ez lehetővé teszi, hogy válassza ki a beszédfelismerési és nyelvi beszédfelismerési válasz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: eff79b31e926d382d98416b585ca2af54b3e87d0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57872642"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Gyors útmutató: Átalakítás szöveg-hang transzformációs Node.js használatával

Ebből a gyorsútmutatóból megtudhatja, hogyan átalakítandó szöveg-hang transzformációs Node.js és a szöveg-hang transzformációs REST API használatával. Ebben az útmutatóban a kérelem törzsében van strukturálva, [Speech összefoglaló Markup Language (SSML)](speech-synthesis-markup.md), amely lehetővé teszi, hogy a válasz nyelv és válassza ki.

Ez a rövid útmutatóhoz egy [Azure Cognitive Services-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) beszédszolgáltatások erőforrással. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Node 8.12.x vagy újabb](https://nodejs.org/en/)
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével
* Egy Azure-előfizetést a beszédszolgáltatások kulcs. [Itt igényelhet ingyenesen! ](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Hozzon létre egy projektet, és a szükséges függőségek

Hozzon létre egy új Node.js-projektet, a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével használatával. Ezután másolja a következő kódrészletet egy `tts.js` nevű fájlba a projektjében.

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

A szöveg-hang transzformációs REST API egy hozzáférési jogkivonatot a hitelesítéshez szükséges. A hozzáférési jogkivonatot kapjon egy exchange szükség. Ez a függvény kicserél egy hozzáférési token használatával beszédszolgáltatások előfizetői azonosítóját a `issueToken` végpont.

Ez a minta azt feltételezi, hogy a Speech Services-előfizetés az USA nyugati régiójában. Ha egy másik régiót használ, módosítsa a `uri`. A teljes listát lásd: [régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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
> A hitelesítés további információkért lásd: [hitelesítés hozzáférési jogkivonatot a](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

A következő szakaszban a függvény, amely az szöveg-hang transzformációs API-t, és mentse a szintetizált válasz hozunk létre.

## <a name="make-a-request-and-save-the-response"></a>Kérés és a válasz mentése

Ide fog a kérést a szöveg-hang transzformációs API és a speech válasz mentése. Ez a példa feltételezi, hogy az USA nyugati RÉGIÓJA végpont használata. Ha az erőforrás egy másik régióba regisztrálva van, ellenőrizze, hogy frissíti a `uri`. További információkért lásd: [beszédszolgáltatások régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Következő lépésként hozzá kell a kéréshez szükséges fejlécek. Győződjön meg arról, hogy frissítenie `User-Agent` az erőforrás (az Azure Portalon található), és a készlet nevére `X-Microsoft-OutputFormat` , az előnyben részesített hangkimeneti. Kimeneti formátumok teljes listáját lásd: [hang kimenete](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs).

Ezután hozhat létre a kérelem törzsében Speech összefoglaló Markup Language (SSML) használatával. Ez a példa a struktúrát határozza meg, és használja a `text` adjon meg a korábban létrehozott.

>[!NOTE]
> Ebben a példában a `JessaRUS` hangtípusú. A Microsoft teljes listáját adott beszédhangot/nyelvek, lásd: [nyelvi támogatás](language-support.md).
> Ha érdekli a saját márkáját egy egyedi, könnyen felismerhető névre hangalapú létrehozása, [létrehozása egyéni hangtípust](how-to-customize-voice-font.md).

Végül paritásadatok egy kérelmet a szolgáltatáshoz. Ha a kérelem sikeres, és egy 200 állapotkódot adott vissza, a beszéd válasz kiírt `TTSOutput.wav`.

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

Már majdnem kész. Az utolsó lépés, hogy hozzon létre egy aszinkron függvényt. Ez a függvény az előfizetési kulcs fogja beolvasni egy környezeti változóból, Rákérdezés a szöveg, egy token beszerzéséhez, várja meg a kérés befejezéséhez, majd a szöveg-beszéd átalakítás, és mentse a hanganyag egy .wav.

Ha még nem használta a környezeti változókat, vagy inkább az előfizetési kulcs szoftveresen kötött karakterláncként tesztelése, cserélje le a `process.env.SPEECH_SERVICE_KEY` karakterláncként az előfizetési kulccsal végzett.

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

Ennyi az egész, készen áll a szöveg-hang transzformációs mintaalkalmazás futtatásához. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
node tts.js
```

Amikor a rendszer kéri, írjon be bármilyen kívánt szöveg-hang transzformációs konvertálása. Ha ez sikeres, a beszéd fájl található a projektmappába. A kedvenc media player használatával lejátszani.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ellenőrizze, hogy a bizalmas adatok eltávolítása a mintaalkalmazás forráskódja, például a előfizetési kulcsok.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a Node.js-minták a Githubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Lásd még

* [Szöveget beszéddé átalakító API-referencia](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [Egyéni hangtípust létrehozása](how-to-customize-voice-font.md)
* [Hozzon létre egy egyéni beszédfelismerési rekord hangalapú minták](record-custom-voice-samples.md)
