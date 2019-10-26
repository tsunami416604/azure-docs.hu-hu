---
title: 'Gyors útmutató: modell betanítása és űrlapos adatok kinyerése a cURL-Form felismerő használatával'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az űrlap-felismerő REST API a cURL használatával betaníthatja a modelleket, és kinyerheti az adatok űrlapokból való kinyerését.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 098dc5e2ab7d4b9533f58e03557db533eaa49a90
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931274"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Gyors útmutató: űrlap-felismerő modell betanítása és űrlap-adatok kinyerése a REST API és a cURL használatával

Ebben a rövid útmutatóban az Azure űrlap-felismerő REST API a cURL használatával betanítási és pontszám-űrlapok segítségével kinyerheti a kulcs-érték párokat és táblákat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez a következőket kell tennie:
- Hozzáférés az űrlap-felismerő korlátozott hozzáférésének előzetes verziójához. Az előzetes verzió eléréséhez töltse ki és küldje el az [űrlap-felismerő hozzáférési kérelmének](https://aka.ms/FormRecognizerRequestAccess) űrlapját.
- a [fürt](https://curl.haxx.se/windows/) telepítve van.
- Legalább öt azonos típusú űrlap. Ezeket az adattípusokat fogja használni a modell betanításához. Ehhez a rövid útmutatóhoz [minta adatkészletet](https://go.microsoft.com/fwlink/?linkid=2090451) is használhat. Töltse fel a betanítási fájlokat egy blob Storage-tároló gyökerébe egy Azure Storage-fiókban.

## <a name="create-a-form-recognizer-resource"></a>Űrlap-felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Űrlap-felismerő modell betanítása

Először is szüksége lesz egy Azure Storage-blobban található betanítási adathalmazra. Legalább öt kitöltött űrlapot (PDF-dokumentumot és/vagy képet) kell tartalmaznia a fő bemeneti adatokhoz hasonló típusú vagy szerkezetű űrlapokon. Vagy egyetlen üres űrlapot is használhat két kitöltött űrlap használatával. Az üres űrlap fájlnevének tartalmaznia kell a "Empty" szót. A betanítási adataival kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../build-training-data-set.md) létrehozása című témakört.

Ha az Azure Blob-tárolóban található dokumentumokkal szeretne betanítani egy űrlap-felismerő modellt, a következő cURL-parancs futtatásával hívja meg a **Train** API-t. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le a `<Endpoint>`t arra a végpontra, amelyet az űrlap-felismerő előfizetésével kapott.
1. Cserélje le a `<subscription key>` értéket az előző lépésből másolt előfizetési kulccsal.
1. Cserélje le a `<SAS URL>` értéket az Azure Blob Storage-tároló közös hozzáférés-aláírási (SAS) URL-címére. Az SAS URL-cím lekéréséhez nyissa meg a Microsoft Azure Storage Explorer, kattintson a jobb gombbal a tárolóra, majd válassza a **közös hozzáférésű aláírás beolvasása**elemet. Győződjön meg arról, hogy az **olvasási** és a **listázási** engedély be van jelölve, majd kattintson a **Létrehozás**gombra. Ezután másolja az értéket az **URL** szakaszban. A formátumnak a következőket kell tartalmaznia: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

A következő JSON-kimenettel `200 (Success)` választ kap:

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

Jegyezze fel a `"modelId"` értéket. Ehhez a következő lépések szükségesek.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Kulcs-érték párok és táblák kinyerése űrlapokból

Ezután elemezni fog egy dokumentumot, és Kinyeri a kulcs-érték párokat és táblákat. Hívja meg a **modell-elemzés API-** t az alábbi curl-parancs futtatásával. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le a `<Endpoint>`t az űrlap-felismerő előfizetésből beszerzett végpontra.
1. Cserélje le a `<modelID>` értéket az előző szakaszban kapott modell-AZONOSÍTÓra.
1. Cserélje le a `<path to your form>` értéket az űrlap fájljának elérési útjára (például C:\temp\file.pdf). Ebben a rövid útmutatóban a [minta adatkészletének](https://go.microsoft.com/fwlink/?linkid=2090451) **tesztelési** mappájában található fájlokat használhatja.
1. Cserélje le a `<file type>` értéket a fájl típusára. Támogatott típusok: `application/pdf`, `image/jpeg`, `image/png`.
1. A `<subscription key>` helyére írja be az előfizetési kulcsot.


```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer sikeres választ ad vissza a JSON-ban. Az űrlapból kinyert kulcs-érték párokat és táblákat jelöli:

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

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az űrlap-felismerő REST API és a cURL használatával végezte el a modell betanítását, és egy minta forgatókönyvben futtatja azt. Következő lépésként tekintse meg a dokumentációt az űrlap-felismerő API részletesebb megismeréséhez.

> [!div class="nextstepaction"]
> [REST API dokumentáció](https://aka.ms/form-recognizer/api)
