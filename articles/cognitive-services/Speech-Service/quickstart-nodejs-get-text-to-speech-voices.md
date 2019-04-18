---
title: 'Gyors útmutató: Szöveg-hang transzformációs beszédhangot, node.js-szel – beszédszolgáltatások listázása'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban megismerheti, hogyan beolvasni a standard és a Neurális beszédhangot teljes listáját a régió/végpont Node.js használatával fogjuk. A listát ad vissza JSON-fájlként, és a hangalapú rendelkezésre állása régiónként eltérő.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: erhopf
ms.openlocfilehash: 62187ddbe587a81038f8424b079e3c0c313d1ae2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58887097"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Gyors útmutató: Szöveg-hang transzformációs beszédhangot Node.js használatával listájának beolvasása

Ebben a rövid útmutatóban megismerheti, hogyan beolvasni a standard és a Neurális beszédhangot teljes listáját a régió/végpont Node.js használatával fogjuk. A listát ad vissza JSON-fájlként, és a hangalapú rendelkezésre állása régiónként eltérő. Támogatott régiók listáját lásd: [régiók](regions.md).

Ez a rövid útmutatóhoz egy [Azure Cognitive Services-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) beszédszolgáltatások erőforrással. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Node 8.12.x vagy újabb](https://nodejs.org/en/)
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével
* Egy Azure-előfizetést a beszédszolgáltatások kulcs. [Itt igényelhet ingyenesen! ](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Hozzon létre egy projektet, és a szükséges függőségek

Hozzon létre egy új Node.js-projektet, a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével használatával. Ezután másolja a következő kódrészletet egy `get-voices.js` nevű fájlba a projektjében.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languagesto a file
const fs = require('fs');
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `npm install request request-promise`.

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

A következő szakaszban a függvény, amely beszédhangot listájának, és mentse a JSON-kimenet hozunk létre.

## <a name="make-a-request-and-save-the-response"></a>Kérés és a válasz mentése

Ide fog hozhat létre a kérést, és mentse a visszaadott beszédhangot listáját. Ez a példa feltételezi, hogy az USA nyugati RÉGIÓJA végpont használata. Ha az erőforrás egy másik régióba regisztrálva van, ellenőrizze, hogy frissíti a `uri`. További információkért lásd: [beszédszolgáltatások régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ezután adja hozzá a kéréshez szükséges fejlécek. Végül paritásadatok egy kérelmet a szolgáltatáshoz. A kérelem sikeres, és egy 200 állapotkódot adott vissza, ha a válasz írt fájlt.

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

Már majdnem kész. Az utolsó lépés, hogy hozzon létre egy aszinkron függvényt. Ez a függvény fog olvassa el az előfizetési kulcs egy környezeti változóból, egy token beszerzéséhez, várjon, amíg a kérelem végrehajtásához, majd a JSON-válasz fájlba írni.

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

Ennyi az egész, készen áll a mintaalkalmazás futtatásához. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ellenőrizze, hogy a bizalmas adatok eltávolítása a mintaalkalmazás forráskódja, például a előfizetési kulcsok.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a Node.js-minták a Githubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Lásd még

* [Szöveget beszéddé átalakító API-referencia](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Egyéni hangtípust létrehozása](how-to-customize-voice-font.md)
* [Hozzon létre egy egyéni beszédfelismerési rekord hangalapú minták](record-custom-voice-samples.md)
