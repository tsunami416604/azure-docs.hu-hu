---
title: 'Gyors útmutató: bevételezési adatok kinyerése a cURL-Form felismerővel'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az űrlap-felismerő REST API és a cURL használatával gyűjti össze az USA értékesítési nyugtáinak képeiből származó adatok mennyiségét.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 8b0e7d717ed5c06600adf261f7ce5b44f572adf9
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774262"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Gyors útmutató: bevételezési adatok kinyerése az űrlap-felismerő REST API és a cURL használatával

Ebben a rövid útmutatóban az Azure űrlap-felismerő REST API és a cURL használatával kinyerheti és azonosíthatja a releváns információkat az USA értékesítési nyugtáján.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!IMPORTANT]
> Ez a rövid útmutató az űrlap felismerő v 2.0 API-ját használja. Ha az előfizetése nem a `West US 2` vagy `West Europe` régióban található, akkor a v 1.0 API-t kell használnia. Ehelyett kövesse az [1.0](./curl-train-extract-v1.md) -s verziót.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőket kell tennie:
- Hozzáférés az űrlap-felismerő korlátozott hozzáférésének előzetes verziójához. Az előzetes verzió eléréséhez töltse ki és küldje el az [űrlap-felismerő hozzáférési kérelmének](https://aka.ms/FormRecognizerRequestAccess) űrlapját.
- a [fürt](https://curl.haxx.se/windows/) telepítve van.
- Egy nyugtát ábrázoló rendszerkép URL-címe. Ehhez a rövid útmutatóhoz [minta képet](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg?raw=true) is használhat.

## <a name="create-a-form-recognizer-resource"></a>Űrlap-felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Visszaigazolás elemzése

A nyugták elemzésének megkezdéséhez hívja meg az **[elemzés visszaigazolása](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeReceiptAsync)** API-t az alábbi curl-parancs használatával. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le a `<Endpoint>`t arra a végpontra, amelyet az űrlap-felismerő előfizetésével kapott.
1. Cserélje le a `<your receipt URL>`t egy nyugtás rendszerkép URL-címére.
1. Cserélje le a `<subscription key>`t az előző lépésből másolt előfizetési kulccsal.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

`202 (Success)` választ fog kapni, amely tartalmazza a am **Operation-Location** fejlécet. A fejléc értéke olyan műveleti azonosítót tartalmaz, amelynek segítségével lekérdezheti az aszinkron művelet állapotát, és lekérheti az eredményeket. A következő példában a `operations/` utáni karakterlánc a művelet azonosítója.

```console
https://cognitiveservice/formrecognizer/v2.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>A Bevételezés eredményeinek beolvasása

Az **elemzés visszaigazolási** API meghívása után hívja meg az **[elemzés visszaigazolásának eredményét](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeReceiptResult)** API-t a művelet és a kinyert adatmennyiség állapotának lekéréséhez. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le a `<Endpoint>`t arra a végpontra, amelyet az űrlap-felismerő előfizetési kulcsával kapott. Az űrlap-felismerő erőforrás- **Áttekintés** lapon találhatja meg.
1. Cserélje le a `<operationId>`t az előző lépésben megadott műveleti AZONOSÍTÓra.
1. A `<subscription key>` helyére írja be az előfizetési kulcsot.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/prebuilt/receipt/analyzeResults/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>A válasz vizsgálata

A JSON-kimenettel `200 (Success)` választ kap. Az első mező, `"status"`, a művelet állapotát jelzi. Ha a művelet befejeződött, a `"recognitionResults"` mező a beérkezésből kinyert szöveg minden sorát tartalmazza, és a `"understandingResults"` mező a Bevételezés legfontosabb részeinek kulcs/érték adatait tartalmazza. Ha a művelet nem fejeződött be, a `"status"` értéke `"running"` vagy `"notStarted"`lesz, és újra meg kell hívnia az API-t manuálisan vagy parancsfájlon keresztül. Javasoljuk, hogy a hívások között egy másodperc vagy több intervallum legyen.

Tekintse meg az alábbi beérkezési képet és a hozzá tartozó JSON-kimenetet. A kimenet le lett rövidítve az olvashatóság érdekében.

![A contoso áruházból érkezett visszaigazolás](../media/contoso-allinone.jpg)

Az `"recognitionResults"` csomópont tartalmazza az összes felismert szöveget. A szöveget az oldal, a sor, majd az egyes szavak szerint rendezi. A `"understandingResults"` csomópont tartalmazza a modell által felderített bevételezés-specifikus értékeket. Itt talál hasznos kulcs/érték párokat, mint például az adó, a teljes, a kereskedelmi címek és így tovább.

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

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az űrlap-felismerő REST APIt használta a cURL használatával egy értékesítési visszaigazolás tartalmának kinyeréséhez. Következő lépésként tekintse meg a dokumentációt az űrlap-felismerő API részletesebb megismeréséhez.

> [!div class="nextstepaction"]
> [REST API dokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeReceiptAsync)
