---
title: Szándék a REST-hívással a Pythonban
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 23ac98f91c989c9bedb6b91e6a7ce26dc164ac5a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987775"
---
## <a name="prerequisites"></a>Előfeltételek

* [Python 3.6-os](https://www.python.org/downloads/) vagy újabb.
* [Visual Studio kód](https://code.visualstudio.com/)
* Nyilvános alkalmazásazonosító:`df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>LUIS-futásidejű kulcs létrehozása előrejelzésekhez

1. Bejelentkezés az [Azure-portálra](https://portal.azure.com)
1. Kattintson [a **Nyelvismertetés létrehozása gombra.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Adja meg a **Runtime** kulcs összes szükséges beállítását:

    |Beállítás|Érték|
    |--|--|
    |Név|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Hely|Válassza ki a közeli és elérhető helyeket|
    |Tarifacsomag|`F0`- a minimális tarifaszint|
    |Erőforráscsoport|Elérhető erőforráscsoport kiválasztása|

1. Kattintson a **Létrehozás gombra,** és várja meg az erőforrás létrehozását. Létrehozása után keresse meg az erőforráslapot.
1. Gyűjtse `endpoint` össze `key`a konfigurált és a.

## <a name="get-intent-from-the-prediction-endpoint"></a>Leképezés az előrejelzési végpontról

A Python használatával lekérdezheti az [előrejelzési végpontot,](https://aka.ms/luis-apim-v3-prediction) és előrejelzési eredményt kaphat.

1. Másolja ezt a kódrészletet `predict.py`egy fájlba, amelynek neve:

    ```python
    ########### Python 3.6 #############
    import requests

    try:

        key = 'YOUR-KEY' # your Runtime key
        endpoint = 'YOUR-ENDPOINT' # such as 'your-resource-name.api.cognitive.microsoft.com'
        appId = 'df67dcdb-c37d-46af-88e1-8b97951ca1c2'
        utterance = 'turn on all lights'

        headers = {
        }

        params ={
            'query': utterance,
            'timezoneOffset': '0',
            'verbose': 'true',
            'show-all-intents': 'true',
            'spellCheck': 'false',
            'staging': 'false',
            'subscription-key': key
        }

        r = requests.get(f'https://{endpoint}/luis/prediction/v3.0/apps/{appId}/slots/production/predict',headers=headers, params=params)
        print(r.json())

    except Exception as e:
        print(f'{e}')
    ```

1. Cserélje `YOUR-KEY` le `YOUR-ENDPOINT` a és az értékeket a saját **előrejelzési Runtime** kulcs és végpont.

    |Információ|Cél|
    |--|--|
    |`YOUR-KEY`|A 32 karakterből álló **előrejelzési Runtime** kulcs.|
    |`YOUR-ENDPOINT`| Az előrejelzési URL-végpont. Például: `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Telepítse `requests` a függőséget. Http-kérelmek et lehet letenni:

    ```console
    pip install requests
    ```

1. Futtassa a parancsfájlt ezzel a konzolparaléval:

    ```console
    python predict.py
    ```

1. Tekintse át az előrejelzési választ, amely JSON-ként ad vissza:

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Itt a JSON válasz formázott olvashatóság:

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte ezt a rövid útmutatót, törölje a fájlt a fájlrendszerből.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kimondott szöveg hozzáadása és betanítása](../get-started-get-model-rest-apis.md)