---
title: 'Gyors útmutató: Anomáliák észlelése kötegként az anomália-detektor REST API és a Python használatával'
titleSuffix: Azure Cognitive Services
description: A rendellenesség-Kiderítő API használatával az adatsorozatban lévő rendellenességeket kötegként vagy adatfolyamként lehet érzékelni.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: f40f1b94b3e7c2732fd8bed0bc6e503277b533c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565820"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Gyors útmutató: Az idősoros adataiban észlelt rendellenességek észlelése az anomália REST API és a Python használatával

Ezzel a rövid útmutatóval megkezdheti a anomáliák-Kiderítő API két észlelési módjának használatát az idősorozat-adataiban észlelt rendellenességek észlelésére. Ez a Python-alkalmazás két, JSON-formátumú idősorozat-adatokat tartalmazó API-kérelmet küld, és lekéri a válaszokat.

| API-kérelem                                        | Alkalmazás kimenete                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Rendellenességek észlelése kötegként                        | Az idősorozat-adatpontokhoz tartozó anomália-állapotot (és az egyéb adatmennyiségeket) tartalmazó JSON-válasz, valamint az észlelt rendellenességek helyei. |
| A legutóbbi adatpont anomália állapotának észlelése | Az idősorozat-adatként a legutóbbi adatponthoz tartozó anomália-állapotot (és egyéb adatértékeket) tartalmazó JSON-válasz.                                                                                                                                         |

 Habár ez az alkalmazás Pythonban íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

## <a name="prerequisites"></a>Előfeltételek

- [Python 2. x vagy 3. x](https://www.python.org/downloads/)

- A Pythonhoz készült [kérelmek kódtára](http://docs.python-requests.org)

- Idősorozat-adatpontokat tartalmazó JSON-fájl. A rövid útmutatóhoz tartozó példa a githubon érhető [](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)el.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. Hozzon létre egy új Python-fájlt a kedvenc szövegszerkesztőben vagy az IDE-ben. Adja hozzá a következő importálásokat.

    ```python
    import requests
    import json
    ```

2. Hozzon létre változókat az előfizetési kulcshoz és a végponthoz. Az alábbi URI-k használhatók a anomáliák észleléséhez. Az API-kérelmek URL-címeinek létrehozásához ezeket a rendszer később hozzáfűzi a szolgáltatási végponthoz.

    |Észlelési módszer  |URI  |
    |---------|---------|
    |Kötegelt észlelés    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Észlelés a legújabb adatponton     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Olvassa el a JSON-adatfájlt a megnyitásával és `json.load()`a használatával.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Függvény létrehozása a kérelmek küldéséhez

1. Hozzon létre egy nevű `send_request()` új függvényt, amely a fent létrehozott változókat veszi igénybe. Ezután hajtsa végre a következő lépéseket.

2. Hozzon létre egy szótárt a kérések fejlécéhez. Állítsa be `Content-Type` a `application/json`-t, és adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

3. Küldje el a kérelmet `requests.post()`a paranccsal. Egyesítse a végpont és a anomália észlelési URL-címét a teljes kérelem URL-címéhez, és adja meg a fejléceket és a JSON-kérelmek adatait. Majd adja vissza a választ.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json',
               'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(
        endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Rendellenességek észlelése kötegként

1. Hozzon létre egy `detect_batch()` metódust, amely egy kötegként észleli a rendellenességeket az összes adategységben. Hívja meg `send_request()` a fent létrehozott metódust a végponttal, az URL-lel, az előfizetési kulccsal és a JSON-adataival.

2. A `json.dumps()` formázáshoz hívja meg az eredményt, és nyomtassa ki a konzolra.

3. Ha a válasz tartalmaz `code` mezőt, nyomtassa ki a hibakódot és a hibaüzenetet.

4. Ellenkező esetben keresse meg a rendellenességek pozícióit az adatkészletben. A válasz `isAnomaly` mezője egy logikai értéket tartalmaz, amely arra vonatkozik, hogy egy adott adatpont rendellenesség-e. Ismételje meg a listát, és nyomtassa ki bármelyik `True` érték indexét. Ezek az értékek a rendellenes adatpontok indexének felelnek meg, ha vannak ilyenek.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(
            result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print(x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legutóbbi adatpont anomália állapotának észlelése

1. Hozzon létre egy `detect_latest()` nevű metódust annak megállapításához, hogy az idősorozat legújabb adatpontja anomália-e. Hívja meg `send_request()` a fenti metódust a végpont, az URL-cím, az előfizetési kulcs és a JSON-adatai alapján. 

2. A `json.dumps()` formázáshoz hívja meg az eredményt, és nyomtassa ki a konzolra.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Töltse be az idősorozat adatait, és küldje el a kérést

1. Töltse be a JSON idősoros adatait egy fájlkezelő megnyitásával, és `json.load()` használja rajta. Ezután hívja meg a fent létrehozott anomáliák észlelési módszereit.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Példaválasz

A sikeres válaszokat JSON formátumban adja vissza a rendszer. Az alábbi hivatkozásokra kattintva megtekintheti a JSON-választ a GitHubon:
* [Példa a Batch észlelési válaszára](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Példa a legutóbbi pont észlelési válaszára](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
