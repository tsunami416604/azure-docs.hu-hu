---
title: 'Rövid útmutató: Anomáliák észlelése kötegként az Anomália-detektor REST API és a Python használatával'
titleSuffix: Azure Cognitive Services
description: Az Anomáliadetektor API-val észlelheti az adatsorok rendellenességeit kötegként vagy streamelési adatokon ebben a rövid útmutatóban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 684aba561dc50b64dd7cc564cff8e55229ce1429
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239041"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Rövid útmutató: Az anomáliadetektor REST API-jával és a Pythonnal észleli az idősorozat-adatok anomáliáinak anomáliáit

Ezzel a rövid útmutatóval elkezdheti használni az Anomália-detektor API két észlelési módját az idősorozat-adatok anomáliáinak észleléséhez. Ez a Python-alkalmazás két API-kérelmet küld, amelyek JSON-formátumú idősorozat-adatokat tartalmaznak, és lekéri a válaszokat.

| API-kérelem                                        | Alkalmazás kimenete                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Anomáliák észlelése kötegként                        | A JSON-válasz, amely az idősorozat-adatok minden egyes adatpontjának anomáliaállapotát (és egyéb adatait) és az észlelt anomáliák helyzetét tartalmazza. |
| A legújabb adatpont anomáliaállapotának észlelése | Az idősorozat-adatok legfrissebb adatpontjának anomáliaállapotát (és egyéb adatait) tartalmazó JSON-válasz.                                                                                                                                         |

 Bár ez az alkalmazás python nyelven íródott, az API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A rövid útmutató forráskódját a [GitHubon](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py)találja.

## <a name="prerequisites"></a>Előfeltételek

- [Python 2.x vagy 3.x](https://www.python.org/downloads/)
- Anomáliadetektor kulcsa és végpontja
- A [python kérések könyvtára](https://pypi.org/project/requests/)

- Idősorozat-adatpontokat tartalmazó JSON-fájl. A rövid útmutató példaadatai a [GitHubon](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)találhatók.

### <a name="create-an-anomaly-detector-resource"></a>Anomáliadetektor-erőforrás létrehozása

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]


## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. Hozzon létre egy új python fájlt, és adja hozzá a következő importálást.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Hozzon létre változókat az előfizetési kulcshoz és a végponthoz. Az alábbiakban az anomáliadetektáláshoz használható URI-k láthatók. Ezeket később hozzáfűzi a szolgáltatás végpontjának az API-kérelem URL-címének létrehozásához.

    |Észlelési módszer  |URI  |
    |---------|---------|
    |Kötegfelismerés    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Észlelés a legújabb adatponton     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Olvassa el a JSON-adatfájlban `json.load()`a megnyitásával és a használatával.

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Kérelemküldési funkció létrehozása

1. Hozzon létre `send_request()` egy új függvényt, amely a fent létrehozott változókat veszi figyelembe. Ezután hajtsa végre a következő lépéseket.

2. Hozzon létre egy szótárat a kérelemfejlécek számára. Állítsa `Content-Type` be `application/json`a t, és `Ocp-Apim-Subscription-Key` adja hozzá az előfizetési kulcsot a fejléchez.

3. Küldje el `requests.post()`a kérelmet a használatával. Kombinálja a végpontot és az anomáliaészlelési URL-címet a teljes kérelem URL-címéhez, és adja meg a fejléceket és a json kérelem adatait. És akkor vissza a választ.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Anomáliák észlelése kötegként

1. Hozzon létre `detect_batch()` egy metódust, amelynek célja az adatok anomáliák kötegként való észleléséhez. Hívja `send_request()` meg a fenti metódust a végpont, url, előfizetési kulcs és json adatokat.

2. Az `json.dumps()` eredmény formázásához hívja meg az eredményt, és nyomtassa ki a konzolra.

3. Ha a `code` válasz mezőt tartalmaz, nyomtassa ki a hibakódot és a hibaüzenetet.

4. Ellenkező esetben keresse meg az anomáliák helyét az adatkészletben. A válasz `isAnomaly` mezője logikai értéket tartalmaz, amely arra vonatkozik, hogy egy adott adatpont anomália-e. Végighaladhat a listán, és kinyomtathatja az `True` értékek indexét. Ezek az értékek megfelelnek a rendellenes adatpontok indexének, ha találhatók ilyenek.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legújabb adatpont anomáliaállapotának észlelése

1. Hozzon létre `detect_latest()` egy metódust, amely megállapítja, hogy az idősorozat legújabb adatpontja anomália-e. Hívja `send_request()` meg a fenti módszert a végpont, url, előfizetési kulcs és json adatokat. 

2. Az `json.dumps()` eredmény formázásához hívja meg az eredményt, és nyomtassa ki a konzolra.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>A kérelem elküldése

Hívja meg a fent létrehozott anomáliadetektálási módszereket.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Példaválasz

A sikeres választ JSON formátumban adja vissza. Kattintson az alábbi linkekre a JSON-válasz megtekintéséhez a GitHubon:
* [Példa kötegészlelési válaszra](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Példa a legutóbbi pontészlelési válaszra](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
