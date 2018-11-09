---
title: 'Rövid útmutató: Támogatott nyelvek listájának lekérése, Node.js – Translator Text API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban lekéri a fordításhoz, átíráshoz és szótárban való kereséshez támogatott nyelvek, valamint példák listáját a Translator Text API és Node.js segítségével.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 10/29/2018
ms.author: erhopf
ms.openlocfilehash: 2d87173384a171d6b96ffa52c1d0bf80069a34c2
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50250665"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-with-nodejs"></a>Rövid útmutató: A Translator Text API használata a támogatott nyelvek listájának lekéréséhez a Node.js-sel

Ebből a rövid útmutatóból megtudhatja, hogyan kérheti le a támogatott nyelvek listáját egy GET kérelemmel a Node.js és a Translator Text REST API használatával.

Ehhez a rövid útmutatóhoz szükség van egy [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely tartalmaz egy Translator Text-erőforrást. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Node 8.12.x vagy újabb](https://nodejs.org/en/)
* Egy Azure-előfizetői azonosító a Translator Text szolgáltatáshoz

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a következő kódrészletet egy `get-languages.js` nevű fájlba a projektjében.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `npm install request uuidv4`.

Ezekre a modulokra a HTTP-kérelem és az `'X-ClientTraceId'` fejléc egyedi azonosítójának létrehozásához van szükség.

## <a name="set-the-subscription-key"></a>Az előfizetői azonosító megadása

Ez a kód megpróbálja beolvasni a Translator Text előfizetői azonosítóját a `TRANSLATOR_TEXT_KEY` környezeti változóból. Ha még nem ismeri a környezeti változókat, beállíthatja a `subscriptionKey` sztringet, és megjegyzéssé teheti a feltételes utasítást.

Másolja a projektbe a következő kódot:

```javascript
/* Checks to see if the subscription key is available
as an environment variable. If you are setting your subscription key as a
string, then comment these lines out.

If you want to set your subscription key as a string, replace the value for
the Ocp-Apim-Subscription-Key header as a string. */
const subscriptionKey = process.env.TRANSLATOR_TEXT_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};
```

## <a name="configure-the-request"></a>A kérelem konfigurálása

A kérelemmodulon keresztül elérhető `request()` metódus lehetővé teszi a HTTP-metódus, az URL-cím, a kérelemparaméterek, a fejlécek és a JSON-törzs `options` objektumként való átadását. Az alábbi kódrészletben a kérelmet fogjuk konfigurálni:

>[!NOTE]
> A végpontokkal, az útvonalakkal és a kérelem-paraméterekkel kapcsolatos további információért lásd a [Translator Text API 3.0 által támogatott nyelvek lekérését](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) ismertető témakört.

```javascript
let options = {
    method: 'GET',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'languages',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    json: true,
};
```

### <a name="authentication"></a>Hitelesítés

A kérelmek hitelesítésének legegyszerűbb módja az, hogy átadja az előfizetői azonosítót `Ocp-Apim-Subscription-Key` fejlécként, amit ebben a példában alkalmazunk. Alternatív megoldásként hozzáférési jogkivonatra cserélheti az előfizetői azonosítóját, és átadhatja a hozzáférési jogkivonatot is `Authorization` fejlécként a kérelem ellenőrzése céljából. További információért lásd: [Hitelesítés](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

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

> [!div class="nextstepaction"]
> [A Node.js-példák megismerése a GitHubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)

## <a name="see-also"></a>Lásd még

Megtudhatja, hogyan használhatja a Translator Text API-t a nyelvfelismerésen kívül a következőkhöz:

* [Szöveg lefordítása](quickstart-nodejs-translate.md)
* [Szöveg átírása](quickstart-nodejs-transliterate.md)
* [A beviteli nyelv azonosítása](quickstart-nodejs-detect.md)
* [Alternatív fordítások beolvasása](quickstart-nodejs-dictionary.md)
* [Bemenet mondatai hosszának meghatározása](quickstart-nodejs-sentences.md)
