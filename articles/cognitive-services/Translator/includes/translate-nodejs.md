---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: db48917bfb219a85dcaf0075d0dc7072858a9e27
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "69906679"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új projektet a kedvenc IDE vagy szerkesztőjével, vagy egy új mappát, amely `translate-text.js` egy nevű fájlt használ az asztalon. Ezután másolja a kódrészletet a projektbe vagy fájlba:

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `npm install request uuidv4`.

Ezekre a modulokra a HTTP-kérelem és az `'X-ClientTraceId'` fejléc egyedi azonosítójának létrehozásához van szükség.

## <a name="set-the-subscription-key-and-endpoint"></a>Az előfizetési kulcs és a végpont beállítása

Ez a minta megpróbálja beolvasni a Translator Text előfizetési kulcsot és a végpontot ezekből `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` a `TRANSLATOR_TEXT_ENDPOINT`környezeti változókból: és. Ha nem ismeri a környezeti változókat, beállíthatja `subscriptionKey` és `endpoint` karakterláncként is megadhatja a feltételes utasításokat, és megjegyzéseket fűzhet hozzájuk.

Másolja a projektbe a következő kódot:

```javascript
var key_var = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('Please set/export the following environment variable: ' + key_var);
}
var subscriptionKey = process.env[key_var];
var endpoint_var = 'TRANSLATOR_TEXT_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('Please set/export the following environment variable: ' + endpoint_var);
}
var endpoint = process.env[endpoint_var];
```

## <a name="configure-the-request"></a>A kérelem konfigurálása

A kérelemmodulon keresztül elérhető `request()` metódus lehetővé teszi a HTTP-metódus, az URL-cím, a kérelemparaméterek, a fejlécek és a JSON-törzs `options` objektumként való átadását. Az alábbi kódrészletben a kérelmet fogjuk konfigurálni:

>[!NOTE]
> A végpontokkal, az útvonalakkal és a kérelemparaméterekkel kapcsolatos további információért lásd: [Translator Text API 3.0: Translate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate).

```javascript
let options = {
    method: 'POST',
    baseUrl: endpoint,
    url: 'translate',
    qs: {
      'api-version': '3.0',
      'to': ['de', 'it']
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Hello World!'
    }],
    json: true,
};
```

A kérelmek hitelesítésének legegyszerűbb módja az, hogy átadja az előfizetői azonosítót `Ocp-Apim-Subscription-Key` fejlécként, amit ebben a példában alkalmazunk. Alternatív megoldásként hozzáférési jogkivonatra cserélheti az előfizetői azonosítóját, és átadhatja a hozzáférési jogkivonatot is `Authorization` fejlécként a kérelem ellenőrzése céljából.

Ha Cognitive Services több szolgáltatást használó előfizetést használ, a `Ocp-Apim-Subscription-Region` kérések fejlécében is szerepelnie kell.

További információért lásd: [Hitelesítés](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="make-the-request-and-print-the-response"></a>A kérelem indítása és a válasz megjelenítése

A következő lépésben létrehozzuk a kérelmet a `request()` metódussal. Ez a metódus első argumentumként az előző szakaszban létrehozott `options` objektumot használja, majd megjeleníti a formázott JSON-választ.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> Ebben a példában az `options` objektumban határozzuk meg a HTTP-kérelmet, de a kérelemmodul az egyszerűsített metódusok (például a `.post` vagy a `.get`) használatát is támogatja. További információkért lásd [az egyszerűsített metódusokat ismertető szakaszt](https://github.com/request/request#convenience-methods).

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Ezzel összeállított egy egyszerű programot, amely meghívja a Translator Text API-t, és visszaad egy JSON-választ. Most itt az ideje, hogy futtassa a programot:

```console
node translate-text.js
```

Ha szeretné összevetni a saját kódját a miénkkel, a teljes mintakódot megtekintheti a [GitHubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Mintaválasz

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem változtatható módon adta meg az előfizetői azonosítót a programban, a rövid útmutató befejezése után mindenképpen távolítsa el az előfizetői azonosítót.

## <a name="next-steps"></a>További lépések

Tekintse meg az API-referenciát, amely mindent megtudhat a Translator Text API.

> [!div class="nextstepaction"]
> [API-referenciák](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
