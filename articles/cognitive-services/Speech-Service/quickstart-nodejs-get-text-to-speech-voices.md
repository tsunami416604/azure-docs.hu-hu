---
title: 'Gyors útmutató: szöveg-beszéd hangok listázása, Node. js – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan kérheti le a standard és a neurális hangok teljes listáját a Node. js-t használó régió/végpont számára. A rendszer JSON-ként adja vissza a listát, és a hang elérhetősége régiónként változik.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bd063102a4c0cbdc4b62df027e643b117b412278
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812858"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Rövid útmutató: a szöveg-beszéd hangok listájának lekérése a Node. js használatával

Ebből a rövid útmutatóból megtudhatja, hogyan kérheti le a standard és a neurális hangok teljes listáját a Node. js-t használó régió/végpont számára. A rendszer JSON-ként adja vissza a listát, és a hang elérhetősége régiónként változik. A támogatott régiók listáját a [régiók](regions.md)című részben tekintheti meg.

Ehhez a rövid útmutatóhoz egy Speech Service-erőforrással rendelkező [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) van szükség. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Node 8.12.x vagy újabb](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)vagy a kedvenc szövegszerkesztője
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be ingyen!](get-started.md)

## <a name="create-a-project-and-require-dependencies"></a>Projekt létrehozása és függőségek megkövetelése

Hozzon létre egy új Node. js-projektet a kedvenc IDE vagy szerkesztő használatával. Ezután másolja a következő kódrészletet egy `get-voices.js` nevű fájlba a projektjében.

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

A szöveg-beszéd REST APIhoz hozzáférési jogkivonat szükséges a hitelesítéshez. Hozzáférési jogkivonat lekéréséhez Exchange szükséges. Ez a függvény az `issueToken`-végpont használatával cseréli le egy hozzáférési tokenhez tartozó beszédfelismerési szolgáltatás előfizetési kulcsát.

Ez a példa azt feltételezi, hogy a Speech Service-előfizetés az USA nyugati régiójában található. Ha más régiót használ, frissítse a `uri`értékét. A teljes listát lásd: [régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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

A következő szakaszban létrehozjuk a függvényt a hangok listájának lekéréséhez és a JSON-kimenet fájlba mentéséhez.

## <a name="make-a-request-and-save-the-response"></a>Kérelem elkészítése és a válasz mentése

Itt fogja felépíteni a kérést, és menti a visszaadott hangok listáját. Ez a példa feltételezi, hogy az USA nyugati végpontját használja. Ha az erőforrás egy másik régióban van regisztrálva, ellenőrizze, hogy a `uri`frissíti-e. További információ: [Speech Service Regions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ezután adja hozzá a kéréshez szükséges fejléceket. Végül egy kérést küld a szolgáltatásnak. Ha a kérelem sikeres, és a rendszer egy 200 állapotkódot ad vissza, a válasz fájlba lesz írva.

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

Már majdnem kész. Az utolsó lépés egy aszinkron függvény létrehozása. Ez a függvény beolvassa az előfizetési kulcsot egy környezeti változóból, lekérdezi a jogkivonatot, várja meg, amíg befejeződik a kérelem, majd a JSON-választ írja a fájlra.

Ha nem ismeri a környezeti változókat, vagy inkább az előfizetési kulcs hardcoded használja karakterláncként, cserélje le a `process.env.SPEECH_SERVICE_KEY`t az előfizetési kulcsra karakterláncként.

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

Ekkor készen áll a minta alkalmazás futtatására. A parancssorból (vagy a terminál-munkamenetből) navigáljon a projekt könyvtárába, és futtassa a következő parancsot:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ügyeljen arra, hogy eltávolítsa a mintául szolgáló alkalmazás forráskódjának bizalmas adatait, például az előfizetési kulcsokat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A Node. js-minták megismerése a GitHubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Lásd még:

* [Szöveget beszéddé átalakító API-referencia](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Egyéni hangbetűkészletek létrehozása](how-to-customize-voice-font.md)
* [Hangminták rögzítése egyéni hang létrehozásához](record-custom-voice-samples.md)
