---
title: 'Gyors útmutató: Bevételezési adatok kinyerése a Python-Form felismerő használatával'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az űrlap-felismerő REST API és a Python használatával gyűjti össze az adatokból az értékesítési nyugták lemezképeit.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: e3c5583f38f7a7f5a3654bfdd27620593175cf58
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562664"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Gyors útmutató: Bevételezési adatok kinyerése az űrlap-felismerő REST API és a Python használatával

Ebben a rövid útmutatóban az Azure űrlap-felismerő REST API a Python használatával kinyerheti és azonosíthatja a kapcsolódó információkat az értékesítési nyugtákban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez a következőket kell tennie:
- Hozzáférés az űrlap-felismerő korlátozott hozzáférésének előzetes verziójához. Az előzetes verzió eléréséhez töltse ki és küldje el az [űrlap-felismerő hozzáférési kérelmének](https://aka.ms/FormRecognizerRequestAccess) űrlapját.
- [Python](https://www.python.org/downloads/) telepítve (ha helyileg szeretné futtatni a mintát).
- Egy nyugtát ábrázoló rendszerkép URL-címe. Ehhez a rövid útmutatóhoz [minta képet](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) is használhat.

## <a name="create-a-form-recognizer-resource"></a>Űrlap-felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Visszaigazolás elemzése

A visszaigazolás elemzésének megkezdéséhez az alábbi Python-szkripttel hívja meg az **elemzés** visszaigazolása API-t. A szkript futtatása előtt végezze el a következő módosításokat:

1. Cserélje `<Endpoint>` le az elemet az űrlap-felismerő előfizetési kulcsból beszerzett végpontra. Az űrlap-felismerő erőforrás- **Áttekintés** lapon találhatja meg.
1. Cserélje `<your receipt URL>` le a értékét egy nyugtát ábrázoló rendszerkép URL-címére.
1. Cserélje `<subscription key>` le az elemet az előző lépésből másolt előfizetési kulcsra.

    ```python
    import http.client, urllib.request, urllib.parse, urllib.error, base64

    source = r"<your receipt URL>"
    body = {"url":source}
    body = json.dumps(body)

    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        conn = http.client.HTTPSConnection('<Endpoint>')
        conn.request("POST", "/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze", body, headers)
        response = conn.getresponse()
        data = response.read()
        operationURL = "" + response.getheader("Operation-Location")
        print ("Operation-Location header: " + operationURL)
        conn.close()
    except Exception as e:
        print(e)
        exit()
    ```

1. Mentse a kódot egy. file kiterjesztésű fájlba. Például: *Form-Recognizer-receipts.py*.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python form-recognizer-receipts.py`.

Egy `202 (Success)` olyan választ fog kapni, amely egy **művelet – hely** fejlécet tartalmaz, amelyet a szkript a konzolra fog nyomtatni. Ez a fejléc egy műveleti azonosítót tartalmaz, amelynek segítségével lekérdezheti a művelet állapotát, és beolvashatja az elemzési eredményeket. A következő példában szereplő sztring `operations/` a művelet azonosítója.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>A Bevételezés eredményeinek beolvasása

Az elemzési beérkezési  API meghívása után hívja meg a **Get beérkezési eredmény** API-t a művelet és a kinyert adatmennyiség állapotának lekéréséhez. Adja hozzá a következő kódot a Python-szkript aljához. Ezzel kibontja a műveleti azonosító értékét, és átadja egy új API-hívásnak. A művelet aszinkron, így a parancsfájl rendszeres időközönként meghívja az API-t, amíg az eredmények elérhetővé nem válnak. Javasoljuk, hogy egy vagy több másodperces intervallumot válasszon.

```python
operationId = operationURL.split("operations/")[1]

conn = http.client.HTTPSConnection('<Endpoint>')
while True:
    try:
        conn.request("GET", f"/formrecognizer/v1.0-preview/prebuilt/receipt/operations/{operationId}", "", headers)
        responseString = conn.getresponse().read().decode('utf-8')
        responseDict = json.loads(responseString)
        conn.close()
        print(responseString)
        if 'status' in responseDict and responseDict['status'] not in ['NotStarted','Running']:
            break
        time.sleep(1)
    except Exception as e:
        print(e)
        exit()
```

1. Mentse a parancsfájlt.
1. Ismét a paranccsal futtassa a mintát. `python` Például: `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>A válasz vizsgálata

A parancsfájl addig kinyomtatja a válaszokat a konzolra, amíg az elemzési művelet be nem fejeződik. Ezután JSON formátumban fogja kinyomtatni a kinyert szöveges adatfájlokat. A `"recognitionResults"` mező a beérkezésből kinyert szöveg minden sorát tartalmazza, a `"understandingResults"` mező pedig a Bevételezés legfontosabb részeinek kulcs/érték információit tartalmazza.

Tekintse meg az alábbi beérkezési képet és a hozzá tartozó JSON-kimenetet. A kimenet le lett rövidítve az olvashatóság érdekében.

![A contoso áruházból érkezett visszaigazolás](../media/contoso-receipt.png)

```json
{
  "status": "Succeeded",
  "recognitionResults": [{
    "page": 1,
    "clockwiseOrientation": 0.36,
    "width": 1688,
    "height": 3000,
    "unit": "pixel",
    "lines": [{
      "boundingBox": [616, 291, 1050, 278, 1053, 384, 620, 397],
      "text": "Contoso",
      "words": [{
        "boundingBox": [619, 292, 1051, 284, 1052, 382, 620, 398],
        "text": "Contoso"
      }]
    }, {
      "boundingBox": [322, 588, 501, 600, 497, 655, 318, 643],
      "text": "Contoso",
      "words": [{
        "boundingBox": [330, 590, 501, 602, 499, 654, 326, 644],
        "text": "Contoso"
      }]
    },
    ...
    ]
  }],
  "understandingResults": [{
    "pages": [1],
    "fields": {
      "Subtotal": {
        "valueType": "numberValue",
        "value": 1098.99,
        "text": "1098.99",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/14/words/1"
        }]
      },
      "Total": {
        "valueType": "numberValue",
        "value": 1203.39,
        "text": "1203.39",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/18/words/1"
        }]
      },
      "Tax": {
        "valueType": "numberValue",
        "value": 104.4,
        "text": "$104.40",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/16/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/16/words/1"
        }]
      },
      "MerchantAddress": {
        "valueType": "stringValue",
        "value": "123 Main Street Redmond, WA 98052",
        "text": "123 Main Street Redmond, WA 98052",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/2/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/2"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/2"
        }]
      },
      "MerchantName": {
        "valueType": "stringValue",
        "value": "Contoso",
        "text": "Contoso",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/1/words/0"
        }]
      },
      "MerchantPhoneNumber": {
        "valueType": "stringValue",
        "value": null,
        "text": "123-456-7890",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/4/words/0"
        }]
      },
      "TransactionDate": {
        "valueType": "stringValue",
        "value": "2019-06-10",
        "text": "6/10/2019",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/0"
        }]
      },
      "TransactionTime": {
        "valueType": "stringValue",
        "value": "13:59:00",
        "text": "13:59",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/1"
        }]
      }
    }
  }]
}
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az űrlap-felismerő REST APIt használta a Python használatával a modell betanításához és egy minta forgatókönyvben való futtatásához. Következő lépésként tekintse meg a dokumentációt az űrlap-felismerő API részletesebb megismeréséhez.

> [!div class="nextstepaction"]
> [REST API dokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
