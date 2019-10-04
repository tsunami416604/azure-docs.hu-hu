---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 7f14a12d5de64206f64e8c7205beb2c59c4f1f2a
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906982"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a következő kódrészletet egy `get-languages.py` nevű fájlba a projektjében.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `pip install requests uuid`.

Az első megjegyzés arra utasítja a Python-értelmezőt, hogy UTF-8 kódolást használjon. A rendszer importálja azokat a modulokat, amelyek az előfizetői azonosító egy környezeti változóból való beolvasásához, a HTTP-kérelem felépítéséhez, egy egyedi azonosító létrehozásához, illetve a Translator Text API által visszaadott JSON-válasz kezeléséhez szükségesek.

## <a name="set-the-endpoint-and-path"></a>A végpont és az elérési út beállítása

Ez a minta megpróbálja beolvasni a Translator Text végpontot egy környezeti változóból `TRANSLATOR_TEXT_ENDPOINT`:. Ha még nem ismeri a környezeti változókat, beállíthatja a `endpoint` sztringet, és megjegyzéssé teheti a feltételes utasítást.

```python
endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

A Translator Text globális végpont beállítása `endpoint`. A `path` tulajdonság a `languages` útvonalat állítja be, és meghatározza, hogy a 3-as API-verziót szeretnénk használni.

>[!NOTE]
> További információ a végpontokról, az útvonalakról és a kérelmek paraméteréről [: Translator Text API 3,0: Nyelvek](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```python
path = '/languages?api-version=3.0'
constructed_url = endpoint + path
```

## <a name="add-headers"></a>Fejlécek hozzáadása

A támogatott nyelvek beszerzésére irányuló kérelem nem igényel hitelesítést. Állítsa be `Content-type` a `application/json` as és `X-ClientTraceId` a Hozzáadás lehetőséget a kérelem egyedi azonosításához.

Másolja a projektbe a következő kódrészletet:

```python
headers = {
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Ha Cognitive Services több szolgáltatásra kiterjedő előfizetést használ, akkor a kérés paramétereinek `Ocp-Apim-Subscription-Region` is szerepelnie kell. [További információ a többszolgáltatásos előfizetés hitelesítéséről](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-get-a-list-of-supported-languages"></a>Kérelem létrehozása a támogatott nyelvek listájának lekérésére

Hozzunk létre egy GET kérelmet a `requests` modullal. Ez két argumentumot fogad: az összefűzött URL-címet és a kérelemfejléceket:

```python
request = requests.get(constructed_url, headers=headers)
response = request.json()
```

## <a name="print-the-response"></a>A válasz megjelenítése

Az utolsó lépés az eredmények kiírása. Ez a kódrészlet szebbé teszi az eredményeket a kulcsok rendezésével, a behúzás beállításával és az elem- és kulcselválasztók meghatározásával.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Ezzel összeállított egy egyszerű programot, amely meghívja a Translator Text API-t, és visszaad egy JSON-választ. Most itt az ideje, hogy futtassa a programot:

```console
python get-languages.py
```

Ha szeretné összevetni a saját kódját a miénkkel, a teljes mintakódot megtekintheti a [GitHubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

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
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
