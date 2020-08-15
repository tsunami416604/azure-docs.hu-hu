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
ms.date: 06/30/2020
ms.author: aahi
ms.openlocfilehash: 86742568d8f0c7c951d872e7df23b8ce1cb0920f
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2020
ms.locfileid: "88244227"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Gyors útmutató: anomáliák észlelése az idősoros adataiban az anomália-detektor REST API és C használatával #

Ezzel a rövid útmutatóval megkezdheti a anomáliák-Kiderítő API két észlelési módjának használatát az idősorozat-adataiban észlelt rendellenességek észlelésére. Ez a C#-alkalmazás két, JSON-formátumú idősorozat-adatokat tartalmazó API-kérelmet küld, és lekéri a válaszokat.

| API-kérelem                                        | Alkalmazás kimenete                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Rendellenességek észlelése kötegként                        | Az idősorozat-adatpontokhoz tartozó anomália-állapotot (és az egyéb adatmennyiségeket) tartalmazó JSON-válasz, valamint az észlelt rendellenességek helyei. |
| A legutóbbi adatpont anomália állapotának észlelése | Az idősorozat-adatként a legutóbbi adatponthoz tartozó anomália-állapotot (és egyéb adatértékeket) tartalmazó JSON-válasz.                                        |

 Habár ez az alkalmazás C# nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A jelen rövid útmutató forráskódját a [githubon](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs)találja.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
- Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" hozzon létre egy rendellenesség-Kiderítő erőforrást, "  target="_blank"> és hozzon létre egy anomália-detektor erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    - Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazásnak a rendellenesség-érzékelő API-hoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
- A [Visual Studio 2017-es vagy újabb](https://visualstudio.microsoft.com/downloads/) verziójának bármely kiadása
- A [Json.NET](https://www.newtonsoft.com/json) keretrendszer, amely NuGet-csomagként letölthető. Newtonsoft.Jstelepítése NuGet-csomagként a Visual Studióban:

    1. Kattintson a jobb gombbal a projektre **megoldáskezelő**.
    2. Válassza a **NuGet-csomagok kezelése**lehetőséget.
    3. Keressen rá a *Newtonsoft.Jsre* , és telepítse a csomagot.

- Ha Linux/MacOS rendszert használ, akkor az alkalmazás a [mono](https://www.mono-project.com/)használatával futtatható.

- Idősorozat-adatpontokat tartalmazó JSON-fájl. A rövid útmutatóhoz tartozó példa a [githubon](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)érhető el.

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

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

1. Hozzon létre egy nevű új aszinkron függvényt `Request` , amely a fent létrehozott változókat veszi fel.

2. Állítsa be az ügyfél biztonsági protokollját és fejléc-információit egy `HttpClient` objektum használatával. Ügyeljen arra, hogy hozzáadja az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez. Ezután hozzon létre egy `StringContent` objektumot a kérelemhez.

3. Küldje el a kérelmet a szolgáltatással `PostAsync()` , majd adja vissza a választ.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Rendellenességek észlelése kötegként

1. Hozzon létre egy nevű új függvényt `detectAnomaliesBatch()` . Hozza létre a kérést, és küldje el úgy, hogy meghívja a `Request()` függvényt a végponttal, az előfizetési kulccsal, a köteg anomália észlelésének URL-címével és az idősorozat adataival.

2. Deszerializálja a JSON-objektumot, és írja a konzolba.

3. Ha a válasz tartalmaz `code` mezőt, nyomtassa ki a hibakódot és a hibaüzenetet.

4. Ellenkező esetben keresse meg a rendellenességek pozícióit az adatkészletben. A válasz `isAnomaly` mezője logikai értékeket tartalmaz, amelyek mindegyike azt jelzi, hogy az adatpont egy anomália-e. Alakítsa át ezt a karakterlánc-tömbre a Response objektum `ToObject<bool[]>()` függvényével. Ismételje meg a tömböt, és nyomtassa ki bármelyik `true` érték indexét. Ezek az értékek a rendellenes adatpontok indexének felelnek meg, ha vannak ilyenek.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legutóbbi adatpont anomália állapotának észlelése

1. Hozzon létre egy nevű új függvényt `detectAnomaliesLatest()` . Hozza létre a kérést, és küldje el úgy, hogy meghívja a `Request()` függvényt a végponttal, az előfizetési kulccsal, a legutóbbi pont anomália-észlelésének URL-címével és az idősorozat adataival

2. Deszerializálja a JSON-objektumot, és írja a konzolba.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Töltse be az idősorozat adatait, és küldje el a kérést

1. Az alkalmazás fő metódusában töltse be a JSON idősorozat-adatait az-val `File.ReadAllText()` .

2. Hívja meg a fent létrehozott anomália-észlelési funkciókat. Ezzel a paranccsal `System.Console.ReadKey()` megtarthatja a konzolablak megnyitását az alkalmazás futtatása után.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Példaválasz

A sikeres válaszokat JSON formátumban adja vissza a rendszer. Az alábbi hivatkozásokra kattintva megtekintheti a JSON-választ a GitHubon:
* [Példa a Batch észlelési válaszára](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Példa a legutóbbi pont észlelési válaszára](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
