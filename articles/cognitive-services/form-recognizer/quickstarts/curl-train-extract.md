---
title: 'Gyors útmutató: modell betanítása és űrlapos adatok kinyerése a cURL-Form felismerő használatával'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az űrlap-felismerő REST API a cURL használatával betaníthatja a modelleket, és kinyerheti az adatok űrlapokból való kinyerését.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: f4e26d74ff0922841d2ceb2ce4eb06b96c697a9f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88719092"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Gyors útmutató: űrlap-felismerő modell betanítása és űrlap-adatok kinyerése a REST API és a cURL használatával

Ebben a rövid útmutatóban az Azure űrlap-felismerő REST API a cURL használatával betanítási és pontszám-űrlapok segítségével kinyerheti a kulcs-érték párokat és táblákat.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőket kell tennie:
- a [fürt](https://curl.haxx.se/windows/) telepítve van.
- Legalább hat egyforma típusú formátumból álló készlet. Ezek közül öt a modell betanításához, majd a hatodik űrlappal való teszteléséhez fog használni. Az űrlapok különböző fájltípusok lehetnek, de azonos típusú dokumentumnak kell lenniük. Ehhez a rövid útmutatóhoz [minta adatkészletet](https://go.microsoft.com/fwlink/?linkid=2090451) is használhat. Töltse fel a betanítási fájlokat egy blob Storage-tároló gyökerébe egy standard teljesítményű Azure Storage-fiókban. A tesztelési fájlokat külön mappában helyezheti el.

## <a name="create-a-form-recognizer-resource"></a>Űrlap-felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Űrlap-felismerő modell betanítása

Először is szüksége lesz egy Azure Storage-blobban található betanítási adathalmazra. Legalább öt kitöltött űrlapot (PDF-dokumentumot és/vagy képet) kell tartalmaznia a fő bemeneti adatokhoz hasonló típusú vagy szerkezetű űrlapokon. Vagy egyetlen üres űrlapot is használhat két kitöltött űrlap használatával. Az üres űrlap fájlnevének tartalmaznia kell a "Empty" szót. A betanítási adataival kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../build-training-data-set.md) létrehozása című témakört.

> [!NOTE]
> A címkézett adatszolgáltatással manuálisan is felcímkézheti a betanítási adatait. Ez egy összetettebb folyamat, de jobban betanított modellt eredményez. A szolgáltatással kapcsolatos további információkért tekintse meg az Áttekintés a [címkékkel](../overview.md#train-with-labels) foglalkozó szakaszát.



Ha az Azure Blob-tárolóban található dokumentumokkal szeretne betanítani egy űrlap-felismerő modellt, hívja meg az **[Egyéni modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API-ját a következő curl-parancs futtatásával. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `<Endpoint>` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le az `<subscription key>` elemet az előző lépésből másolt előfizetési kulcsra.
1. Cserélje le `<SAS URL>` az-t az Azure Blob Storage-tároló megosztott hozzáférési aláírása (SAS) URL-címére. Az SAS URL-cím lekéréséhez nyissa meg a Microsoft Azure Storage Explorer, kattintson a jobb gombbal a tárolóra, majd válassza a **közös hozzáférésű aláírás beolvasása**elemet. Győződjön meg arról, hogy az **olvasási** és a **listázási** engedély be van jelölve, majd kattintson a **Létrehozás**gombra. Ezután másolja az értéket az **URL** szakaszban. A következő formátumban kell lennie: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

    # <a name="v20"></a>[2.0-s verzió](#tab/v2-0)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    # <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    
    ---



A `201 (Success)` Válasz egy **Location** fejlécet kap. Ennek a fejlécnek az értéke a betanított új modell azonosítója.

## <a name="get-training-results"></a>Képzési eredmények beolvasása

Miután elindította a vonatok műveletét, új műveletet fog használni, **[Egyéni modell beszerzésével](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** ellenőrizhető a betanítási állapot. Adja át a modell AZONOSÍTÓját ebbe az API-hívásba a betanítási állapot megtekintéséhez:

1. Cserélje le `<Endpoint>` az helyére az űrlapot felismerő előfizetési kulccsal beszerzett végpontot.
1. Lecserélés az `<subscription key>` előfizetési kulccsal
1. Cserélje le az `<model ID>` t az előző lépésben kapott modell-azonosítóra.



# <a name="v20"></a>[2.0-s verzió](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```ce\": \""<SAS URL>"\"}"
```
    
---

`200 (Success)`A következő formátumban kap választ egy JSON-törzsből. Figyelje meg a `"status"` mezőt. Ez az érték a `"ready"` betanítás befejezése után lesz. Ha a modell nem fejeződött be, a parancs újbóli futtatásával újra le kell kérdezni a szolgáltatást. Javasoljuk, hogy a hívások között egy másodperc vagy több intervallum legyen.

A `"modelId"` mező tartalmazza a betanított modell azonosítóját. Ezt a következő lépéshez kell megadnia.

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>A kulcs-érték párok és táblák űrlapjainak elemzése

Ezután az újonnan betanított modellt fogja használni a dokumentumok elemzéséhez és a kulcs-érték párok és táblák kinyeréséhez. Hívja meg az **[elemzés űrlap](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** API-t a következő curl-parancs futtatásával. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `<Endpoint>` az elemet az űrlap-felismerő előfizetési kulcsból beszerzett végpontra. Az űrlap-felismerő erőforrás- **Áttekintés** lapon találhatja meg.
1. Cserélje le az értékét az `<model ID>` előző szakaszban kapott modell-azonosítóra.
1. Cserélje le `<SAS URL>` egy sas URL-címet a fájlra az Azure Storage-ban. Kövesse a betanítás szakasz lépéseit, de ahelyett, hogy a teljes blob-tároló SAS URL-címét beolvassa, szerezze be egyet az elemezni kívánt fájlhoz.
1. A `<subscription key>` helyére írja be az előfizetési kulcsot.

# <a name="v20"></a>[2.0-s verzió](#tab/v2-0)    
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
# <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)    
```bash
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
    
---



Választ fog kapni egy `202 (Success)` **műveleti hely** fejlécével. Ennek a fejlécnek az értéke tartalmazza az elemzési művelet eredményeinek nyomon követésére használt eredmény-azonosítót. Mentse a következő lépés eredmény-AZONOSÍTÓját.

## <a name="get-the-analyze-results"></a>Az elemzés eredményeinek beolvasása

A következő API használatával kérdezheti le az elemzési művelet eredményét.

1. Cserélje le `<Endpoint>` az elemet az űrlap-felismerő előfizetési kulcsból beszerzett végpontra. Az űrlap-felismerő erőforrás- **Áttekintés** lapon találhatja meg.
1. Cserélje le az `<result ID>` azonosítót az előző szakaszban kapott azonosítóra.
1. A `<subscription key>` helyére írja be az előfizetési kulcsot.


# <a name="v20"></a>[2.0-s verzió](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
    
---

`200 (Success)`A következő formátumban kap választ egy JSON-törzsből. A kimenet lerövidítve az egyszerűség kedvéért. Figyelje meg a `"status"` mező alján található mezőt. Ez az érték lesz az elemzési `"succeeded"` művelet befejezésekor. Ha az elemzési művelet nem fejeződött be, újra le kell kérdezni a szolgáltatást a parancs újbóli futtatásával. Javasoljuk, hogy a hívások között egy másodperc vagy több intervallum legyen.

A fő kulcs/érték párok társításai és táblái a `"pageResults"` csomóponton találhatók. Ha az egyszerű szöveg kinyerését is a *includeTextDetails* URL-cím paraméterrel adta meg, akkor a `"readResults"` csomópont megjeleníti a dokumentumban lévő összes szöveg tartalmát és pozícióit.

Ez a JSON-kimenet lerövidítve az egyszerűség kedvéért.

# <a name="v20"></a>[2.0-s verzió](#tab/v2-0)
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          }, 
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```    
# <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)    
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          }, 
          ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ], 
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ], 
    "documentResults": [],
    "errors": []
  }
}
``` 

---


## <a name="improve-results"></a>Az eredmények javítása

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az űrlap-felismerő REST API és a cURL használatával végezte el a modell betanítását, és egy minta forgatókönyvben futtatja azt. Következő lépésként tekintse meg a dokumentációt az űrlap-felismerő API részletesebb megismeréséhez.

> [!div class="nextstepaction"]
> [REST API dokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
