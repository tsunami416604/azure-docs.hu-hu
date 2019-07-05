---
title: 'Gyors útmutató: A cURL - űrlap felismerő használatával beérkezési adatokat nyerhet ki'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutatóban az űrlap felismerő REST API-t fogjuk a curl használatával adatokat nyerhet ki képekből, értékesítési Nyugtával rendelkezik.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: 0178e53e6a7fde54b988e710a1cabbb7ded69b22
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592582"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Gyors útmutató: A képernyő felismerő REST API használatával a curl használatával beérkezési adatokat nyerhet ki

Ez a rövid útmutatóban való használni kívánt Azure űrlap felismerő REST API a curl használatával csomagolja ki, és határozza meg az értékesítési Nyugtával rendelkezik releváns adatokat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Rövid útmutató elvégzéséhez kell rendelkeznie:
- Az űrlap felismerő korlátozott hozzáférésű előzetes verzióra való hozzáférést. Töltse ki az előzetes verzió eléréséhez, és küldje el a [űrlap felismerő hozzáférési kérelem](https://aka.ms/FormRecognizerRequestAccess) űrlap.
- [cURL](https://curl.haxx.se/windows/) telepítve.
- Visszaigazolás kép URL-címe. Használhat egy [képet](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) ebben a rövid útmutatóban.

## <a name="create-a-form-recognizer-resource"></a>Űrlap felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Visszaigazolás elemzése

Visszaigazolás elemzése elindításához hívja a **elemzése beérkezési** API-t az alábbi cURL-parancs használatával. Futtassa a parancsot, mielőtt a módosítások:

1. Cserélje le `<Endpoint>` az űrlap felismerő előfizetési kulcs beszerzett a végponttal. Az űrlap felismerő erőforráson található **áttekintése** fülre.
1. Cserélje le `<your receipt URL>` a nyugta kép URL-cím.
1. Cserélje le `<subscription key>` az előfizetés az előző lépésben kimásolt kulccsal.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

Kapni fog egy `202 (Success)` választ, amely tartalmaz egy **művelet helymeghatározás** fejléc. A fejléc értéke tartalmaz egy művelet azonosítója, amellyel a művelet állapotának lekérdezése, és az eredmények eléréséhez. A következő példában a karakterlánc után `operations/` a művelet azonosítója.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>A nyugta eredményeinek beolvasása

Po, hogy a **elemzése beérkezési** API-hívás a **beérkezési eredmény beolvasása** API állapotát a művelet és a kinyert adatok segítségével.

1. Cserélje le `<operationId>` az az előző lépésben a művelet azonosítója.
1. A `<subscription key>` helyére írja be az előfizetési kulcsot.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/operations/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>A válasz vizsgálata

Kapni fog egy `200 (Success)` JSON-kimeneteket a válaszban. Az első mező `"status"`, a művelet állapotát jelzi. Ha a művelet befejeződött, a `"recognitionResults"` mező tartalmazza, amelyek a nyugta kivonták minden sort és a `"understandingResults"` mező legfontosabb részeit fogadását kulcs/érték adatait tartalmazza. Ha a művelet nem fejeződött be, az értékét `"status"` lesz `"Running"` vagy `"NotStarted"`, és ismét meghívja az API-t manuálisan vagy egy parancsfájl segítségével. Azt javasoljuk, hogy legalább egy másodperc közötti hívások időközt.

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

Ez a rövid útmutatóban használt az űrlap felismerő REST API-t a curl használatával értékesítési nyugta tartalmának kibontásához. Ezután megismerheti az űrlap felismerő API alaposabban is körüljárják referencia dokumentációjában talál.

> [!div class="nextstepaction"]
> [REST API dokumentációja](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
