---
title: 'Gyors útmutató: Rendellenességek észlelése, a batch az Anomáliadetektálási detector használatával REST API-t és a Python használatával |} A Microsoft Docs'
description: Az Anomáliadetektálási detector használatával API segítségével észlelheti a rendellenességeket az adatsorozat egy kötegelt vagy a streamelt adatokon.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 6b4ddcadfe63f74d115c155354a276e45c6b53f9
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544500"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Gyors útmutató: Rendellenességek észlelése az idősoros adatokat az Anomáliadetektálási detector használatával REST API-t és a Python használatával

Ez a rövid útmutató segítségével indítsa el a rendellenességek észlelése az idősoros adatokat az Anomáliadetektálási detector használatával API két észlelési mód használatával. A Python-alkalmazás két API-kérések tartalmazó JSON-formátumú idősoros adatokat küld, és lekérdezi a válaszokat.

| API-kérelem                                        | Alkalmazás kimenete                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Kötegelt feladatként rendellenességek észlelése                        | Az idősorozat-adatokat, és minden észlelt rendellenességek igazítja adatpontok anomáliadetektálási állapota (és más adatok) tartalmazó JSON-választ. |
| A legújabb adatpont anomáliadetektálási állapotának észlelése | Az anomáliadetektálási állapota (és egyéb adatokat) az idősorozat-adatok a legutóbbi adatok pontját tartalmazó JSON-választ.                                                                                                                                         |

 Bár ez az alkalmazás pythonban írt, az API-t az szinte bármelyik programozási nyelvével kompatibilis webes RESTful szolgáltatás.

## <a name="prerequisites"></a>Előfeltételek

- [Python 2.x vagy 3.x](https://www.python.org/downloads/)

- A [kérelmek könyvtár](http://docs.python-requests.org) Python

- A JSON fájlt tartalmazó idősorozat-adatok mutat. A példaadatokat ebben a rövid útmutatóban található [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. A kedvenc szövegszerkesztőjével vagy az IDE hozzon létre egy új python-fájlt. Adja hozzá az alábbi importálásokat.

    ```python
    import requests
    import json
    ```

2. Változók létrehozása az előfizetési kulcs és a végpontot. Az alábbiakban az URI-k rendellenességek észlelése is használhat. Ezek hozzá lesznek fűzve a később, hozhat létre az API-Szolgáltatásvégpont URL-címeket.

    |Észlelési módszer  |URI  |
    |---------|---------|
    |Batch-észlelés    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |A legújabb adatok ponton észlelése     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Olvassa el a JSON-adatok fájlt megnyitni, és használatával `json.load()`.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Kérelmek küldése függvény létrehozása

1. Hozzon létre egy új függvényt nevű `send_request()` átkerül a fent létrehozott változókat. Hajtsa végre az alábbi lépéseket.

2. Hozzon létre egy szótár, a kérelem fejlécében. Állítsa be a `Content-Type` való `application/json`, és adja hozzá az előfizetési kulcs, a `Ocp-Apim-Subscription-Key` fejléc.

3. A kérelmet az küldése `requests.post()`. Kombinálhatja a végpont és a rendellenességek észlelése URL-CÍMÉT a kérelem teljes URL-címéhez, és közé tartoznak a fejlécek és json-kérelem adatai. És választ, majd adja meg.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Kötegelt feladatként rendellenességek észlelése

1. Hozzon létre egy meghívott metódus `detect_batch()` során az adatokat egy kötegelt rendellenességek észlelése. Hívja a `send_request()` metódus a végpont URL-címe, előfizetési kulcs és json-adatok a fent létrehozott.

2. Hívás `json.dumps()` formázza azt, és nyomtassa ki a konzol eredményétől.

3. Ha a válasz tartalmaz `code` mezőben nyomtassa ki a hibakódot és a hibaüzenet jelenik meg.

4. Ellenkező esetben találja az adatkészlet a pozíciók a rendellenességek észlelését. A válasz `isAnomaly` mező arra, hogy egy adott adatpontot anomáliát vonatkozó logikai értéket tartalmaz. Iterál végig a listát, és nyomtassa ki az index minden `True` értékeket. Ha találhatók esetleges rendellenes adatpontok index ezeket az értékeket felelnek meg.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print (x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legújabb adatpont anomáliadetektálási állapotának észlelése

1. Hozzon létre egy meghívott metódus `detect_latest()` annak megállapításához, hogy a legújabb adatok pontot az idősorozat-anomáliadetektálási. Hívja a `send_request()` a végpont URL-címe, előfizetési kulcs és json-adatokat a fenti metódus. 

2. Hívás `json.dumps()` formázza azt, és nyomtassa ki a konzol eredményétől.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Az idősorozat-adatok betöltése, és a kérés küldése

1. A JSON idősorozat-adatok a fájlleíró megnyitásával, és használatával betöltése `json.load()` rajta. Észlelési módszerek fent létrehozott majd hívja meg az anomáliadetektáló.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Példaválasz

A sikeres válasz JSON formátumban. A JSON-válasz megtekintése a Githubon, az alábbi hivatkozásokra kattintva:
* [A batch észlelési példaválasz](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Legújabb pont észlelési példaválasz](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)