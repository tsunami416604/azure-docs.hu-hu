---
title: 'Gyors útmutató: Szöveg-beszéd hangok listázása, Node. js-Speech szolgáltatás'
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
ms.openlocfilehash: 1044519110d8b0ae7b5a50860c8116d73b6b70bc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559384"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Gyors útmutató: A szöveg-beszéd hangok listájának beolvasása a Node. js használatával

Ebből a rövid útmutatóból megtudhatja, hogyan kérheti le a standard és a neurális hangok teljes listáját a Node. js-t használó régió/végpont számára. A rendszer JSON-ként adja vissza a listát, és a hang elérhetősége régiónként változik. A támogatott régiók listáját a [régiók](regions.md)című részben tekintheti meg.

Ehhez a rövid útmutatóhoz egy Speech Services-erőforrással rendelkező [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) -fiókra van szükség. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Node 8.12.x vagy újabb](https://nodejs.org/en/)
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével
* Egy Azure-előfizetési kulcs a Speech Serviceshez. [Szerezze be ingyen!](get-started.md)

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

A szöveg-hang transzformációs REST API egy hozzáférési jogkivonatot a hitelesítéshez szükséges. A hozzáférési jogkivonatot kapjon egy exchange szükség. Ez a függvény a Speech Services előfizetési kulcsát egy hozzáférési jogkivonat számára `issueToken` a végpont használatával cseréli le.

Ez a példa feltételezi, hogy a Speech Services-előfizetése az USA nyugati régiójában található. Ha egy másik régiót használ, módosítsa a `uri`. A teljes listát lásd: [régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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
> A hitelesítéssel kapcsolatos további információkért lásd: [hitelesítés hozzáférési](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)jogkivonattal.

A következő szakaszban létrehozjuk a függvényt a hangok listájának lekéréséhez és a JSON-kimenet fájlba mentéséhez.

## <a name="make-a-request-and-save-the-response"></a>Kérés és a válasz mentése

Itt fogja felépíteni a kérést, és menti a visszaadott hangok listáját. Ez a példa feltételezi, hogy az USA nyugati RÉGIÓJA végpont használata. Ha az erőforrás egy másik régióba regisztrálva van, ellenőrizze, hogy frissíti a `uri`. További információ: [Speech Services-régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ezután adja hozzá a kéréshez szükséges fejléceket. Végül paritásadatok egy kérelmet a szolgáltatáshoz. Ha a kérelem sikeres, és a rendszer egy 200 állapotkódot ad vissza, a válasz fájlba lesz írva.

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

Ha nem ismeri a környezeti változókat, vagy inkább egy karakterláncként szeretné tesztelni az előfizetési kulcs hardcoded, `process.env.SPEECH_SERVICE_KEY` cserélje le az előfizetési kulcsot karakterláncként.

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

Ekkor készen áll a minta alkalmazás futtatására. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ellenőrizze, hogy a bizalmas adatok eltávolítása a mintaalkalmazás forráskódja, például a előfizetési kulcsok.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Node. js-minták megismerése a GitHubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Lásd még

* [Szöveget beszéddé átalakító API-referencia](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Egyéni hangtípust létrehozása](how-to-customize-voice-font.md)
* [Hozzon létre egy egyéni beszédfelismerési rekord hangalapú minták](record-custom-voice-samples.md)
