---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: fd8b4ae06018de1d03ca60e836534a535c8f5df8
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "69906930"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a következő kódrészletet egy `get-languages.js` nevű fájlba a projektjében.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `npm install request uuidv4`.

Ezekre a modulokra a HTTP-kérelem és az `'X-ClientTraceId'` fejléc egyedi azonosítójának létrehozásához van szükség.

## <a name="set-the-endpoint"></a>A végpont beállítása

Ez a minta megpróbálja beolvasni a Translator Text végpontot egy környezeti változóból `TRANSLATOR_TEXT_ENDPOINT`:. Ha még nem ismeri a környezeti változókat, beállíthatja a `endpoint` sztringet, és megjegyzéssé teheti a feltételes utasítást.

```javascript
lorum ipsum
```

## <a name="configure-the-request"></a>A kérelem konfigurálása

A kérelemmodulon keresztül elérhető `request()` metódus lehetővé teszi a HTTP-metódus, az URL-cím, a kérelemparaméterek, a fejlécek és a JSON-törzs `options` objektumként való átadását. Az alábbi kódrészletben a kérelmet fogjuk konfigurálni:

>[!NOTE]
> A végpontokkal, az útvonalakkal és a kérelem-paraméterekkel kapcsolatos további információért lásd a [Translator Text API 3.0 által támogatott nyelvek lekérését](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) ismertető témakört.

```javascript
let options = {
    method: 'GET',
    baseUrl: endpoint,
    url: 'languages',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    json: true,
};
```

Ha Cognitive Services több szolgáltatásra kiterjedő előfizetést használ, akkor a kérés paramétereinek `Ocp-Apim-Subscription-Region` is szerepelnie kell. [További információ a többszolgáltatásos előfizetés hitelesítéséről](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

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
node get-languages.js
```

Ha szeretné összevetni a saját kódját a miénkkel, a teljes mintakódot megtekintheti a [GitHubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Mintaválasz

Keresse meg az ország/régió rövidítést ebben a [listában](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

A mintát csonkoltuk, hogy az eredményeknek csak egy részletét mutassa:

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem változtatható módon adta meg az előfizetői azonosítót a programban, a rövid útmutató befejezése után mindenképpen távolítsa el az előfizetői azonosítót.

## <a name="next-steps"></a>További lépések

Tekintse meg az API-referenciát, amely mindent megtudhat a Translator Text API.

> [!div class="nextstepaction"]
> [API-referenciák](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
