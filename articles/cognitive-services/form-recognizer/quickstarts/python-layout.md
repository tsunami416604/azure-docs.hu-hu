---
title: 'Rövid útmutató: Szöveg- és elrendezésadatok kinyerése python használatával – Űrlapfelismerő'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban a Form Recognizer Layout REST API-t a Pythonnal fogja használni szöveg- és táblázatadatok olvasásához az űrlapokból.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 342ae7e42c85ad661c04ba4ebb6629673f4af4dc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482276"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Rövid útmutató: Szöveg- és elrendezésadatok kinyerése a Form Recognizer REST API-val pythonnal

Ebben a rövid útmutatóban az Azure Form Recognizer REST API-t a Python segítségével kinyerheti a szövegelrendezési adatok és a táblázatadatok űrlapdokumentumokból.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához a következőkre van szüksége:
- [Python](https://www.python.org/downloads/) telepítve (ha helyileg szeretné futtatni a mintát).
- Űrlapdokumentum. A gyorsútmutató [mintaadatkészletéből](https://go.microsoft.com/fwlink/?linkid=2090451) letöltheti a lemezképet.

## <a name="create-a-form-recognizer-resource"></a>Űrlapfelismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>Az űrlap elrendezésének elemzése

Az elrendezés elemzésének megkezdéséhez hívja meg az **[Elrendezés elemzése](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** API-t az alábbi Python-parancsfájl használatával. A parancsfájl futtatása előtt hajtsa végre a következő módosításokat:

1. Cserélje `<Endpoint>` le a Form Recognizer-előfizetéssel kapott végpontra.
1. Cserélje `<path to your form>` le a helyi űrlapdokumentum elérési útját.
1. Cserélje `<subscription key>` le az előző lépésből másolt előfizetési kulcsot.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Mentse a kódot egy .py kiterjesztésű fájlba. Például *form-recognizer-layout.py*.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python form-recognizer-layout.py`.

Olyan `202 (Success)` választ fog kapni, amely tartalmazza a **Műveleti hely** fejlécét, amelyet a parancsfájl a konzolra fog nyomtatni. Ez a fejléc egy műveleti azonosítót tartalmaz, amellyel lekérdezheti az aszinkron művelet állapotát, és lekaphatja az eredményeket. A következő példaértékben az `operations/` ezt követő karakterlánc a műveletazonosító.

```console
https://cognitiveservice/formrecognizer/v2.0-preview/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>Az elrendezés eredményeinek beszerezni

Miután meghívta az **Elrendezés elemzése** API-t, hívja meg a Get Analyze **[Layout Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** API-t a művelet állapotának és a kinyert adatok nak a lekéréséhez. Adja hozzá a következő kódot a Python-parancsfájl aljához. Ez a kód a műveletazonosító értékét használja egy új API-hívásban. Ez a parancsfájl rendszeres időközönként meghívja az API-t, amíg az eredmények elérhetővé nem válikk. Javasoljuk, hogy egy másodperc vagy több időközt.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Mentse a forgatókönyvet.
1. Ismét használja `python` a parancsot a minta futtatásához. Például: `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>A válasz vizsgálata

A parancsfájl addig nyomtatja a válaszokat a konzolra, amíg az **Elrendezés elemzése** művelet be nem fejeződik. Ezután json formátumban nyomtatja ki a kinyert adatokat. A `"readResults"` csomópont minden szövegsort tartalmaz, és a megfelelő határolókeret elhelyezése az oldalon található. A `"pageResults"` mező a táblázatokon belüli minden szövegdarabot megjelenít, mindegyiknek a soroszlop koordinátáival.

Tekintse meg a következő számlaképet és a megfelelő JSON-kimenetet. A kimenet az egyszerűség kedvéért lerövidült.

> [!div class="mx-imgBorder"]
> ![Contoso számlabizonylat táblával](../media/contoso-invoice.png)

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-11-12T19:55:36Z",
  "lastUpdatedDateTime":"2019-11-12T19:55:43Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "language":"en",
        "angle":0,
        "width":8.5,
        "height":11,
        "unit":"inch",
        "lines":[ 
          { 
            "language":"en",
            "boundingBox":[ 
              0.5384,
              1.1583,
              1.4466,
              1.1583,
              1.4466,
              1.3534,
              0.5384,
              1.3534
            ],
            "text":"Contoso",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.5384,
                  1.1583,
                  1.4466,
                  1.1583,
                  1.4466,
                  1.3534,
                  0.5384,
                  1.3534
                ],
                "text":"Contoso",
                "confidence":1
              }
            ]
          },
          { 
            "language":"en",
            "boundingBox":[ 
              0.7994,
              1.5143,
              1.3836,
              1.5143,
              1.3836,
              1.6154,
              0.7994,
              1.6154
            ],
            "text":"Address:",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.7994,
                  1.5143,
                  1.3836,
                  1.5143,
                  1.3836,
                  1.6154,
                  0.7994,
                  1.6154
                ],
                "text":"Address:",
                "confidence":1
              }
            ]
          },
          ...
          { 
            "language":"en",
            "boundingBox":[ 
              6.2285,
              3.4114,
              6.3919,
              3.4114,
              6.3919,
              3.5119,
              6.2285,
              3.5119
            ],
            "text":"PT",
            "words":[ 
              { 
                "boundingBox":[ 
                  6.2285,
                  3.4114,
                  6.3919,
                  3.4114,
                  6.3919,
                  3.5119,
                  6.2285,
                  3.5119
                ],
                "text":"PT",
                "confidence":1
              }
            ]
          }
        ]
      }
    ],
    "pageResults":[ 
      { 
        "page":1,
        "tables":[ 
          { 
            "rows":2,
            "columns":6,
            "cells":[ 
              { 
                "rowIndex":0,
                "columnIndex":0,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5075,
                  2.8088,
                  1.9061,
                  2.8088,
                  1.9061,
                  3.3219,
                  0.5075,
                  3.3219
                ],
                "elements":[ 
                  "#/readResults/0/lines/8/words/0",
                  "#/readResults/0/lines/8/words/1"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a Form Recognizer REST API-t a Python nal a számla szövegelrendezésének kinyeréséhez használta. Ezután tekintse meg a referenciadokumentációt a Form Recognizer API részletesebb megismeréséhez.

> [!div class="nextstepaction"]
> [REST API referenciadokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)
