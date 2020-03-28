---
title: 'Rövid útmutató: Modell betanítása és űrlapadatok kinyerése cURL használatával - Űrlapfelismerő'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban a formfelismerő REST API-t cURL-lel fogja használni egy modell betanításához és az űrlapok ból származó adatok kinyeréséhez.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 32756187852de0834afc1dc034d3f7419f0c8087
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118393"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Rövid útmutató: Űrlapfelismerő modell betanítása és űrlapadatok kinyerése a REST API cURL használatával

Ebben a rövid útmutatóban az Azure Form Recognizer REST API cURL-lel a kulcs-érték párok és táblák kinyeréséhez használja az űrlapokat és a pontozást.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához a következőkre van szüksége:
- [cURL](https://curl.haxx.se/windows/) telepítve.
- Legalább hat azonos típusú formaból áll. Ezek közül ötöt fogsz használni a modell betanításához, majd teszteled a hatodik űrlappal. Az űrlapok különböző fájltípusok lehetnek, de azonos típusú dokumentumoknak kell lenniük. Ehhez a rövid útmutatóhoz [mintaadatkészletet](https://go.microsoft.com/fwlink/?linkid=2090451) használhat. Töltse fel a betanítási fájlokat egy blob storage-tároló egy Azure Storage-fiók ban. A tesztfájlokat külön mappába helyezheti.

## <a name="create-a-form-recognizer-resource"></a>Űrlapfelismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Űrlapfelismerő modell betanítása

Először is szüksége lesz egy betanítási adatok egy Azure Storage-blobban egy készlet. Legalább öt kitöltött űrlapnak (PDF-dokumentumnak és/vagy képnek) kell lennie, amelyek típusa/szerkezete megegyezik a fő bemeneti adatokéval. Vagy használhat egyetlen üres űrlapot két kitöltött űrlappal. Az üres űrlap fájlnevének tartalmaznia kell az "üres" szót. Lásd: [Betanítási adatkészlet létrehozása egyéni modellhez](../build-training-data-set.md) tippeket és lehetőségeket a betanítási adatok összeállításához.

> [!NOTE]
> A címkézett adatfunkció segítségével manuálisan címkézheti a betanítási adatok egy részét vagy egészét. Ez egy összetettebb folyamat, de egy jobban képzett modellt eredményez. Tekintse meg a [Vonat címkékkel](../overview.md#train-with-labels) szakaszaz áttekintés, hogy többet tudjon meg ezt a funkciót.

Űrlapfelismerő modell betanításához az Azure blob tárolójában lévő dokumentumokkal, hívja meg a **[Betanítási egyéni modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** API-t a következő cURL parancs futtatásával. A parancs futtatása előtt hajtsa végre a következő módosításokat:

1. Cserélje `<Endpoint>` le a Form Recognizer-előfizetéssel kapott végpontra.
1. Cserélje `<subscription key>` le az előző lépésből másolt előfizetési kulcsot.
1. Cserélje `<SAS URL>` le az Azure Blob storage tároló megosztott hozzáférésű aláírás (SAS) URL-címét. A SAS URL-címének beolvasásához nyissa meg a Microsoft Azure Storage Exploreralkalmazást, kattintson a jobb gombbal a tárolóra, és válassza **a Megosztott hozzáférésű aláírás beolvasása parancsot.** Ellenőrizze, hogy az **Olvasás** és **a Lista** engedélyek be vannak-e jelölve, majd kattintson a **Létrehozás gombra.** Ezután másolja az **URL-cím** szakasz értékét. Meg kell a `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`forma: .

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

A `201 (Success)` **helyfejléces** választ kapja. A fejléc értéke a betanított új modell azonosítója. 

## <a name="get-training-results"></a>Képzési eredmények beszerezni

Miután elindította a vonat működését, egy új művelet, **[Az egyéni modell beszerezni](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetCustomModel)** e betanítási állapotának ellenőrzéséhez használja. Adja át a modellazonosítót az API-hívásba a betanítási állapot ellenőrzéséhez:

1. Cserélje `<Endpoint>` le a kapott végpontra az Űrlapfelismerő előfizetési kulccsal.
1. Csere `<subscription key>` az előfizetési kulcsra
1. Csere `<model ID>` az előző lépésben kapott modellazonosítóra

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

A `200 (Success)` következő formátumban kap választ egy JSON-törzsnél. Figyelje `"status"` meg a mezőt. Ez lesz az `"ready"` érték, ha a képzés befejeződött. Ha a modell nem fejeződött be a betanítás, a parancs ismételt futtatásával újra le kell kérdeznie a szolgáltatást. Azt javasoljuk, hogy a hívások között egy másodperces vagy több időközt.

A `"modelId"` mező tartalmazza a betanítás alatt álló modell azonosítóját. Erre szükséged lesz a következő lépéshez.

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

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Kulcsérték-párok és táblák űrlapjainak elemzése

Ezután az újonnan betanított modell segítségével elemezhet egy dokumentumot, és kulcsérték-párokat és táblákat nyerhet ki belőle. Hívja meg az **[Űrlap elemzése](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** API-t a következő cURL parancs futtatásával. A parancs futtatása előtt hajtsa végre a következő módosításokat:

1. Cserélje `<Endpoint>` le az Űrlapfelismerő előfizetési kulcsból kapott végpontra. Ezt az Űrlapfelismerő erőforrás **áttekintése** lapon találja.
1. Cserélje `<model ID>` le az előző szakaszban kapott modellazonosítóra.
1. Cserélje `<SAS URL>` le egy SAS URL-címet a fájlhoz az Azure storage-ban. Kövesse a betanítási szakasz lépéseit, de ahelyett, hogy a teljes blobtároló SAS-URL-címét kapna, szerezzen be egyet az elemezni kívánt fájlhoz.
1. A `<subscription key>` helyére írja be az előfizetési kulcsot.

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

A `202 (Success)` **művelet-hely fejléccel** választ fog kapni. A fejléc értéke tartalmazza az Elemzés művelet eredményeinek nyomon követéséhez használt eredményazonosítót. Mentse ezt az eredményazonosítót a következő lépéshez.

## <a name="get-the-analyze-results"></a>Az Elemzés eredményeinek beszereznie

A következő API-val lekérdezheti az Elemzés művelet eredményeit.

1. Cserélje `<Endpoint>` le az Űrlapfelismerő előfizetési kulcsból kapott végpontra. Ezt az Űrlapfelismerő erőforrás **áttekintése** lapon találja.
1. Cserélje `<result ID>` le az előző szakaszban kapott azonosítóra.
1. A `<subscription key>` helyére írja be az előfizetési kulcsot.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

A `200 (Success)` következő formátumban kap választ egy JSON-törzsnél. A kimenet az egyszerűség kedvéért lerövidült. Figyelje `"status"` meg a mező alján. Ez lesz az `"succeeded"` érték, ha az Elemzés művelet befejeződött. Ha az Elemzés művelet nem fejeződött be, a parancs ismételt futtatásával újra le kell kérdeznie a szolgáltatást. Azt javasoljuk, hogy a hívások között egy másodperces vagy több időközt.

A fő kulcs/értékpár társítások `"pageResults"` és táblák a csomópontban találhatók. Ha az *includeTextDetails* URL-paraméteren keresztül is megadtál egyszerű szövegkinyerést, akkor a `"readResults"` csomópont a dokumentum teljes szövegének tartalmát és pozícióját jeleníti meg.

```json
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

Ebben a rövid útmutatóban a formfelismerő REST API-t cURL-lel használta egy modell betanításához és mintaforgatókönyvben való futtatásához. Ezután tekintse meg a referenciadokumentációt a Form Recognizer API részletesebb megismeréséhez.

> [!div class="nextstepaction"]
> [REST API referenciadokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
