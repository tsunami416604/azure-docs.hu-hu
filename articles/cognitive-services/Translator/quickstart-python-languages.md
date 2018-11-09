---
title: 'Rövid útmutató: Támogatott nyelvek lekérése, Python – Translator Text API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban lekéri a fordításhoz, átíráshoz és szótárban való kereséshez támogatott nyelvek, valamint példák listáját a Translator Text API és a Python használatával.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 10/29/2018
ms.author: erhopf
ms.openlocfilehash: 4c261a46b96865900a92bc53e4973fa3df094289
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50246558"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-python"></a>Rövid útmutató: A Translator Text API használata a támogatott nyelvek listájának lekéréséhez a Pythonnal

Ebből a rövid útmutatóból megtudhatja, hogyan kérheti le a támogatott nyelvek listáját egy GET kérelemmel a Python és a Translator Text REST API használatával.

Ehhez a rövid útmutatóhoz szükség van egy [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely tartalmaz egy Translator Text-erőforrást. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Python 2.7.x vagy 3.x
* Egy Azure-előfizetői azonosító a Translator Text szolgáltatáshoz

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a következő kódrészletet egy `get-languages.py` nevű fájlba a projektjében.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `pip install requests uuid`.

Az első megjegyzés arra utasítja a Python-értelmezőt, hogy UTF-8 kódolást használjon. A rendszer importálja azokat a modulokat, amelyek az előfizetői azonosító egy környezeti változóból való beolvasásához, a HTTP-kérelem felépítéséhez, egy egyedi azonosító létrehozásához, illetve a Translator Text API által visszaadott JSON-válasz kezeléséhez szükségesek.

## <a name="set-the-subscription-key-base-url-and-path"></a>Az előfizetői azonosító, az alap URL-cím és az elérési út beállítása

Ez a minta megpróbálja beolvasni a Translator Text-előfizetői azonosítót a `TRANSLATOR_TEXT_KEY` környezeti változóból. Ha még nem ismeri a környezeti változókat, beállíthatja a `subscriptionKey` sztringet, és megjegyzéssé teheti a feltételes utasítást.

Másolja a projektbe a következő kódot:

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

Jelenleg egy végpont érhető el a Translator Text szolgáltatáshoz, és ez van beállítva `base_url` tulajdonságként. A `path` tulajdonság a `languages` útvonalat állítja be, és meghatározza, hogy a 3-as API-verziót szeretnénk használni.

>[!NOTE]
> A végpontokkal, az útvonalakkal és a kérelem-paraméterekkel kapcsolatos további információért lásd a [Translator Text API 3.0 által támogatott nyelvek lekérését](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) ismertető témakört.

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'
constructed_url = base_url + path
```

## <a name="add-headers"></a>Fejlécek hozzáadása

A kérelmek hitelesítésének legegyszerűbb módja az, hogy átadja az előfizetői azonosítót `Ocp-Apim-Subscription-Key` fejlécként, amit ebben a példában alkalmazunk. Alternatív megoldásként hozzáférési jogkivonatra cserélheti az előfizetői azonosítóját, és átadhatja a hozzáférési jogkivonatot is `Authorization` fejlécként a kérelem ellenőrzése céljából. További információért lásd: [Hitelesítés](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Másolja a projektbe a következő kódrészletet:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

## <a name="create-a-request-to-get-a-list-of-supported-languages"></a>Kérelem létrehozása a támogatott nyelvek listájának lekérésére

Hozzunk létre egy GET kérelmet a `requests` modullal. Ez két argumentumot fogad: az összefűzött URL-címet és a kérelemfejléceket:

```python
request = requests.get(constructed_url, headers=headers)
response = request.json()
```

## <a name="print-the-response"></a>A válasz megjelenítése

Az utolsó lépés az eredmények kiírása. Ez a kódrészlet szebbé teszi az eredményeket a kulcsok rendezésével, a behúzás beállításával és az elem- és kulcselválasztók meghatározásával.

```python
print(json.dumps(response, sort_keys=True, indent=4, ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Ezzel összeállított egy egyszerű programot, amely meghívja a Translator Text API-t, és visszaad egy JSON-választ. Most itt az ideje, hogy futtassa a programot:

```console
python get-languages.py
```

Ha szeretné összevetni a saját kódját a miénkkel, a teljes mintakódot megtekintheti a [GitHubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

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
> [A Python-példák megismerése a GitHubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)

## <a name="see-also"></a>Lásd még

Megtudhatja, hogyan használhatja a Translator Text API-t a szövegátíráson kívül a következőkhöz:

* [Szöveg lefordítása](quickstart-python-translate.md)
* [Szöveg átírása](quickstart-python-transliterate.md)
* [A beviteli nyelv azonosítása](quickstart-python-detect.md)
* [Alternatív fordítások beolvasása](quickstart-python-dictionary.md)
* [Bemenet mondatai hosszának meghatározása](quickstart-python-sentences.md)
