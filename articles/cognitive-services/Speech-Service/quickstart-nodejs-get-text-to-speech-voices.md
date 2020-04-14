---
title: 'Rövid útmutató: Szövegfelolvasó hangok listázása, Node.js - Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban megtudhatja, hogyan szerezheti le egy régió/végpont standard és neurális hangok teljes listáját a Node.js használatával. A lista JSON-ként jelenik meg, és a hang elérhetősége régiónként változik.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 9fe8bc06aafd17518d37c35034fac9b566e079ce
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261550"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Rövid útmutató: A szövegfelolvasó hangok listájának beolvasása a Node.js használatával

Ebben a rövid útmutatóban megtudhatja, hogyan szerezheti le egy régió/végpont standard és neurális hangok teljes listáját a Node.js használatával. A lista JSON-ként jelenik meg, és a hang elérhetősége régiónként változik. A támogatott régiók listáját a Régiók című [témakörben található.](regions.md)

Ez a rövid útmutató egy [Azure Cognitive Services-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) igényel egy beszédszolgáltatási erőforrással. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* <a href="https://nodejs.org/en/" target="_blank">Node 8.12.x vagy újabb<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>vagy kedvenc szövegszerkesztő
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Kap egy részére szabad!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Projekt létrehozása és függőségek megkövetelése

Hozzon létre egy új Node.js projektsegítségével a kedvenc IDE vagy szerkesztő. Ezután másolja a következő kódrészletet egy `get-voices.js` nevű fájlba a projektjében.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `npm install request request-promise`.

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

A következő szakaszban létrehozunk egy függvényt a hangok listájának leéséhez, és a JSON kimenetet fájlba mentjük.

## <a name="make-a-request-and-save-the-response"></a>Kérés kérése és mentése

Itt fogja felépíteni a kérelmet, és mentse a visszaküldött hangok listáját. Ez a minta feltételezi, hogy a nyugat-amerikai végpontot használja. Ha az erőforrás egy másik régióban van `uri`regisztrálva, győződjön meg arról, hogy frissíti a . További információt a [Beszédszolgáltatási területek című témakörben talál.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)

Ezután adja hozzá a szükséges fejléceket a kérelemhez. Végül, akkor, hogy egy kérelmet a szolgáltatás. Ha a kérelem sikeres, és egy 200-as állapotkódot ad vissza, a válasz fájlba kerül.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Már majdnem kész. Az utolsó lépés egy aszinkron függvény létrehozása. Ez a függvény egy környezeti változóból olvassa be az előfizetési kulcsot, lekérést kap, megvárja a kérés befejezését, majd írja be a JSON-választ a fájlba.

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
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ez az, készen áll a mintaalkalmazás futtatására. A parancssorból (vagy terminálmunkamenetből) keresse meg a projektkönyvtárat, és futtassa a következőket:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Győződjön meg arról, hogy eltávolít minden bizalmas információt a mintaalkalmazás forráskódjából, például az előfizetési kulcsokból.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Node.js minták felfedezése a GitHubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Lásd még

* [Szövegfelolvasó API-hivatkozások](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Egyéni hangbetűtípusok létrehozása](how-to-customize-voice-font.md)
* [Hangminták rögzítése egyéni hang létrehozásához](record-custom-voice-samples.md)
