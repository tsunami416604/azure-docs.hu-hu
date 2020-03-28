---
title: 'Rövid útmutató: Nyugtaadatok kinyerése cURL használatával - Űrlapfelismerő'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban a Form Recognizer REST API cURL-lel az USA értékesítési nyugtáinak lemezképeiből nyerheti ki az adatokat.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: e053222d3b79668c2f6044417e31e104ce0f4222
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118493"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Rövid útmutató: Nyugtaadatok kinyerése a FORM Recognizer REST API-val cURL-lel

Ebben a rövid útmutatóban az Azure Form Recognizer REST API-t cURL-lel fogja használni a releváns információk kinyeréséhez és azonosításához az USA értékesítési nyugtáiban.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához a következőkre van szüksége:
- [cURL](https://curl.haxx.se/windows/) telepítve.
- A nyugta képének URL-címe. Ehhez a rövid útmutatóhoz [mintaképet](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg?raw=true) használhat.

## <a name="create-a-form-recognizer-resource"></a>Űrlapfelismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Bevételezés elemzése

A nyugta elemzésének megkezdéséhez hívja meg a **[Nyugta elemzése](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeReceiptAsync)** API-t az alábbi cURL paranccsal. A parancs futtatása előtt hajtsa végre a következő módosításokat:

1. Cserélje `<Endpoint>` le a Form Recognizer-előfizetéssel kapott végpontra.
1. Cserélje `<your receipt URL>` le a nyugtakép URL-címére.
1. Cserélje `<subscription key>` le az előző lépésből másolt előfizetési kulcsot.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

Olyan `202 (Success)` választ fog kapni, amely tartalmazza az am **Operation-Location** fejlécet. A fejléc értéke egy műveleti azonosítót tartalmaz, amellyel lekérdezheti az aszinkron művelet állapotát, és lekaphatja az eredményeket. A következő példában az `operations/` ezt követő karakterlánc a műveletazonosító.

```console
https://cognitiveservice/formrecognizer/v2.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>A nyugta eredmények begyűjtése

Miután meghívta a **Nyugta elemzési** api-t, hívja meg a **[Get Analyze Receipt Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeReceiptResult)** API-t a művelet állapotának és a kinyert adatoknak a lekéréséhez. A parancs futtatása előtt hajtsa végre a következő módosításokat:

1. Cserélje `<Endpoint>` le a kapott végpontra az Űrlapfelismerő előfizetési kulccsal. Ezt az Űrlapfelismerő erőforrás **áttekintése** lapon találja.
1. Cserélje `<operationId>` le az előző lépés műveleti azonosítójára.
1. A `<subscription key>` helyére írja be az előfizetési kulcsot.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/prebuilt/receipt/analyzeResults/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>A válasz vizsgálata

A JSON `200 (Success)` kimenettel választ fog kapni. Az első `"status"`mező , a művelet állapotát jelzi. Ha a művelet befejeződött, a mező tartalmazza a `"recognitionResults"` bevételezésből `"understandingResults"` kinyert összes szövegsort, és a mező a bevételezés legfontosabb részeire vonatkozó kulcs/érték információkat tartalmazza. Ha a művelet nem fejeződött `"status"` be, az értéke lesz, `"running"` vagy `"notStarted"`, és meg kell hívni a API-t újra, akár manuálisan, vagy egy parancsfájlon keresztül. Azt javasoljuk, hogy a hívások között egy másodperces vagy több időközt.

Tekintse meg a következő nyugtaképet és a hozzá tartozó JSON-kimenetet. A kimenet lerövidült az olvashatóság érdekében.

![Nyugta a Contoso áruházból](../media/contoso-allinone.jpg)

A `"recognitionResults"` csomópont tartalmazza az összes felismert szöveget. A szöveg oldalak, majd sorok, majd szavak szerint van rendezve. A `"understandingResults"` csomópont tartalmazza a modell által felderített bevételezés-specifikus értékeket. Ez az, ahol talál hasznos kulcs / érték párok, mint az adó, összesen, kereskedő címét, és így tovább.

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[ 
      { 
        "docType":"prebuilt:receipt",
        "pageRange":[ 
          1,
          1
        ],
        "fields":{ 
          "ReceiptType":{ 
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{ 
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[ 
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[ 
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{ 
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[ 
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{ 
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[ 
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{ 
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[ 
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{ 
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[ 
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[ 
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{ 
            "type":"array",
            "valueArray":[ 
              { 
                "type":"object",
                "valueObject":{ 
                  "Quantity":{ 
                    "type":"number",
                    "text":"1",
                    "boundingBox":[ 
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{ 
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[ 
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{ 
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[ 
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[ 
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{ 
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[ 
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[ 
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{ 
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[ 
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[ 
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{ 
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[ 
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[ 
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{ 
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[ 
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[ 
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a Form Recognizer REST API-t cURL-lel használta az értékesítési nyugta tartalmának kinyeréséhez. Ezután tekintse meg a referenciadokumentációt a Form Recognizer API részletesebb megismeréséhez.

> [!div class="nextstepaction"]
> [REST API referenciadokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeReceiptAsync)
