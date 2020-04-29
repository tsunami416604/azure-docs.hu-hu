---
title: 'Gyors útmutató: anomáliák észlelése az idősoros adataiban az anomália-detektor REST API és a Java használatával'
titleSuffix: Azure Cognitive Services
description: Ebből a témakörből megtudhatja, hogyan kezelheti az adatsorozatok rendellenességeit kötegként vagy adatfolyamként tárolt adatsorokban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 4e7cc969ff2c36415b8fd1cebafa1873f9efd38c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80239055"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Gyors útmutató: anomáliák észlelése az idősoros adataiban az anomália-detektor REST API és a Java használatával

Ezzel a rövid útmutatóval megkezdheti a anomáliák-Kiderítő API két észlelési módjának használatát az idősorozat-adataiban észlelt rendellenességek észlelésére. Ez a Java-alkalmazás két, JSON-formátumú idősorozat-adatokat tartalmazó API-kérelmet küld, és lekéri a válaszokat.

| API-kérelem                                        | Alkalmazás kimenete                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Rendellenességek észlelése kötegként                        | Az idősorozat-adatpontokhoz tartozó anomália-állapotot (és az egyéb adatmennyiségeket) tartalmazó JSON-válasz, valamint az észlelt rendellenességek helyei. |
| A legutóbbi adatpont anomália állapotának észlelése | Az idősorozat-adatként a legutóbbi adatponthoz tartozó anomália-állapotot (és egyéb adatértékeket) tartalmazó JSON-válasz.                                                                                                                                         |

 Habár ez az alkalmazás Java nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A jelen rövid útmutató forráskódját a [githubon](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java)találja.

## <a name="prerequisites"></a>Előfeltételek

- A [Java&trade; Development Kit (JDK) 7-es](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) vagy újabb verziója.
- Anomália-detektor kulcsa és végpontja
- A kódtárak importálása a Maven adattárból
    - [JSON a Java-](https://mvnrepository.com/artifact/org.json/json) csomagban
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) -csomag

- Idősorozat-adatpontokat tartalmazó JSON-fájl. A rövid útmutatóhoz tartozó példa a [githubon](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)érhető el.

### <a name="create-an-anomaly-detector-resource"></a>Anomália-detektor erőforrásának létrehozása

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. Hozzon létre egy új Java-projektet, és importálja a következő könyvtárakat.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Hozzon létre változókat az előfizetési kulcshoz és a végponthoz. Az alábbi URI-k használhatók a anomáliák észleléséhez. Az API-kérelmek URL-címeinek létrehozásához ezeket a rendszer később hozzáfűzi a szolgáltatási végponthoz.

    |Észlelési módszer  |URI  |
    |---------|---------|
    |Kötegelt észlelés    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Észlelés a legújabb adatponton     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Függvény létrehozása a kérelmek küldéséhez

1. Hozzon létre egy nevű `sendRequest()` új függvényt, amely a fent létrehozott változókat veszi igénybe. Ezután hajtsa végre a következő lépéseket.

2. Hozzon `CloseableHttpClient` létre egy objektumot, amely küldhet kérelmeket az API-nak. Küldje el a kérést `HttpPost` egy kérelem objektumnak a végpont és egy anomália-detektor URL-címének kombinálásával.

3. A kérelem `setHeader()` funkciójának használatával állítsa be a `Content-Type` fejlécet `application/json`, és adja hozzá az előfizetési kulcsot `Ocp-Apim-Subscription-Key` a fejléchez.

4. Használja a kérelem `setEntity()` függvényét az elküldeni kívánt adathoz.

5. A kérelem elküldéséhez használja az ügyfél `execute()` függvényét, és mentse egy `CloseableHttpResponse` objektumba.

6. Hozzon `HttpEntity` létre egy objektumot a válasz tartalmának tárolására. Szerezze be a tartalmat `getEntity()`a-val. Ha a válasz nem üres, küldje vissza.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Rendellenességek észlelése kötegként

1. Hozzon létre egy `detectAnomaliesBatch()` metódust, amely egy kötegként észleli a rendellenességeket az összes adategységben. Hívja meg `sendRequest()` a fent létrehozott metódust a végponttal, az URL-lel, az előfizetési kulccsal és a JSON-adataival. Szerezze be az eredményt, és nyomtassa ki a-konzolra.

2. Ha a válasz tartalmaz `code` mezőt, nyomtassa ki a hibakódot és a hibaüzenetet.

3. Ellenkező esetben keresse meg a rendellenességek pozícióit az adatkészletben. A válasz `isAnomaly` mezője egy logikai értéket tartalmaz, amely arra vonatkozik, hogy egy adott adatpont rendellenesség-e. Szerezze be a JSON-tömböt, és ismételje meg az értékeket, és `true` nyomtassa ki bármelyik érték indexét. Ezek az értékek a rendellenes adatpontok indexének felelnek meg, ha vannak ilyenek.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legutóbbi adatpont anomália állapotának észlelése

Hozzon létre egy `detectAnomaliesLatest()` nevű metódust az adathalmaz utolsó adatpontjának anomália állapotának észleléséhez. Hívja meg `sendRequest()` a fent létrehozott metódust a végponttal, az URL-lel, az előfizetési kulccsal és a JSON-adataival. Szerezze be az eredményt, és nyomtassa ki a-konzolra.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Töltse be az idősorozat adatait, és küldje el a kérést

1. Az alkalmazás fő metódusában olvassa el a kérelmekbe felvenni kívánt adatmennyiséget tartalmazó JSON-fájlt.

2. Hívja meg a fent létrehozott két anomália-észlelési függvényt.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Példaválasz

A sikeres válaszokat JSON formátumban adja vissza a rendszer. Az alábbi hivatkozásokra kattintva megtekintheti a JSON-választ a GitHubon:
* [Példa a Batch észlelési válaszára](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Példa a legutóbbi pont észlelési válaszára](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
