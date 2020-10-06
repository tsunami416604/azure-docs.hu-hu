---
title: 'Gyors útmutató: a betanítás a REST API és a Python-Form felismerő használatával'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható az űrlap-felismerő címkézett adatszolgáltatása a REST API és a Python használatával egyéni modell betanításához.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 25a21107fd4b67a3a67b69a73401540d7b02b6b5
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762076"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Űrlap-felismerő modell betanítása címkékkel REST API és Python használatával

Ebben a rövid útmutatóban az űrlap-felismerő REST API a Python használatával végezheti el a manuálisan címkézett adattípusú egyéni modell betanítását. A szolgáltatással kapcsolatos további információkért tekintse meg az Áttekintés a [címkékkel](../overview.md#train-with-labels) foglalkozó szakaszát.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőket kell tennie:
- [Python](https://www.python.org/downloads/) telepítve (ha helyileg szeretné futtatni a mintát).
- Legalább hat egyforma típusú formátumból álló készlet. Ezeket az adattípusokat fogja használni a modell betanításához és egy űrlap teszteléséhez. Ehhez a rövid útmutatóhoz [minta adatkészletet](https://go.microsoft.com/fwlink/?linkid=2090451) is használhat. Töltse fel a betanítási fájlokat egy blob Storage-tároló gyökerébe egy standard teljesítményű Azure Storage-fiókban.

> [!NOTE]
> Ez a rövid útmutató az URL-cím által elért távoli dokumentumokat használja. Ha ehelyett a helyi fájlokat szeretné használni, tekintse meg a 2.0-s verzióra [vonatkozó](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) dokumentációt, valamint a 2.1-es [referenciákat](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/TrainCustomModelAsync).

## <a name="create-a-form-recognizer-resource"></a>Űrlap-felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Betanítási adatértékek beállítása

Ezután be kell állítania a szükséges bemeneti adatokat. A címkézett adatok funkció olyan speciális bemeneti követelményekkel rendelkezik, amelyeken túl van szükség az egyéni modellek címkék nélküli betanításához.

Győződjön meg arról, hogy az összes betanítási dokumentum formátuma azonos. Ha űrlapjai többféle formátumban vannak, rendezze őket almappákba formátum szerint. A betanítás során egy almappára kell hivatkoznia az API-nak.

Ha címkével ellátott adatokkal kívánja betanítani a modellt, a következő fájloknak kell lennie bemenetként az almappában. Megtudhatja, hogyan hozhatja létre ezeket a fájlokat alább.

* **Forrásoldali űrlapok** – az adatok kinyerésére szolgáló űrlapok. A támogatott típusok a következők: JPEG, PNG, PDF vagy TIFF.
* **OCR-elrendezési fájlok** – ezek a JSON-fájlok, amelyek az összes olvasható szöveg méretét és helyét írják le az egyes forrás űrlapokon. Ezeket az adattípusokat az űrlap-felismerő elrendezési API-val fogja használni. 
* **Címkézett fájlok** – ezek olyan JSON-fájlok, amelyek a felhasználó által manuálisan megadott adatfeliratokat írják le.

Az összes fájlnak ugyanabban az almappájában kell lennie, és a következő formátumúnak kell lennie:

* input_file1.pdf 
* input_file1.pdf.ocr.jsbekapcsolva
* input_file1.pdf.labels.jsbekapcsolva 
* input_file2.pdf 
* input_file2.pdf.ocr.jsbekapcsolva
* input_file2.pdf.labels.jsbekapcsolva
* ...

> [!TIP]
> Ha az űrlap-felismerő [minta címkézési eszközzel](./label-tool.md)címkézi az űrlapokat, az eszköz automatikusan létrehozza ezeket a CÍMKÉKET és OCR-elrendezési fájlokat.

### <a name="create-the-ocr-output-files"></a>OCR kimeneti fájlok létrehozása

Ahhoz, hogy a szolgáltatás figyelembe vegye a címkével ellátott betanításhoz tartozó bemeneti fájlokat, az OCR-találati fájlok szükségesek. Egy adott forrás űrlap OCR-eredményeinek beszerzéséhez kövesse az alábbi lépéseket:

1. Hívja meg az **[elemzés elrendezés](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** API-t az olvasási elrendezés tárolójában a bemeneti fájllal a kérelem törzsének részeként. Mentse a válasz **műveleti helye** fejlécében található azonosítót.
1. Hívja meg az elemzési **[elrendezés eredményének beolvasása](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** API-t az előző lépés műveleti azonosítójának használatával.
1. Szerezze be a választ, és írja a tartalmat egy fájlba. Minden forrás űrlap esetében a megfelelő OCR-fájlnak tartalmaznia kell az eredeti fájlnevet a következővel: `.ocr.json` . Az OCR JSON-kimenetének a következő formátumúnak kell lennie. Tekintse meg a [minta OCR-fájlját](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json) a teljes példaként. 

    # <a name="v20"></a>[2.0-s verzió](#tab/v2-0)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    
    # <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    


    ---



### <a name="create-the-label-files"></a>A címkefájl létrehozása

A címkézett fájlok olyan kulcs-érték társításokat tartalmaznak, amelyeket a felhasználó kézzel írt be. A címkével ellátott adatképzéshez szükségesek, de nem minden forrásfájl számára szükséges a megfelelő címkefájl. A címkék nélküli forrásfájlok általános betanítási dokumentumként lesznek kezelve. A megbízható képzéshez öt vagy több címkézett fájlt ajánlunk. A fájlok létrehozásához használhat egy felhasználói felületi eszközt, például a [minta feliratozási eszközét](./label-tool.md) .

A címkefájl létrehozásakor megadhatja, hogy &mdash; az egyes régiók pontosan milyen értékekkel rendelkeznek a dokumentumban. Ez a képzés még nagyobb pontosságot eredményez. A régiók formátuma nyolc értékből áll, amelyek megfelelnek a négy X, Y koordinátáknak: felülről balra, jobb felső sarok, jobb alsó és bal alsó. A koordináta-értékek nulla és egy, a lap méretei közé vannak méretezve.

Minden forrás űrlap esetében a megfelelő címkefájl az eredeti fájlnevet fűzi hozzá a következőhöz: `.labels.json` . A címkefájl formátuma a következő lehet:. Tekintse meg a [minta címkefájl](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json) teljes példáját.

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!IMPORTANT]
> Csak egy címkét alkalmazhat az egyes szöveges elemekre, és az egyes címkék csak egyszer használhatók fel oldalanként. Nem alkalmazhat címkét több oldalra.


## <a name="train-a-model-using-labeled-data"></a>Modell betanítása címkézett adattal

Ha címkével ellátott adattal szeretne betanítani egy modellt, a következő Python-kód futtatásával hívja meg az **[Egyéni modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API-t. A kód futtatása előtt végezze el a következő módosításokat:

1. Cserélje le az értékét `<Endpoint>` az űrlap-felismerő erőforrás végponti URL-címére.
1. Cserélje le `<SAS URL>` az-t az Azure Blob Storage-tároló megosztott hozzáférési aláírása (SAS) URL-címére. Az SAS URL-cím lekéréséhez nyissa meg a Microsoft Azure Storage Explorer, kattintson a jobb gombbal a tárolóra, majd válassza a **közös hozzáférésű aláírás beolvasása**elemet. Győződjön meg arról, hogy az **olvasási** és a **listázási** engedély be van jelölve, majd kattintson a **Létrehozás**gombra. Ezután másolja az értéket az **URL** szakaszban. A következő formátumban kell lennie: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
1. A helyére írja `<Blob folder name>` be annak a mappának a nevét a blob-tárolóban, ahol a bemeneti adatok találhatók. Ha az adatok a gyökérben vannak, hagyja üresen, és távolítsa el a `"prefix"` mezőt a HTTP-kérelem törzsében.

# <a name="v20"></a>[2.0-s verzió](#tab/v2-0)
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```    
# <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)    
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.1-preview.1/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```   

---



## <a name="get-training-results"></a>Képzési eredmények beolvasása

A betanítási művelet elindítását követően a visszaadott AZONOSÍTÓval beolvashatja a művelet állapotát. Adja hozzá a következő kódot a Python-szkript aljához. Ez az azonosító értéket használja a betanítási hívásból egy új API-hívásban. A betanítási művelet aszinkron, így a parancsfájl rendszeres időközönként meghívja az API-t, amíg a betanítási állapot be nem fejeződik. Javasoljuk, hogy egy vagy több másodperces intervallumot válasszon.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

A betanítási folyamat befejeztével a `201 (Success)` következőhöz hasonló JSON-tartalommal kapcsolatos választ kap. A válasz lerövidítve lett az egyszerűség kedvéért.

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

Másolja az `"modelId"` értéket a következő lépésekben való használatra.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

Ha a folyamat befejeződött, a `202 (Success)` JSON-tartalommal kapcsolatos választ a következő formátumban fogja kapni. A válasz lerövidítve lett az egyszerűség kedvéért. A fő kulcs/érték társítások a `"documentResults"` csomóponton találhatók. A `"selectionMarks"` csomópont (v 2.1 előzetes verzió) megjeleníti az összes kijelölési jelet (jelölőnégyzet, választógomb), valamint azt, hogy az állapota "kijelölt" vagy "nem kijelölt". Az elrendezési API eredményei (a dokumentumban lévő szöveg tartalma és pozíciói) a `"readResults"` csomóponton vannak.

# <a name="v20"></a>[2.0-s verzió](#tab/v2-0)
```json
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:16:28Z",
  "lastUpdatedDateTime": "2020-08-21T02:16:35Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "boundingBox": [
              0.5826,
              0.4411,
              2.3387,
              0.4411,
              2.3387,
              0.7969,
              0.5826,
              0.7969
            ],
            "text": "Contoso, Ltd.",
            "words": [
              {
                "boundingBox": [
                  0.5826,
                  0.4411,
                  1.744,
                  0.4411,
                  1.744,
                  0.7969,
                  0.5826,
                  0.7969
                ],
                "text": "Contoso,",
                "confidence": 1
              },
              {
                "boundingBox": [
                  1.8448,
                  0.4446,
                  2.3387,
                  0.4446,
                  2.3387,
                  0.7631,
                  1.8448,
                  0.7631
                ],
                "text": "Ltd.",
                "confidence": 1
              }
            ]
          },
          ...
            ]
          }
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "rowIndex": 0,
                "columnIndex": 0,
                "text": "Training Date",
                "boundingBox": [
                  0.5133,
                  4.2167,
                  1.7567,
                  4.2167,
                  1.7567,
                  4.4492,
                  0.5133,
                  4.4492
                ],
                "elements": [
                  "#/readResults/0/lines/14/words/0",
                  "#/readResults/0/lines/14/words/1"
                ]
              },
              ...
            ]
          },
        ]
      }
    ],
    "documentResults": [
      {
        "docType": "custom:form",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Receipt No": {
            "type": "string",
            "valueString": "9876",
            "text": "9876",
            "page": 1,
            "boundingBox": [
              7.615,
              1.245,
              7.915,
              1.245,
              7.915,
              1.35,
              7.615,
              1.35
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/3/words/3"
            ]
          },
          ...
        }
      }
    ],
    "errors": []
  }
}
```
# <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1) 
```json   
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:29:42Z",
  "lastUpdatedDateTime": "2020-08-21T02:29:50Z",
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
            "boundingBox": [
              0.5826,
              0.4411,
              2.3387,
              0.4411,
              2.3387,
              0.7969,
              0.5826,
              0.7969
            ],
            "text": "Contoso, Ltd.",
            "words": [
              {
                "boundingBox": [
                  0.5826,
                  0.4411,
                  1.744,
                  0.4411,
                  1.744,
                  0.7969,
                  0.5826,
                  0.7969
                ],
                "text": "Contoso,",
                "confidence": 1
              },
              {
                "boundingBox": [
                  1.8448,
                  0.4446,
                  2.3387,
                  0.4446,
                  2.3387,
                  0.7631,
                  1.8448,
                  0.7631
                ],
                "text": "Ltd.",
                "confidence": 1
              }
            ]
          },
          ...
        ], 
        "selectionMarks": [
          {
            "boundingBox": [
              3.9737,
              3.7475,
              4.1693,
              3.7475,
              4.1693,
              3.9428,
              3.9737,
              3.9428
            ],
            ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "rowIndex": 0,
                "columnIndex": 0,
                "text": "Training Date",
                "boundingBox": [
                  0.5133,
                  4.2167,
                  1.7567,
                  4.2167,
                  1.7567,
                  4.4492,
                  0.5133,
                  4.4492
                ],
                "elements": [
                  "#/readResults/0/lines/12/words/0",
                  "#/readResults/0/lines/12/words/1"
                ]
              },
              ...
            ]
          }
        ] 
      }
    ], 
    "documentResults": [
      {
        "docType": "custom:e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "modelId": "e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "ID #": {
            "type": "string",
            "valueString": "5554443",
            "text": "5554443",
            "page": 1,
            "boundingBox": [
              2.315,
              2.43,
              2.74,
              2.43,
              2.74,
              2.515,
              2.315,
              2.515
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/8/words/1"
            ]
          },
          ...
        },
        "docTypeConfidence": 1
      }
    ],
    "errors": []
  }
}
```

---


## <a name="improve-results"></a>Az eredmények javítása

Vizsgálja `"confidence"` meg az egyes kulcsok/értékek eredményének értékét a `"documentResults"` csomópont alatt. Érdemes megtekinteni a csomópontban található megbízhatósági pontszámokat is `"readResults"` , amelyek az elrendezési műveletnek felelnek meg. Az elrendezés eredményének megbízhatósága nem befolyásolja a kulcs/érték kinyerési eredményeinek megbízhatóságát, ezért mindkettőt ellenőriznie kell.
* Ha az elrendezési művelet megbízhatósági pontszáma alacsony, próbálja meg javítani a bemeneti dokumentumok minőségét (lásd a [bemeneti követelményeket](../overview.md#input-requirements)).
* Ha a kulcs/érték kibontási műveletének megbízhatósági pontszáma alacsony, ügyeljen arra, hogy az elemzett dokumentumok ugyanolyan típusúak legyenek, mint a betanítási készletben használt dokumentumok. Ha a betanítási készlet dokumentumaiban változások vannak kialakítva, érdemes lehet különböző mappákba bontani őket, és minden egyes változathoz egy modellt betanítani.

### <a name="avoid-cluttered-labels"></a>Kerülje a felzsúfolt címkék elkerülését

Esetenként, ha ugyanazon a sorban eltérő címkéket alkalmaz, a szolgáltatás egyesítheti ezeket a címkéket egyetlen mezőbe. Például egy címben a város, az állam és az irányítószám különböző mezőkként is címkézhető, de az előrejelzés során ezeket a mezőket nem ismeri fel külön.

Tisztában vagyunk azzal, hogy ez a forgatókönyv elengedhetetlen az ügyfelek számára, és a jövőben is dolgozunk. Jelenleg azt javasoljuk, hogy a felhasználók több zsúfolt mezőt is felcímkéznek egyetlen mezőként, majd elkülönítsék a feltételeket a kinyerési eredmények utólagos feldolgozásával.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Pythont az űrlap-felismerő REST API, hogy a modelleket manuálisan címkézett adattal végezze. Következő lépésként tekintse meg az API-referenciák dokumentációját, amely részletesebben vizsgálja meg az űrlap-felismerő API-t.

> [!div class="nextstepaction"]
> [REST API dokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
