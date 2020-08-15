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
ms.date: 06/30/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 38c2b3cdf40f1924a36ffd84d9dc5f9b2f7f319d
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245706"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Gyors útmutató: anomáliák észlelése az idősoros adataiban az anomália-detektor REST API és a Python használatával

Ezzel a rövid útmutatóval megkezdheti a anomáliák-Kiderítő API két észlelési módjának használatát az idősorozat-adataiban észlelt rendellenességek észlelésére. Ez a Python-alkalmazás két, JSON-formátumú idősorozat-adatokat tartalmazó API-kérelmet küld, és lekéri a válaszokat.

| API-kérelem                                        | Alkalmazás kimenete                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Rendellenességek észlelése kötegként                        | Az idősorozat-adatpontokhoz tartozó anomália-állapotot (és az egyéb adatmennyiségeket) tartalmazó JSON-válasz, valamint az észlelt rendellenességek helyei. |
| A legutóbbi adatpont anomália állapotának észlelése | Az idősorozat-adatként a legutóbbi adatponthoz tartozó anomália-állapotot (és egyéb adatértékeket) tartalmazó JSON-válasz.                                                                                                                                         |

 Habár ez az alkalmazás Pythonban íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A jelen rövid útmutató forráskódját a [githubon](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py)találja.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
- Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" hozzon létre egy rendellenesség-Kiderítő erőforrást, "  target="_blank"> és hozzon létre egy anomália-detektor erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    - Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazásnak a rendellenesség-érzékelő API-hoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
- [Python 2. x vagy 3. x](https://www.python.org/downloads/)
- A Pythonhoz készült [kérelmek kódtára](https://pypi.org/project/requests/)

- Idősorozat-adatpontokat tartalmazó JSON-fájl. A rövid útmutatóhoz tartozó példa a [githubon](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)érhető el.

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. Hozzon létre egy új Python-fájlt, és adja hozzá a következő importálásokat.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Hozzon létre változókat az előfizetési kulcshoz és a végponthoz. Az alábbi URI-k használhatók a anomáliák észleléséhez. Az API-kérelmek URL-címeinek létrehozásához ezeket a rendszer később hozzáfűzi a szolgáltatási végponthoz.

    |Észlelési módszer  |URI  |
    |---------|---------|
    |Kötegelt észlelés    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Észlelés a legújabb adatponton     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Olvassa el a JSON-adatfájlt a megnyitásával és a használatával `json.load()` .

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Függvény létrehozása a kérelmek küldéséhez

1. Hozzon létre egy nevű új függvényt `send_request()` , amely a fent létrehozott változókat veszi igénybe. Ezután hajtsa végre a következő lépéseket.

2. Hozzon létre egy szótárt a kérések fejlécéhez. Állítsa be a- `Content-Type` t `application/json` , és adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

3. Küldje el a kérelmet a paranccsal `requests.post()` . Egyesítse a végpont és a anomália észlelési URL-címét a teljes kérelem URL-címéhez, és adja meg a fejléceket és a JSON-kérelmek adatait. Majd adja vissza a választ.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Rendellenességek észlelése kötegként

1. Hozzon létre egy metódust `detect_batch()` , amely egy kötegként észleli a rendellenességeket az összes adategységben. Hívja `send_request()` meg a fent létrehozott metódust a végponttal, az URL-lel, az előfizetési kulccsal és a JSON-adataival.

2. A `json.dumps()` formázáshoz hívja meg az eredményt, és nyomtassa ki a konzolra.

3. Ha a válasz tartalmaz `code` mezőt, nyomtassa ki a hibakódot és a hibaüzenetet.

4. Ellenkező esetben keresse meg a rendellenességek pozícióit az adatkészletben. A válasz `isAnomaly` mezője egy logikai értéket tartalmaz, amely arra vonatkozik, hogy egy adott adatpont rendellenesség-e. Ismételje meg a listát, és nyomtassa ki bármelyik érték indexét `True` . Ezek az értékek a rendellenes adatpontok indexének felelnek meg, ha vannak ilyenek.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legutóbbi adatpont anomália állapotának észlelése

1. Hozzon létre egy nevű metódust `detect_latest()` annak megállapításához, hogy az idősorozat legújabb adatpontja anomália-e. Hívja `send_request()` meg a fenti metódust a végpont, az URL-cím, az előfizetési kulcs és a JSON-adatai alapján.

2. A `json.dumps()` formázáshoz hívja meg az eredményt, és nyomtassa ki a konzolra.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>A kérelem elküldése

Hívja meg a fent létrehozott anomáliák észlelési módszereit.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Példaválasz

A sikeres válaszokat JSON formátumban adja vissza a rendszer. Az alábbi hivatkozásokra kattintva megtekintheti a JSON-választ a GitHubon:
* [Példa a Batch észlelési válaszára](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Példa a legutóbbi pont észlelési válaszára](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
