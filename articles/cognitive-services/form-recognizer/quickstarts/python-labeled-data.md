---
title: 'Rövid útmutató: A REST API és a Python használatával címkékkel való betanítása – űrlapfelismerő'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja a Form Recognizer címkézett adatfunkciót a REST API-val és a Pythonnal egy egyéni modell betanításához.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 36ded3bd85cd7acdffbfe46b9e931a811994fa30
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531100"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Űrlapfelismerő modell betanítása feliratokkal REST API és Python használatával

Ebben a rövid útmutatóban a Form Recognizer REST API-t a Python nal fogja használni egy egyéni modell manuálisan címkézett adatokkal történő betanításához. Tekintse meg a [Vonat címkékkel](../overview.md#train-with-labels) szakaszaz áttekintés, hogy többet tudjon meg ezt a funkciót.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához a következőkre van szüksége:
- [Python](https://www.python.org/downloads/) telepítve (ha helyileg szeretné futtatni a mintát).
- Legalább hat azonos típusú formaból áll. Ezeket az adatokat a modell betanításához és egy űrlap teszteléséhez fogja használni. Ehhez a rövid útmutatóhoz [mintaadatkészletet](https://go.microsoft.com/fwlink/?linkid=2090451) használhat. Töltse fel a betanítási fájlokat egy blob storage-tároló egy Azure Storage-fiók ban.

## <a name="create-a-form-recognizer-resource"></a>Űrlapfelismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Betanítási adatok beállítása

Ezután be kell állítania a szükséges bemeneti adatokat. A címkézett adatszolgáltatás az egyéni modell betanításához szükséges követelményeken túlmutató speciális beviteli követelményekkel rendelkezik. 

Győződjön meg arról, hogy az összes képzési dokumentum azonos formátumú. Ha az űrlapok több formátumban vannak, a közös formátum alapján rendezze őket almappákba. A betanításkor az API-t egy almappába kell irányítania.

Annak érdekében, hogy a címkeadatok használatával modelleket tanítson be, a következő fájlokra lesz szüksége bemenetként az almappában. Az alábbiakban megtudhatja, hogyan hozhat létre ilyen fájlt.

* **Forrásűrlapok** – az űrlapok, amelyekből adatokat lehet kinyerni. A támogatott típusok a JPEG, PNG, PDF vagy TIFF.
* **OCR elrendezés fájlok** - JSON fájlokat, amelyek leírják a méretek és pozíciók minden olvasható szöveg minden forrás formában. Az adatok létrehozásához az Űrlapfelismerő elrendezés API-t fogja használni. 
* **Címke fájlok** - JSON fájlokat, amelyek leírják adatcímkék, amelyek a felhasználó által megadott kézzel.

Az összes ilyen fájlnak ugyanazt az almappát kell elfoglalnia, és a következő formátumban kell lennie:

* input_file1.pdf 
* input_file1.pdf.ocr.json
* input_file1.pdf.labels.json 
* input_file2.pdf 
* input_file2.pdf.ocr.json
* input_file2.pdf.labels.json
* ...

> [!TIP]
> Ha az űrlapokat az Űrlapfelismerő [mintacímkéző eszközzel címkézi,](./label-tool.md)az eszköz automatikusan létrehozza ezeket a címke- és OCR-elrendezésfájlokat.

### <a name="create-the-ocr-output-files"></a>Az OCR kimeneti fájlok létrehozása

OCR-eredményfájlokra van szükség ahhoz, hogy a szolgáltatás figyelembe vehesse a megfelelő bemeneti fájlokat a címkézett betanításhoz. Egy adott forrásűrlap OCR-eredményeinek eléréséhez kövesse az alábbi lépéseket:

1. Hívja meg az **[Elemzési elrendezés](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** API-t az olvasási elrendezés tárolóban a bemeneti fájllal a kérelem törzsének részeként. Mentse a válasz Művelet-hely fejlécében található **azonosítót.**
1. Hívja meg a **[Get Analyze Layout Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** API-t az előző lépés műveletazonosítójának használatával.
1. A válasz beírása és a tartalom beírása egy fájlba. A megfelelő OCR-fájlhoz minden forrásűrlaphoz hozzá kell `.ocr.json`fűznie az eredeti fájlnevet. Az OCR JSON kimenetnek a következő formátummal kell rendelkeznie. Tekintse meg a [minta OCR fájlt](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json) egy teljes példa. 

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

### <a name="create-the-label-files"></a>Címkefájlok létrehozása

A címkefájlok olyan kulcsérték-társításokat tartalmaznak, amelyeket a felhasználó manuálisan adott meg. Ezek szükségesek a címkézett adatbetanításhoz, de nem minden forrásfájlnak kell egy megfelelő címkefájlhoz. A címkék nélküli forrásfájlokat a rendszer rendes képzési dokumentumként kezeli. A megbízható betanításhoz öt vagy több címkézett fájlt ajánlunk.

Címkefájl létrehozásakor tetszés szerint&mdash;megadhatja a dokumentum értékeinek pontos helyét. Ez ad a képzés még nagyobb pontosságot. A területek formázása nyolc értékből áll, amelyek négy X,Y koordinátának felelnek meg: bal felső, jobb felső, jobb alsó és bal alsó érték. A koordinátaértékek nulla és egy között vannak, az oldal méreteinek méretére méretezve.

A megfelelő címkefájlhoz minden forrásűrlaphoz hozzá kell `.labels.json`fűzni az eredeti fájlnevet. A címkefájlnak a következő formátummal kell rendelkeznie. Tekintse meg a [mintacímke-fájlt](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json) egy teljes példát.

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

> [!NOTE]
> Minden szövegelemre csak egy címke alkalmazható, és minden címke oldalanként csak egyszer alkalmazható. Jelenleg nem alkalmazhat címkét több oldalra.


## <a name="train-a-model-using-labeled-data"></a>Modell betanítása címkézett adatok használatával

A modell címkézett adatokkal való betanításához hívja meg a **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** API-t a következő python-kód futtatásával. A kód futtatása előtt hajtsa végre a következő módosításokat:

1. Cserélje `<Endpoint>` le az űrlapfelismerő erőforrás végpontjának URL-címét.
1. Cserélje `<SAS URL>` le az Azure Blob storage tároló megosztott hozzáférésű aláírás (SAS) URL-címét. A SAS URL-címének beolvasásához nyissa meg a Microsoft Azure Storage Exploreralkalmazást, kattintson a jobb gombbal a tárolóra, és válassza **a Megosztott hozzáférésű aláírás beolvasása parancsot.** Ellenőrizze, hogy az **Olvasás** és **a Lista** engedélyek be vannak-e jelölve, majd kattintson a **Létrehozás gombra.** Ezután másolja az **URL-cím** szakasz értékét. Meg kell a `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`forma: .
1. Cserélje `<Blob folder name>` le a mappa nevét a blob tárolóban, ahol a bemeneti adatok találhatók. Ha az adatok a gyökérben vannak, hagyja `"prefix"` üresen ezt a mezőt, és távolítsa el a mezőt a HTTP-kérelem törzséből.

```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0-preview/custom/models"
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

Amikor a betanítási folyamat befejeződött, a json-tartalommal `201 (Success)` kapcsolatos választ kap, például az alábbiakat. A válasz az egyszerűség kedvéért rövidült.

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

Amikor a folyamat befejeződött, a `202 (Success)` JSON-tartalommal kapcsolatos választ kap a következő formátumban. A válasz az egyszerűség kedvéért rövidült. A fő kulcs/érték társítások a `"documentResults"` csomópontban találhatók. Az Elrendezés API-eredmények (a dokumentum teljes szövegének tartalma `"readResults"` és pozíciója) a csomóponton találhatók.

```json
{ 
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
                0.5375,
                1.1349,
                2.6064,
                1.1349,
                2.6064,
                1.354,
                0.5375,
                1.354
              ],
              "text":"Contoso Suites",
              "words":[ 
                { 
                  "boundingBox":[ 
                    0.5375,
                    1.1402,
                    1.6595,
                    1.1402,
                    1.6595,
                    1.354,
                    0.5375,
                    1.354
                  ],
                  "text":"Contoso",
                  "confidence":1
                },
                { 
                  "boundingBox":[ 
                    1.758,
                    1.1349,
                    2.6064,
                    1.1349,
                    2.6064,
                    1.3534,
                    1.758,
                    1.3534
                  ],
                  "text":"Suites",
                  "confidence":1
                }
              ]
            },
            ...
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
                    "#/readResults/0/lines/7/words/0",
                    "#/readResults/0/lines/7/words/1"
                  ]
                },
                { 
                  "rowIndex":0,
                  "columnIndex":1,
                  "text":"Invoice Date",
                  "boundingBox":[ 
                    1.9061,
                    2.8088,
                    3.3074,
                    2.8088,
                    3.3074,
                    3.3219,
                    1.9061,
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
      ],
      "documentResults":[ 
        { 
          "docType":"Analyze",
          "pageRange":[ 
            1,
            1
          ],
          "fields":{ 
            "total":{ 
              "type":"string",
              "valueString":"$22,123.24",
              "text":"$22,123.24",
              "boundingBox":[ 
                5.29,
                3.41,
                5.9750000000000009,
                3.41,
                5.9750000000000009,
                3.54,
                5.29,
                3.54
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/15/words/0"
                }
              ],
              "fieldName":"total"
            },
            "invoice #":{ 
              "type":"string",
              "valueString":"7689302",
              "text":"7689302",
              "boundingBox":[ 
                0.54,
                3.41,
                1.065,
                3.41,
                1.065,
                3.515,
                0.54,
                3.515
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/12/words/0"
                }
              ],
              "fieldName":"invoice #"
            },
            "vat":{ 
              "type":"string",
              "valueString":"QR",
              "text":"QR",
              "boundingBox":[ 
                6.2250000000000009,
                3.41,
                6.425,
                3.41,
                6.425,
                3.52,
                6.2250000000000009,
                3.52
              ],
              "page":1,
              "confidence":0.9839357733726502,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/16/words/0"
                }
              ],
              "fieldName":"vat"
            },
            ...
          }
        }
      ]
    },
    "status":"succeeded",
    "createdDateTime":"2019-11-12T21:26:19+00:00",
    "lastUpdatedDateTime":"2019-11-12T21:27:27.0488571+00:00"
}
```

## <a name="improve-results"></a>Az eredmények javítása

Vizsgálja `"confidence"` meg az egyes kulcs/érték `"documentResults"` eredmény értékeit a csomópont alatt. Azt is meg kell néznie `"readResults"` a megbízhatósági pontszámok a csomópont, amely megfelel az elrendezés művelet. Az elrendezési eredmények megbízhatósága nem befolyásolja a kulcs/érték kinyerési eredményének megbízhatóságát, ezért mindkettőt ellenőrizze.
* Ha az Elrendezés művelet megbízhatósági pontszámai alacsonyak, próbálja meg javítani a bemeneti dokumentumok minőségét [(lásd: Bemeneti követelmények).](../overview.md#input-requirements)
* Ha a kulcs/érték kinyerési művelet megbízhatósági pontszámai alacsonyak, győződjön meg arról, hogy az elemzett dokumentumok azonos típusúak, mint a betanítási készletben használt dokumentumok. Ha a betanítási készlet dokumentumai megjelenésük eltérő, fontolja meg a különböző mappákra való felosztásukat, és minden változathoz betanítási modellt.

### <a name="avoid-cluttered-labels"></a>Kerülje a zsúfolt címkéket

Néha, ha különböző címkéket alkalmaz ugyanazon a szövegsoron belül, a szolgáltatás ezeket a címkéket egyetlen mezőbe egyesítheti. Egy címben például a várost, az államot és az irányítószámot különböző mezőkként címkézheti, de az előrejelzés során ezeket a mezőket a rendszer nem ismeri fel külön.

Megértjük, hogy ez a forgatókönyv alapvető fontosságú ügyfeleink számára, és dolgozunk ennek a jövőben immár fejlesztésén. Jelenleg azt javasoljuk a felhasználóknak, hogy több zsúfolt mezőt egy mezőként címkézze nek fel, majd különítse el a kifejezéseket a kibontási eredmények utófeldolgozásában.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Form Recognizer REST API-t a Pythonnal a modell manuálisan címkézett adatokkal történő betanításához. Ezután tekintse meg az [API referenciadokumentációját](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) a Form Recognizer API részletesebb feltárásához.
