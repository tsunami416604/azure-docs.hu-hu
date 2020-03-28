---
title: 'Rövid útmutató: Modell betanítása és űrlapadatok kinyerése a REST API használatával pythonnal – Űrlapfelismerő'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban a Form Recognizer REST API-t a Python használatával fogja használni egy modell betanításához és az űrlapok ból származó adatok kinyeréséhez.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 66668f46595c22426984a02c489297e962d061d0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118083"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-python"></a>Rövid útmutató: Űrlapfelismerő modell betanítása és űrlapadatok kinyerése a REST API pythonnal való használatával

Ebben a rövid útmutatóban az Azure Form Recognizer REST API-t a Python nal fogja használni a kulcsérték-párok és táblák kinyeréséhez.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához a következőkre van szüksége:
- [Python](https://www.python.org/downloads/) telepítve (ha helyileg szeretné futtatni a mintát).
- Legalább öt azonos típusú forma. Ezeket az adatokat a modell betanításához fogja használni. Az űrlapok különböző fájltípusok lehetnek, de azonos típusú dokumentumoknak kell lenniük. Ehhez a rövid útmutatóhoz [mintaadatkészletet](https://go.microsoft.com/fwlink/?linkid=2090451) használhat. Töltse fel a betanítási fájlokat egy blob storage-tároló egy Azure Storage-fiók ban.

## <a name="create-a-form-recognizer-resource"></a>Űrlapfelismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Űrlapfelismerő modell betanítása

Először is szüksége lesz egy betanítási adatok egy Azure Storage blob tárolóban egy készlet. Legalább öt kitöltött űrlapnak (PDF-dokumentumnak és/vagy képnek) kell lennie, amelyek típusa/szerkezete megegyezik a fő bemeneti adatokéval. Vagy használhat egyetlen üres űrlapot két kitöltött űrlappal. Az üres űrlap fájlnevének tartalmaznia kell az "üres" szót. Lásd: [Betanítási adatkészlet létrehozása egyéni modellhez](../build-training-data-set.md) tippeket és lehetőségeket a betanítási adatok összeállításához.

> [!NOTE]
> A címkézett adatfunkció segítségével manuálisan címkézheti a betanítási adatok egy részét vagy egészét. Ez egy összetettebb folyamat, de egy jobban képzett modellt eredményez. További információért tekintse meg a [Vonat címkékkel](../overview.md#train-with-labels) című részt az áttekintésben.

Űrlapfelismerő modell betanításához az Azure blob tárolójában lévő dokumentumokkal, hívja meg a **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** API-t a következő python-kód futtatásával. A kód futtatása előtt hajtsa végre a következő módosításokat:

1. Cserélje `<SAS URL>` le az Azure Blob storage tároló megosztott hozzáférésű aláírás (SAS) URL-címét. A SAS URL-címének beolvasásához nyissa meg a Microsoft Azure Storage Exploreralkalmazást, kattintson a jobb gombbal a tárolóra, és válassza **a Megosztott hozzáférésű aláírás beolvasása parancsot.** Ellenőrizze, hogy az **Olvasás** és **a Lista** engedélyek be vannak-e jelölve, majd kattintson a **Létrehozás gombra.** Ezután másolja az **URL-cím** szakasz értékét. Meg kell a `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`forma: .
1. Cserélje `<subscription key>` le az előző lépésből másolt előfizetési kulcsot.
1. Cserélje `<endpoint>` le az űrlapfelismerő erőforrás végpontjának URL-címét.
1. Cserélje `<Blob folder name>` le a blob storage-ban található mappához vezető elérési utat, ahol az űrlapok találhatók. Ha az űrlapok a tároló gyökerében vannak, hagyja üresen ezt a karakterláncot.

    ```python
    ########### Python Form Recognizer Labeled Async Train #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    post_url = endpoint + r"/formrecognizer/v2.0-preview/custom/models"
    source = r"<SAS URL>"
    prefix = "<Blob folder name>"
    includeSubFolders = False
    useLabelFile = False
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subsription key>',
    }
    
    body =  {
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
1. Mentse a kódot egy .py kiterjesztésű fájlba. Például *form-recognizer-train.py*.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python form-recognizer-train.py`.

## <a name="get-training-results"></a>Képzési eredmények beszerezni

Miután elindította a vonat működését, a visszaadott azonosító segítségével kapja meg a művelet állapotát. Adja hozzá a következő kódot a Python-parancsfájl aljához. Ez egy új API-hívás betanítási hívásának azonosítóértékét használja. A betanítási művelet aszinkron, így ez a parancsfájl rendszeres időközönként meghívja az API-t, amíg a betanítási állapot befejeződik. Javasoljuk, hogy egy másodperc vagy több időközt.

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

Amikor a betanítási folyamat befejeződött, a json-tartalommal `201 (Success)` kapcsolatos választ kap, például a következőket:

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

Másolja `"modelId"` az értéket a következő lépésekbe.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

Amikor a folyamat befejeződött, a `200 (Success)` JSON-tartalommal kapcsolatos választ kap a következő formátumban. A válasz az egyszerűség kedvéért rövidült. A fő kulcs/értékpár társítások `"pageResults"` és táblák a csomópontban találhatók. Ha az *includeTextDetails* URL-paraméteren keresztül is megadtál egyszerű szövegkinyerést, akkor a `"readResults"` csomópont a dokumentum teljes szövegének tartalmát és pozícióját jeleníti meg.

```bash
{
  "analyzeResult":{ 
    "readResults":[ 
      { 
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          { 
            "text":"PT",
            "boundingBox":[ 
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[ 
              { 
                "text":"PT",
                "boundingBox":[ 
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[ 

    ],
    "documentResults":[ 

    ],
    "pageResults":[ 
      { 
        "page":1,
        "clusterId":1,
        "keyValuePairs":[ 
          { 
            "key":{ 
              "text":"Address:",
              "boundingBox":[ 
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{ 
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[ 
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[ 
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          { 
            "key":{ 
              "text":"Invoice For:",
              "boundingBox":[ 
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{ 
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[ 
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[ 
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[ 
          { 
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[ 
              { 
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[ 
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[ 
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>Az eredmények javítása

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a Form Recognizer REST API-t a Python használatával egy modell betanításához és mintaforgatókönyvben való futtatásához használta. Ezután tekintse meg a referenciadokumentációt a Form Recognizer API részletesebb megismeréséhez.

> [!div class="nextstepaction"]
> [REST API referenciadokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
