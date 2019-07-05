---
title: 'Gyors útmutató: Python - űrlap felismerő használatával beérkezési adatokat nyerhet ki'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az űrlap felismerő REST API-t fogjuk pythonnal adatokat nyerhet ki képekből, értékesítési Nyugtával rendelkezik.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: 8bd4d441859df6dbb36f594d8423eefd84274ec4
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592545"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Gyors útmutató: A képernyő felismerő REST API használatával a Pythonnal beérkezési adatokat nyerhet ki

Ez a rövid útmutatóban való használni kívánt Azure űrlap felismerő REST API a Pythonnal csomagolja ki, és azonosítsa az értékesítési Nyugtával rendelkezik releváns információkat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Rövid útmutató elvégzéséhez kell rendelkeznie:
- Az űrlap felismerő korlátozott hozzáférésű előzetes verzióra való hozzáférést. Töltse ki az előzetes verzió eléréséhez, és küldje el a [űrlap felismerő hozzáférési kérelem](https://aka.ms/FormRecognizerRequestAccess) űrlap.
- [Python](https://www.python.org/downloads/) telepítve (Ha a minta futtatása helyben szeretné).
- Visszaigazolás kép URL-címe. Használhat egy [képet](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) ebben a rövid útmutatóban.

## <a name="create-a-form-recognizer-resource"></a>Űrlap felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Visszaigazolás elemzése

Visszaigazolás elemzése elindításához hívja a **elemzése beérkezési** API-t a Python-szkriptet az alábbi. A szkript futtatása előtt győződjön meg arról, ezeket a módosításokat:

1. Cserélje le `<Endpoint>` az űrlap felismerő előfizetési kulcs beszerzett a végponttal. Az űrlap felismerő erőforráson található **áttekintése** fülre.
1. Cserélje le `<your receipt URL>` a nyugta kép URL-cím.
1. Cserélje le `<subscription key>` az előfizetés az előző lépésben kimásolt kulccsal.

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

1. Mentse a kódot egy fájlban .py kiterjesztéssel. Ha például *űrlap-felismerő-receipts.py*.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python form-recognizer-receipts.py`.

Kap egy `202 (Success)` választ, amely tartalmaz egy **művelet helymeghatározás** fejléc, amely kiírja a parancsfájl a konzolhoz. Ezt a fejlécet, amellyel a művelet állapotának lekérdezése, és az elemzés eredményeinek beolvasása művelettel-Azonosítót tartalmaz. Az alábbi példa érték, a karakterlánc után `operations/` a művelet azonosítója.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>A nyugta eredményeinek beolvasása

Po, hogy a **elemzése beérkezési** API-hívás a **beérkezési eredmény beolvasása** API állapotát a művelet és a kinyert adatok segítségével. Adja hozzá a következő kódot a Python-szkript alján. Ez a művelet azonosító értéket, és átadja egy új API-hívással. A művelet aszinkron,, ezért ez a szkript rendszeres időközönként meghívja az API-t, mindaddig, amíg az eredmények érhetők el. Azt javasoljuk, hogy legalább egy második időközt.

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
1. Újra használni a `python` parancsot a minta futtatásához. Például: `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>A válasz vizsgálata

A parancsfájl a konzol válaszokat nyomtatja ki addig, amíg az elemzés művelet befejeződik. Ezt követően nyomtatja ki a kinyert szöveg adatokat JSON formátumban. A `"recognitionResults"` mező tartalmazza, amelyek a nyugta kivonták minden sort és a `"understandingResults"` mező legfontosabb részeit fogadását kulcs/érték adatait tartalmazza.

Lásd az alábbi képen fogadását, és a megfelelő JSON kimeneti. A kimenet az olvashatóság érdekében csonkult.

![Contoso áruházból visszaigazolás](../media/contoso-receipt.png)

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

Ebben a rövid útmutatóban használt az űrlap felismerő REST API a Pythonnal a modell betanítását, majd futtassa azt egy mintaforgatókönyv. Ezután megismerheti az űrlap felismerő API alaposabban is körüljárják referencia dokumentációjában talál.

> [!div class="nextstepaction"]
> [REST API dokumentációja](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
