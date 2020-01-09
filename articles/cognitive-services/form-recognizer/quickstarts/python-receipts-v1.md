---
title: 'Gyors útmutató: bevételezési adatok kinyerése a Python-Form felismerő használatával'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az űrlap-felismerő REST API és a Python használatával gyűjti össze az adatokból az értékesítési nyugták lemezképeit.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: pafarley
ms.openlocfilehash: 5d95527fa5333b113fb53840fd687313a139413a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473877"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Gyors útmutató: bevételezési adatok kinyerése az űrlap-felismerő REST API és a Python használatával

Ebben a rövid útmutatóban az Azure űrlap-felismerő REST API a Python használatával kinyerheti és azonosíthatja a kapcsolódó információkat az értékesítési nyugtákban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!IMPORTANT]
> Ez a rövid útmutató a Form felismerő v 1.0 API-t használja. Ha az előfizetése a `West US 2` vagy `West Europe` régióban található, akkor helyette a v[2,0 API](./python-train-extract.md) -t kell követnie.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez a következőket kell tennie:
- Hozzáférés az űrlap-felismerő korlátozott hozzáférésének előzetes verziójához. Az előzetes verzió eléréséhez töltse ki és küldje el az [űrlap-felismerő hozzáférési kérelmének](https://aka.ms/FormRecognizerRequestAccess) űrlapját.
- [Python](https://www.python.org/downloads/) telepítve (ha helyileg szeretné futtatni a mintát).
- Egy nyugtát ábrázoló rendszerkép URL-címe. Ehhez a rövid útmutatóhoz [minta képet](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) is használhat.

## <a name="create-a-form-recognizer-resource"></a>Űrlap-felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Visszaigazolás elemzése

A visszaigazolás elemzésének megkezdéséhez az alábbi Python-szkripttel hívja meg az **elemzés visszaigazolása** API-t. A szkript futtatása előtt végezze el a következő módosításokat:

1. Cserélje le a `<Endpoint>`t arra a végpontra, amelyet az űrlap-felismerő előfizetésével kapott.
1. Cserélje le a `<your receipt URL>`t egy nyugtás rendszerkép URL-címére.
1. Cserélje le a `<subscription key>`t az előző lépésből másolt előfizetési kulccsal.

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

`202 (Success)` választ fog kapni, amely tartalmazza a **művelet – hely** fejlécet, amelyet a parancsfájl a konzolra fog nyomtatni. Ez a fejléc egy műveleti azonosítót tartalmaz, amelynek segítségével lekérdezheti a művelet állapotát, és beolvashatja az elemzési eredményeket. A következő példában a sztring `operations/` után a műveleti azonosító.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>A Bevételezés eredményeinek beolvasása

Az elemzési **beérkezési** API meghívása után hívja meg a **Get beérkezési eredmény** API-t a művelet és a kinyert adatmennyiség állapotának lekéréséhez. Adja hozzá a következő kódot a Python-szkript aljához. Ezzel kibontja a műveleti azonosító értékét, és átadja egy új API-hívásnak. A művelet aszinkron, így a parancsfájl rendszeres időközönként meghívja az API-t, amíg az eredmények elérhetővé nem válnak. Javasoljuk, hogy egy vagy több másodperces intervallumot válasszon.

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
1. A minta futtatásához használja a `python` parancsot. Például: `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>A válasz vizsgálata

A parancsfájl addig kinyomtatja a válaszokat a konzolra, amíg az elemzési művelet be nem fejeződik. Ezután JSON formátumban fogja kinyomtatni a kinyert szöveges adatfájlokat. A `"recognitionResults"` mező a beérkezett szöveg minden olyan sorát tartalmazza, amelyet a rendszer a nyugtából kinyert, és a `"understandingResults"` mező a Bevételezés legfontosabb részeire vonatkozó kulcs/érték információkat tartalmazza.

Tekintse meg az alábbi beérkezési képet és a hozzá tartozó JSON-kimenetet. A kimenet le lett rövidítve az olvashatóság érdekében.

![A contoso áruházból érkezett visszaigazolás](../media/contoso-receipt.png)

Az `"recognitionResults"` csomópont tartalmazza az összes felismert szöveget. A szöveget az oldal, a sor, majd az egyes szavak szerint rendezi. A `"understandingResults"` csomópont tartalmazza a modell által felderített bevételezés-specifikus értékeket. Itt talál hasznos kulcs/érték párokat, mint például az adó, a teljes, a kereskedelmi címek és így tovább.

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

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az űrlap-felismerő REST APIt használta a Python használatával a modell betanításához és egy minta forgatókönyvben való futtatásához. Következő lépésként tekintse meg a dokumentációt az űrlap-felismerő API részletesebb megismeréséhez.

> [!div class="nextstepaction"]
> [REST API dokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)