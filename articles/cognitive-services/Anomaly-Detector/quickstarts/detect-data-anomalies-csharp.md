---
title: 'Gyors útmutató: anomáliák észlelése az idősoros adataiban az anomália-detektor REST API és C használatával #'
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
ms.openlocfilehash: b53fecad3655048a7b9d799134926b2730b16dae
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80239114"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Gyors útmutató: anomáliák észlelése az idősoros adataiban az anomália-detektor REST API és C használatával # 

Ezzel a rövid útmutatóval megkezdheti a anomáliák-Kiderítő API két észlelési módjának használatát az idősorozat-adataiban észlelt rendellenességek észlelésére. Ez a C#-alkalmazás két, JSON-formátumú idősorozat-adatokat tartalmazó API-kérelmet küld, és lekéri a válaszokat.

| API-kérelem                                        | Alkalmazás kimenete                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Rendellenességek észlelése kötegként                        | Az idősorozat-adatpontokhoz tartozó anomália-állapotot (és az egyéb adatmennyiségeket) tartalmazó JSON-válasz, valamint az észlelt rendellenességek helyei. |
| A legutóbbi adatpont anomália állapotának észlelése | Az idősorozat-adatként a legutóbbi adatponthoz tartozó anomália-állapotot (és egyéb adatértékeket) tartalmazó JSON-válasz.                                        |

 Habár ez az alkalmazás C# nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A jelen rövid útmutató forráskódját a [githubon](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs)találja.

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2017-es vagy újabb](https://visualstudio.microsoft.com/downloads/)verziójának bármely kiadása,
- Anomália-detektor kulcsa és végpontja
- A [Json.NET](https://www.newtonsoft.com/json) keretrendszer, amely NuGet-csomagként letölthető. A Newtonsoft. JSON telepítése NuGet-csomagként a Visual Studióban:
    
    1. Kattintson a jobb gombbal a projektre **megoldáskezelő**.
    2. Válassza a **NuGet-csomagok kezelése**lehetőséget.
    3. Keresse meg a *Newtonsoft. JSON* fájlt, és telepítse a csomagot.

- Ha Linux/MacOS rendszert használ, akkor az alkalmazás a [mono](https://www.mono-project.com/)használatával futtatható.

- Idősorozat-adatpontokat tartalmazó JSON-fájl. A rövid útmutatóhoz tartozó példa a [githubon](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)érhető el.

### <a name="create-an-anomaly-detector-resource"></a>Anomália-detektor erőforrásának létrehozása

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. A Visual Studióban hozzon létre egy új konzolos megoldást, és adja hozzá a következő csomagokat. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Hozzon létre változókat az előfizetési kulcshoz és a végponthoz. Az alábbi URI-k használhatók a anomáliák észleléséhez. Az API-kérelmek URL-címeinek létrehozásához ezeket a rendszer később hozzáfűzi a szolgáltatási végponthoz.

    | Észlelési módszer                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Kötegelt észlelés                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Észlelés a legújabb adatponton | `/anomalydetector/v1.0/timeseries/last/detect`   |
        
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Függvény létrehozása a kérelmek küldéséhez

1. Hozzon létre egy nevű `Request` új aszinkron függvényt, amely a fent létrehozott változókat veszi fel.

2. Állítsa be az ügyfél biztonsági protokollját és fejléc-információit egy `HttpClient` objektum használatával. Ügyeljen arra, hogy hozzáadja az előfizetési kulcsot `Ocp-Apim-Subscription-Key` a fejléchez. Ezután hozzon `StringContent` létre egy objektumot a kérelemhez.

3. Küldje el a kérelmet `PostAsync()`a szolgáltatással, majd adja vissza a választ.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Rendellenességek észlelése kötegként

1. Hozzon létre egy nevű `detectAnomaliesBatch()`új függvényt. Hozza létre a kérést, és küldje el `Request()` úgy, hogy meghívja a függvényt a végponttal, az előfizetési kulccsal, a köteg anomália észlelésének URL-címével és az idősorozat adataival.

2. Deszerializálja a JSON-objektumot, és írja a konzolba.

3. Ha a válasz tartalmaz `code` mezőt, nyomtassa ki a hibakódot és a hibaüzenetet. 

4. Ellenkező esetben keresse meg a rendellenességek pozícióit az adatkészletben. A válasz `isAnomaly` mezője logikai értékeket tartalmaz, amelyek mindegyike azt jelzi, hogy az adatpont egy anomália-e. Alakítsa át ezt a karakterlánc-tömbre a Response objektum `ToObject<bool[]>()` függvényével. Ismételje meg a tömböt, és nyomtassa ki bármelyik `true` érték indexét. Ezek az értékek a rendellenes adatpontok indexének felelnek meg, ha vannak ilyenek.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legutóbbi adatpont anomália állapotának észlelése

1. Hozzon létre egy nevű `detectAnomaliesLatest()`új függvényt. Hozza létre a kérést, és küldje el `Request()` úgy, hogy meghívja a függvényt a végponttal, az előfizetési kulccsal, a legutóbbi pont anomália-észlelésének URL-címével és az idősorozat adataival

2. Deszerializálja a JSON-objektumot, és írja a konzolba.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]
 
## <a name="load-your-time-series-data-and-send-the-request"></a>Töltse be az idősorozat adatait, és küldje el a kérést

1. Az alkalmazás fő metódusában töltse be a JSON idősorozat-adatait az- `File.ReadAllText()`val. 

2. Hívja meg a fent létrehozott anomália-észlelési funkciókat. Ezzel `System.Console.ReadKey()` a paranccsal megtarthatja a konzolablak megnyitását az alkalmazás futtatása után.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Példaválasz

A sikeres válaszokat JSON formátumban adja vissza a rendszer. Az alábbi hivatkozásokra kattintva megtekintheti a JSON-választ a GitHubon:
* [Példa a Batch észlelési válaszára](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Példa a legutóbbi pont észlelési válaszára](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
