---
title: 'Gyors útmutató: Első Python - támogatott nyelvek listáját a Translator Text API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban lekéri a fordításhoz, átíráshoz és szótárban való kereséshez támogatott nyelvek, valamint példák listáját a Translator Text API és a Python használatával.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 705bc49cef13ae80d648ca59b50021250dede3bd
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736770"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-python"></a>Gyors útmutató: A Python használatával támogatott nyelvek listáját a Translator Text API használatával

Ebből a rövid útmutatóból megtudhatja, hogyan kérheti le a támogatott nyelvek listáját egy GET kérelemmel a Python és a Translator Text REST API használatával.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Python 2.7.x vagy 3.x

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a következő kódrészletet egy `get-languages.py` nevű fájlba a projektjében.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `pip install requests uuid`.

Az első megjegyzés arra utasítja a Python-értelmezőt, hogy UTF-8 kódolást használjon. A rendszer importálja azokat a modulokat, amelyek az előfizetői azonosító egy környezeti változóból való beolvasásához, a HTTP-kérelem felépítéséhez, egy egyedi azonosító létrehozásához, illetve a Translator Text API által visszaadott JSON-válasz kezeléséhez szükségesek.

## <a name="set-the-base-url-and-path"></a>Állítsa be a kiindulási URL-címét, és elérési útja

A Translator Text globális végpontja van beállítva, a `base_url`. A `path` tulajdonság a `languages` útvonalat állítja be, és meghatározza, hogy a 3-as API-verziót szeretnénk használni.

>[!NOTE]
> Végpontok, útvonalak és a kérelem paramétereinek kapcsolatos további információkért lásd: [Translator Text API 3.0: Nyelvek](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'
constructed_url = base_url + path
```

## <a name="add-headers"></a>Fejlécek hozzáadása

A támogatott nyelvek beolvasására irányuló kérelem nem igényel hitelesítést. Állítsa be a `Content-type` , `application/json` , és adja hozzá `X-ClientTraceId` egyedi azonosítására szolgál a kérést.

Másolja a projektbe a következő kódrészletet:

```python
headers = {
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

Ismerje meg, hogyan használható a Translator Text API-t:

* [Szöveg lefordítása](quickstart-python-translate.md)
* [Szöveg átírása](quickstart-python-transliterate.md)
* [A beviteli nyelv azonosítása](quickstart-python-detect.md)
* [Alternatív fordítások beolvasása](quickstart-python-dictionary.md)
* [Bemenet mondatai hosszának meghatározása](quickstart-python-sentences.md)
