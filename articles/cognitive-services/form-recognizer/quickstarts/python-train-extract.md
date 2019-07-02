---
title: 'Gyors útmutató: A modell betanítását, és bontsa ki az űrlapadatok REST API a Pythonnal – űrlap felismerő használatával'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban, segítségével, az űrlap felismerő REST API a Pythonnal betanítja a modellt, és az adatok kinyerése az űrlapok.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/24/2019
ms.author: pafarley
ms.openlocfilehash: 6119bacee7be65588f2d9cb5becb86296fcf1559
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502833"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-python"></a>Gyors útmutató: Űrlap felismerő modellek betanítása és űrlap adatokat nyerhet ki a REST API a pythonnal

Ebben a rövid útmutatóban a betanítását és pontozását űrlapok kulcs-érték párok és táblák használni az Azure űrlap felismerő REST API a Pythonnal.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Rövid útmutató elvégzéséhez kell rendelkeznie:
- Az űrlap felismerő korlátozott hozzáférésű előzetes verzióra való hozzáférést. Töltse ki az előzetes verzió eléréséhez, és küldje el a [űrlap felismerő hozzáférési kérelem](https://aka.ms/FormRecognizerRequestAccess) űrlap.
- [Python](https://www.python.org/downloads/) telepítve (Ha a minta futtatása helyben szeretné).
- Az azonos típusú legalább öt űrlapok készlete. Ezeket az adatokat a modell betanításához használandó. Használhat egy [minta adatkészlet](https://go.microsoft.com/fwlink/?linkid=2090451) ebben a rövid útmutatóban. Töltse fel az adatok Azure Blob Storage-fiók gyökérmappájában.

## <a name="create-a-form-recognizer-resource"></a>Űrlap felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Egy űrlap felismerő modell betanítása

Először is kell egy Azure Storage blob-tárolóban a betanítási adatok egy készletét. Rendelkeznie kell legalább öt mintául szolgáló űrlapok (PDF-dokumentumok és/vagy képek) az azonos típusú/struktúra a fő bemeneti adatként. Vagy használhat egy egyetlen üres képernyő két ki vannak töltve űrlap. Az űrlap üres fájl nevének kell a keresőkifejezésben "üres".

Egy űrlap felismerő modell betanításához az Azure blob-tárolóban a dokumentumok használatával, hívja a **betanításához** a következő kód futtatásával a python API-t. A kód futtatásához előtt ezeket a módosításokat:

1. Cserélje le `<Endpoint>` az űrlap felismerő erőforrás Azure-régióban, ahol beszerzett az előfizetési kulcsok a végpont URL-címet.
1. Cserélje le `<SAS URL>` együtt az Azure Blob storage-tároló megosztott hozzáférési jogosultságkód (SAS) URL-címe. Ez lekéréséhez nyissa meg a Microsoft Azure Storage Explorer, kattintson a jobb gombbal a tárolóra, és válassza ki **Get közös hozzáférésű jogosultságkód**. Kattintson a következő párbeszédpanelen, és másolja az értéket a **URL-cím** szakaszban. Az űrlap kell rendelkeznie: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
1. Cserélje le `<Subscription key>` az előfizetés az előző lépésben kimásolt kulccsal.
    ```python
    ########### Python Form Recognizer Train #############
    from requests import post as http_post

    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    source = r"<SAS URL>"
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<Subscription Key>',
    }
    url = base_url + "/train" 
    body = {"source": source}
    try:
        resp = http_post(url = url, json = body, headers = headers)
        print("Response status code: %d" % resp.status_code)
        print("Response body: %s" % resp.json())
    except Exception as e:
        print(str(e))
    ```
1. Mentse a kódot egy fájlban .py kiterjesztéssel. Ha például *űrlap felismerni train.py*.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python form-recognize-train.py`.

Kapni fog egy `200 (Success)` válaszban a JSON-kimenetet:

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

Megjegyzés: a `"modelId"` értéket. Szüksége lesz rá a következő lépéseket.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Kulcs-érték párok és táblák kinyerése űrlapok

Ezután fog dokumentum elemzése és bontsa ki a kulcs-érték párok és táblák belőle. Hívja a **modell – elemzése** a következő API-t, hogy a Python-szkript futtatásával. Futtassa a parancsot, mielőtt a módosítások:

1. Cserélje le `<Endpoint>` az űrlap felismerő előfizetési kulccsal végzett beszerzett a végponttal. Az űrlap felismerő erőforráson található **áttekintése** fülre.
1. Cserélje le `<path to your form>` az elérési útját az űrlap (például C:\temp\file.pdf).
1. Cserélje le `<modelID>` a modell az előző szakaszban kapott azonosítóval.
1. Cserélje le `<file type>` az a fájl típusa. Támogatott típusok: `application/pdf`, `image/jpeg`, `image/png`.
1. A `<subscription key>` helyére írja be az előfizetési kulcsot.

    ```python
    ########### Python Form Recognizer Analyze #############
    from requests import post as http_post
    
    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    file_path = r"<path to your form>"
    model_id = "<modelID>"
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        url = base_url + "/models/" + model_id + "/analyze" 
        with open(file_path, "rb") as f:
            data_bytes = f.read()  
        resp = http_post(url = url, data = data_bytes, headers = headers)
        print("Response status code: %d" % resp.status_code)    
        print("Response body:\n%s" % resp.json())   
    except Exception as e:
        print(str(e))
    ```

1. Mentse a kódot egy fájlban .py kiterjesztéssel. Ha például *űrlap felismerni analyze.py*.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>A válasz vizsgálata

A sikeres válasz JSON-fájlban. Azt jelöli, a kulcs-érték párok és az űrlap kinyert táblázatok:

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban használt az űrlap felismerő REST API a Pythonnal a modell betanítását, majd futtassa azt egy mintaforgatókönyv. Ezután megismerheti az űrlap felismerő API alaposabban is körüljárják referencia dokumentációjában talál.

> [!div class="nextstepaction"]
> [REST API dokumentációja](https://aka.ms/form-recognizer/api)
