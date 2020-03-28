---
title: 'Rövid útmutató: Az anomáliadetektor REST API-jával és a C-vel észleli az idősorozat-adatok anomáliáit #'
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
ms.openlocfilehash: b53fecad3655048a7b9d799134926b2730b16dae
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239114"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Rövid útmutató: Az anomáliadetektor REST API-jával és a C-vel észleli az idősorozat-adatok anomáliáit # 

Ezzel a rövid útmutatóval elkezdheti használni az Anomália-detektor API két észlelési módját az idősorozat-adatok anomáliáinak észleléséhez. Ez a C# alkalmazás két API-kérelmet küld, amelyek JSON-formátumú idősorozat-adatokat tartalmaznak, és lekéri a válaszokat.

| API-kérelem                                        | Alkalmazás kimenete                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Anomáliák észlelése kötegként                        | A JSON-válasz, amely az idősorozat-adatok minden egyes adatpontjának anomáliaállapotát (és egyéb adatait) és az észlelt anomáliák helyzetét tartalmazza. |
| A legújabb adatpont anomáliaállapotának észlelése | Az idősorozat-adatok legfrissebb adatpontjának anomáliaállapotát (és egyéb adatait) tartalmazó JSON-válasz.                                        |

 Bár ez az alkalmazás C#-ban íródott, az API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A rövid útmutató forráskódját a [GitHubon](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs)találja.

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2017-es vagy újabb verzióinak](https://visualstudio.microsoft.com/downloads/)bármely kiadása ,
- Anomáliadetektor kulcsa és végpontja
- A [Json.NET](https://www.newtonsoft.com/json) keretrendszer, amely NuGet-csomagként letölthető. A Newtonsoft.Json telepítése NuGet csomagként a Visual Studio-ban:
    
    1. Kattintson a jobb gombbal a projektre a **Megoldáskezelőben.**
    2. Válassza **a NuGet-csomagok kezelése lehetőséget.**
    3. Keresse meg *a Newtonsoft.Json-t,* és telepítse a csomagot.

- Ha Linux/MacOS-t használ, ez az alkalmazás a [Mono](https://www.mono-project.com/)használatával futtatható.

- Idősorozat-adatpontokat tartalmazó JSON-fájl. A rövid útmutató példaadatai a [GitHubon](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)találhatók.

### <a name="create-an-anomaly-detector-resource"></a>Anomáliadetektor-erőforrás létrehozása

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. A Visual Studio-ban hozzon létre egy új konzolmegoldást, és adja hozzá a következő csomagokat. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Hozzon létre változókat az előfizetési kulcshoz és a végponthoz. Az alábbiakban az anomáliadetektáláshoz használható URI-k láthatók. Ezeket később hozzáfűzi a szolgáltatás végpontjának az API-kérelem URL-címének létrehozásához.

    | Észlelési módszer                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Kötegfelismerés                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Észlelés a legújabb adatponton | `/anomalydetector/v1.0/timeseries/last/detect`   |
        
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Kérelemküldési funkció létrehozása

1. Hozzon létre egy `Request` új aszinkron függvényt, amely a fent létrehozott változókat veszi figyelembe.

2. Állítsa be az ügyfél biztonsági protokollját `HttpClient` és fejlécadatait egy objektum használatával. Ügyeljen arra, hogy az `Ocp-Apim-Subscription-Key` előfizetési kulcsot hozzáadja a fejléchez. Ezután `StringContent` hozzon létre egy objektumot a kérelemhez.

3. Küldje el `PostAsync()`a kérést a segítségével, majd küldje vissza a választ.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Anomáliák észlelése kötegként

1. Hozzon létre `detectAnomaliesBatch()`egy új függvényt, amelynek neve . A kérelmet úgy hozhatja `Request()` létre, hogy meghívja a függvényt a végpontjával, az előfizetési kulccsal, a kötegelt anomáliaészlelés URL-címével és az idősorozat-adatokkal.

2. Deszerializálja a JSON-objektumot, és írja azt a konzolra.

3. Ha a `code` válasz mezőt tartalmaz, nyomtassa ki a hibakódot és a hibaüzenetet. 

4. Ellenkező esetben keresse meg az anomáliák helyét az adatkészletben. A válasz `isAnomaly` mezője logikai értékek tömbjét tartalmazza, amelyek mindegyike azt jelzi, hogy egy adatpont anomália-e. Alakítsa át ezt a válaszobjektum `ToObject<bool[]>()` függvényével rendelkező karakterlánctömbté. Végighaladhat a tömbön, és kinyomtathatja az értékek `true` indexét. Ezek az értékek megfelelnek a rendellenes adatpontok indexének, ha találhatók ilyenek.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legújabb adatpont anomáliaállapotának észlelése

1. Hozzon létre `detectAnomaliesLatest()`egy új függvényt, amelynek neve . A kérelmet úgy hozhatja `Request()` létre, hogy meghívja a függvényt a végpontjával, az előfizetési kulccsal, a legújabb pontanomád-észlelési URL-címével és az idősorozat-adatokkal.

2. Deszerializálja a JSON-objektumot, és írja azt a konzolra.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]
 
## <a name="load-your-time-series-data-and-send-the-request"></a>Az idősorozat-adatok betöltése és a kérelem elküldése

1. Az alkalmazás fő módszerében töltse be a JSON idősorozat-adatait a alkalmazással. `File.ReadAllText()` 

2. Hívja meg a fent létrehozott anomáliadetektálási függvényeket. Az `System.Console.ReadKey()` alkalmazás futtatása után tartsa nyitva a konzolablakot.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Példaválasz

A sikeres választ JSON formátumban adja vissza. Kattintson az alábbi linkekre a JSON-válasz megtekintéséhez a GitHubon:
* [Példa kötegészlelési válaszra](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Példa a legutóbbi pontészlelési válaszra](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
