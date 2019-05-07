---
title: 'Gyors útmutató: A modell betanítását és a cURL - űrlap felismerő használatával űrlap adatokat nyerhet ki'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutatóban a betanítja a modellt, és az adatok kinyerése az űrlapok használandó az űrlap felismerő REST API-t a curl használatával.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: pafarley
ms.openlocfilehash: 1afe9239dcc3f5a24d2e950ec7b563bf53d1f04c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143237"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-using-rest-api-with-curl"></a>Gyors útmutató: Egy űrlap felismerő modell betanítását, és bontsa ki az űrlapadatok REST API használatával a curl használatával

Ez a rövid útmutatóban a betanítását és pontozását űrlapok kulcs-érték párok és táblák használandó az űrlap felismerő REST API-t a curl használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Az űrlap felismerő korlátozott hozzáférésű előzetes hozzáférést kapott. Az előzetes verzió eléréséhez, kérjük töltse ki és küldje el a [Cognitive Services űrlap felismerő hozzáférési kérelem](https://aka.ms/FormRecognizerRequestAccess) űrlap. 
* Rendelkeznie kell a [cURL-lel](https://curl.haxx.se/windows/).
* Az űrlap felismerő rendelkeznie kell egy előfizetési kulcsot. Kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) felismerő képernyő előfizetés és a kulcs beszerzése.
* Az azonos típusú öt űrlapok csoportját kell rendelkeznie. Használhat egy [mintaadatkészlettel](https://go.microsoft.com/fwlink/?linkid=2090451) ebben a rövid útmutatóban.

## <a name="train-a-form-recognizer-model"></a>Egy űrlap felismerő modell betanítása

Először is szüksége lesz betanítási adatok egy készletét. Használhatja a saját helyi betanítási adatok vagy az Azure Blob adatait. Rendelkeznie kell legalább öt mintául szolgáló űrlapok (PDF-dokumentumok és/vagy képek) az azonos típusú/struktúra a fő bemeneti adatként. Másik lehetőségként használhatja az egyetlen üres űrlapon; az űrlap filename "üres" szót tartalmaz

Használatával a dokumentumok az Azure Blob-tárolóban űrlap felismerő modell betanításához hívja meg a **betanításához** API az alábbi cURL-parancs végrehajtásával. A parancs futtatása előtt végezze el az alábbi módosításokat:

* Cserélje le `<Endpoint>` kapott, az űrlap felismerő előfizetési kulcs a végponthoz. Az űrlap felismerő erőforrás Áttekintés lapján találja.
* Cserélje le `<SAS URL>` megosztott egy Azure Blob Storage-tárolóval rendelkező hozzáférési jogosultságkód (SAS) URL-címet, ahol a betanítási adatok megtalálható.  
* A `<subscription key>` helyére írja be az előfizetési kulcsot.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \"<SAS URL>\"}"
```

Kapni fog egy `200 (Success)` válasz a következő JSON-kimenetet:

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

Jegyezze fel a `"modelId"` érték; a következő lépéseket szükség lesz rá.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Kulcs-érték párok és táblák kinyerése űrlapok

Ezután, egy dokumentumot elemzi, és bontsa ki a kulcs-érték párok és táblák belőle. Hívja a **modell – elemzése** API az alábbi cURL-parancs végrehajtásával. A parancs futtatása előtt végezze el az alábbi módosításokat:

* Cserélje le `<Endpoint>` kapott, az űrlap felismerő előfizetési kulcs a végponthoz. Az űrlap felismerő erőforrás megtalálja **áttekintése** fülre.
* Cserélje le `<modelID>` a modellt, a modell tanítása az előző lépésben kapott azonosítóval.
* Cserélje le `<path to your form>` való az űrlapot a fájl elérési útját.
* A `<subscription key>` helyére írja be az előfizetési kulcsot.
* Cserélje le `<file type>` a fájltípus – a támogatott PDF-dokumentum, kép/jpeg, kép-vagy png.

```bash
cURL cmd: curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@<path to your form>;type=application/<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>A válasz vizsgálata

Sikeres válasz JSON formátumban adja vissza, és a kinyert kulcs-érték párok és táblák jelöli az űrlap.

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

Ebben az útmutatóban használt az űrlap felismerő REST API-k a curl használatával a modell betanítását, majd futtassa azt egy minta esetet. Ezután megismerheti az űrlap felismerő API alaposabban is körüljárják referencia dokumentációjában talál.

> [!div class="nextstepaction"]
> [REST API dokumentációja](https://aka.ms/form-recognizer/api)
