---
title: 'Rövid útmutató: Az anomáliadetektor REST API-jával és a Java-val észleli az idősorozat-adatok anomáliáinak anomáliáit'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja az Anomália-detektor API-t az adatsorok rendellenességei észlelésére kötegként vagy streamelési adatokon.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 4e7cc969ff2c36415b8fd1cebafa1873f9efd38c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239055"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Rövid útmutató: Az anomáliadetektor REST API-jával és a Java-val észleli az idősorozat-adatok anomáliáinak anomáliáit

Ezzel a rövid útmutatóval elkezdheti használni az Anomália-detektor API két észlelési módját az idősorozat-adatok anomáliáinak észleléséhez. Ez a Java alkalmazás két API-kérelmet küld, amelyek JSON-formátumú idősorozat-adatokat tartalmaznak, és lekéri a válaszokat.

| API-kérelem                                        | Alkalmazás kimenete                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Anomáliák észlelése kötegként                        | A JSON-válasz, amely az idősorozat-adatok minden egyes adatpontjának anomáliaállapotát (és egyéb adatait) és az észlelt anomáliák helyzetét tartalmazza. |
| A legújabb adatpont anomáliaállapotának észlelése | Az idősorozat-adatok legfrissebb adatpontjának anomáliaállapotát (és egyéb adatait) tartalmazó JSON-válasz.                                                                                                                                         |

 Bár ez az alkalmazás Java nyelven íródott, az API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A rövid útmutató forráskódját a [GitHubon](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java)találja.

## <a name="prerequisites"></a>Előfeltételek

- A [&trade; Java Development Kit(JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) vagy újabb.
- Anomáliadetektor kulcsa és végpontja
- Ezeket a könyvtárakat importálja a Maven-tárházból
    - [JSON Java csomagban](https://mvnrepository.com/artifact/org.json/json)
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) csomag

- Idősorozat-adatpontokat tartalmazó JSON-fájl. A rövid útmutató példaadatai a [GitHubon](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)találhatók.

### <a name="create-an-anomaly-detector-resource"></a>Anomáliadetektor-erőforrás létrehozása

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. Hozzon létre egy új Java-projektet, és importálja a következő tárakat.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Hozzon létre változókat az előfizetési kulcshoz és a végponthoz. Az alábbiakban az anomáliadetektáláshoz használható URI-k láthatók. Ezeket később hozzáfűzi a szolgáltatás végpontjának az API-kérelem URL-címének létrehozásához.

    |Észlelési módszer  |URI  |
    |---------|---------|
    |Kötegfelismerés    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Észlelés a legújabb adatponton     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Kérelemküldési funkció létrehozása

1. Hozzon létre `sendRequest()` egy új függvényt, amely a fent létrehozott változókat veszi figyelembe. Ezután hajtsa végre a következő lépéseket.

2. Hozzon `CloseableHttpClient` létre egy objektumot, amely kéréseket küldhet az API-nak. Küldje el a `HttpPost` kérelmet egy kérelemobjektumnak a végpont és az anomáliadetektor URL-címének kombinálásával.

3. A kérelem `setHeader()` függvényében állítsa `Content-Type` be `application/json`a fejlécet a , `Ocp-Apim-Subscription-Key` és adja hozzá az előfizetési kulcsot a fejléchez.

4. Használja a kérelem `setEntity()` funkcióját az elküldendő adatokhoz.

5. Az ügyfél függvényének `execute()` használatával küldje el a kérelmet, és mentse egy `CloseableHttpResponse` objektumba.

6. Hozzon `HttpEntity` létre egy objektumot a választartalom tárolásához. A tartalom `getEntity()`beszerezése a segítségével. Ha a válasz nem üres, adja vissza.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Anomáliák észlelése kötegként

1. Hozzon létre `detectAnomaliesBatch()` egy metódust, amelynek célja az adatok anomáliák kötegként való észleléséhez. Hívja `sendRequest()` meg a fenti metódust a végpont, url, előfizetési kulcs és json adatokat. Szerezd meg az eredményt, és nyomtassa ki a konzolra.

2. Ha a `code` válasz mezőt tartalmaz, nyomtassa ki a hibakódot és a hibaüzenetet.

3. Ellenkező esetben keresse meg az anomáliák helyét az adatkészletben. A válasz `isAnomaly` mezője logikai értéket tartalmaz, amely arra vonatkozik, hogy egy adott adatpont anomália-e. A JSON-tömb beírása és itatása, `true` bármely érték indexének nyomtatása. Ezek az értékek megfelelnek a rendellenes adatpontok indexének, ha találhatók ilyenek.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legújabb adatpont anomáliaállapotának észlelése

Hozzon létre `detectAnomaliesLatest()` egy metódust, amely az adatkészlet utolsó adatpontjának anomáliaállapotának észlelésére szolgál. Hívja `sendRequest()` meg a fenti metódust a végpont, url, előfizetési kulcs és json adatokat. Szerezd meg az eredményt, és nyomtassa ki a konzolra.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Az idősorozat-adatok betöltése és a kérelem elküldése

1. Az alkalmazás fő metódusában olvassa el a JSON-fájlt, amely tartalmazza a kérelmekhez hozzáadott adatokat.

2. Hívja meg a fent létrehozott két anomáliadetektálási függvényt.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Példaválasz

A sikeres választ JSON formátumban adja vissza. Kattintson az alábbi linkekre a JSON-válasz megtekintéséhez a GitHubon:
* [Példa kötegészlelési válaszra](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Példa a legutóbbi pontészlelési válaszra](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
