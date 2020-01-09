---
title: 'Gyors útmutató: anomáliák észlelése kötegként a REST API és a Python anomália-detektor használatával'
titleSuffix: Azure Cognitive Services
description: A rendellenesség-Kiderítő API használatával az adatsorozatban lévő rendellenességek észlelhetők kötegként vagy adatfolyamként az ebben a rövid útmutatóban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: 7cef1c8c1b05c859f9435b06c3096bb983965656
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448866"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Gyors útmutató: anomáliák észlelése az idősoros adataiban az anomália-detektor REST API és a Python használatával

Ezzel a rövid útmutatóval megkezdheti a anomáliák-Kiderítő API két észlelési módjának használatát az idősorozat-adataiban észlelt rendellenességek észlelésére. Ez a Python-alkalmazás két, JSON-formátumú idősorozat-adatokat tartalmazó API-kérelmet küld, és lekéri a válaszokat.

| API-kérelem                                        | Alkalmazás kimenete                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Rendellenességek észlelése kötegként                        | Az idősorozat-adatpontokhoz tartozó anomália-állapotot (és az egyéb adatmennyiségeket) tartalmazó JSON-válasz, valamint az észlelt rendellenességek helyei. |
| A legutóbbi adatpont anomália állapotának észlelése | Az idősorozat-adatként a legutóbbi adatponthoz tartozó anomália-állapotot (és egyéb adatértékeket) tartalmazó JSON-válasz.                                                                                                                                         |

 Habár ez az alkalmazás Pythonban íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A jelen rövid útmutató forráskódját a [githubon](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py)találja.

## <a name="prerequisites"></a>Előfeltételek

- [Python 2. x vagy 3. x](https://www.python.org/downloads/)
- Anomália-detektor kulcsa és végpontja
- A Pythonhoz készült [kérelmek kódtára](https://pypi.org/project/requests/)

- Idősorozat-adatpontokat tartalmazó JSON-fájl. A rövid útmutatóhoz tartozó példa a [githubon](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)érhető el.

### <a name="create-an-anomaly-detector-resource"></a>Anomália-detektor erőforrásának létrehozása

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]


## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. Hozzon létre egy új Python-fájlt, és adja hozzá a következő importálásokat.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Hozzon létre változókat az előfizetési kulcshoz és a végponthoz. Az alábbi URI-k használhatók a anomáliák észleléséhez. Az API-kérelmek URL-címeinek létrehozásához ezeket a rendszer később hozzáfűzi a szolgáltatási végponthoz.

    |Észlelési módszer  |URI  |
    |---------|---------|
    |Kötegelt észlelés    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Észlelés a legújabb adatponton     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Olvassa el a JSON-adatfájlt a megnyitásával és a `json.load()`használatával.

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Függvény létrehozása a kérelmek küldéséhez

1. Hozzon létre egy új, `send_request()` nevű függvényt, amely a fent létrehozott változókat veszi igénybe. Ezután hajtsa végre a következő lépéseket.

2. Hozzon létre egy szótárt a kérések fejlécéhez. Állítsa a `Content-Type` `application/json`re, és adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

3. Küldje el a kérelmet `requests.post()`használatával. Egyesítse a végpont és a anomália észlelési URL-címét a teljes kérelem URL-címéhez, és adja meg a fejléceket és a JSON-kérelmek adatait. Majd adja vissza a választ.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Rendellenességek észlelése kötegként

1. Hozzon létre egy `detect_batch()` nevű metódust, amely az összes adatrendellenességet kötegként észlelte az összes adattal. Hívja meg a fent létrehozott `send_request()` metódust a végponttal, az URL-lel, az előfizetési kulccsal és a JSON-adataival.

2. A `json.dumps()` meghívásával formázza meg az eredményt, és nyomtassa ki a konzolra.

3. Ha a válasz `code` mezőt tartalmaz, nyomtassa ki a hibakódot és a hibaüzenetet.

4. Ellenkező esetben keresse meg a rendellenességek pozícióit az adatkészletben. A válasz `isAnomaly` mező egy logikai értéket tartalmaz, amely arra vonatkozik, hogy egy adott adatpont rendellenesség-e. Ismételje meg a listát, és nyomtassa ki a `True`i értékek indexét. Ezek az értékek a rendellenes adatpontok indexének felelnek meg, ha vannak ilyenek.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legutóbbi adatpont anomália állapotának észlelése

1. Hozzon létre egy `detect_latest()` nevű metódust annak megállapításához, hogy az idősorozat legfrissebb adatpontja anomália-e. Hívja meg a fenti `send_request()` metódust a végponttal, az URL-lel, az előfizetési kulccsal és a JSON-adataival. 

2. A `json.dumps()` meghívásával formázza meg az eredményt, és nyomtassa ki a konzolra.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>A kérelem elküldése

Hívja meg a fent létrehozott anomáliák észlelési módszereit.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Példaválasz

A sikeres válaszokat JSON formátumban adja vissza a rendszer. Az alábbi hivatkozásokra kattintva megtekintheti a JSON-választ a GitHubon:
* [Példa a Batch észlelési válaszára](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Példa a legutóbbi pont észlelési válaszára](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
