---
title: 'Gyors útmutató: Adatanomáliák észlelése a következőhöz: az anomália-detektor ügyféloldali kódtára a Pythonhoz'
titleSuffix: Azure Cognitive Services
description: A rendellenesség-Kiderítő API használatával az adatsorozatban lévő rendellenességeket kötegként vagy adatfolyamként lehet érzékelni.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 08/01/2019
ms.author: aahi
ms.openlocfilehash: 59a4d79cc68c57faf54bde3d42370fb17a317325
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725563"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Gyors útmutató: A Pythonhoz készült adatrendellenesség-detektor ügyféloldali kódtára

Ismerkedés az anomália-detektor .NET-hez készült ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. Az anomália-detektor szolgáltatás lehetővé teszi, hogy az idősoros adataiban az adatsorozatok adatait automatikusan a legjobb illeszkedő modellekkel találja, függetlenül az iparágtól, a forgatókönyvtől vagy az adatmennyiségtől.

A következőhöz használhatja a a Pythonhoz készült rendellenesség-Kiderítő ügyféloldali kódtárat:

* Az idősorozat-adatkészlet összes rendellenességének észlelése batch-kérelemként
* Az idősorozat legújabb adatpontjának anomália állapotának észlelése

[Könyvtár](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [-dokumentációs függvénytár](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [-csomag (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [mintái](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* A [Panda adatelemzési könyvtára](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Beállítás

### <a name="create-an-anomaly-detector-resource"></a>Anomália-detektor erőforrásának létrehozása

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

Miután beolvasott egy kulcsot a próbaverziós előfizetésből vagy erőforrásból, [hozzon létre egy környezeti változót](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a (z) nevű `ANOMALY_DETECTOR_KEY`kulcshoz.

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

 Hozzon létre egy új Python-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. Ezután importálja a következő kódtárakat.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Hozzon létre változókat a kulcshoz környezeti változóként, egy idősorozat-adatfájl elérési útját, valamint az előfizetés Azure-beli helyét. Például: `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után az ügyféloldali kódtár a következővel telepíthető:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Objektummodell

A rendellenesség-Kiderítő ügyfél egy [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél két módszert biztosít a anomáliák észlelésére: Egy teljes adatkészlet [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)és a legújabb adatpontok használatával a [Last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-)segítségével. 

Az idősorozat-információk küldése egy [kérelem](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) -objektumban lévő [pontok](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) sorozata. Az `Request` objektum olyan tulajdonságokat tartalmaz, amelyek leírják az adatok (például a[részletesség](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) ) és az anomáliák észlelésének paramétereit. 

Az anomália-detektor válasza egy [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) vagy [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objektum a használt módszertől függően. 

## <a name="code-examples"></a>Példák a kódokra 

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a következőhöz: az anomália-detektor .NET-hez készült ügyféloldali kódtára:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Idősorozat-adatkészlet betöltése fájlból](#load-time-series-data-from-a-file)
* [A teljes adathalmazban észlelt rendellenességek észlelése](#detect-anomalies-in-the-entire-data-set) 
* [A legutóbbi adatpont anomália állapotának észlelése](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Adja hozzá az Azure Location változót a végponthoz, és hitelesítse az ügyfelet a kulccsal.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Adatsorozat-adatok betöltése fájlból

A rövid útmutatóhoz tartozó példa adatainak letöltése [](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)a githubról:
1. A böngészőben kattintson a jobb gombbal a **RAW**elemre.
2. Kattintson **a hivatkozás mentése másként**elemre.
3. Mentse a fájlt egy. csv-fájlként az alkalmazás könyvtárába.

Ez az idősoros adat. csv-fájlként van formázva, és a rendszer elküldi a rendellenesség-Kiderítő API-nak.

Töltse be az adatfájlt a Panda Library `read_csv()` metódusával, és készítsen egy üres lista változót az adatsorozatok tárolásához. Ismételje meg a fájlt, és az adatpont objektumként [](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) fűzze hozzá az adatelemet. Ez az objektum a. csv-adatfájl soraiban található timestamp és numerikus értéket fogja tartalmazni. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Hozzon létre egy [kérés](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) objektumot az idősorozattal, valamint az adatpontok [részletességét](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (vagy gyakoriságát). Például: `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>A teljes adathalmazban észlelt rendellenességek észlelése 

Hívja meg az API-t, hogy észlelje a rendellenességeket a teljes idősoros adatain keresztül az ügyfél [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) metódusának használatával. Tárolja a visszaadott [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objektumot. Ismételje meg `is_anomaly` a válasz listáját, és nyomtassa ki bármelyik `true` érték indexét. Ezek az értékek a rendellenes adatpontok indexének felelnek meg, ha vannak ilyenek.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legutóbbi adatpont anomália állapotának észlelése

A rendellenesség-Kiderítő API meghívásával megállapíthatja, hogy a legutóbbi adatpontja anomália-e az ügyfél [last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) metódusának használatával, és tárolja a visszaadott [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) objektumot. A válasz `is_anomaly` értéke egy olyan logikai érték, amely megadja az adott pont anomáliának állapotát.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az ide-ban, vagy a parancssorban `python` a paranccsal és a fájl nevével.
 
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Adatfolyam-rendellenességek észlelése Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* Mi a rendellenesség-Kiderítő [API?](../overview.md)
* [Ajánlott eljárások](../concepts/anomaly-detection-best-practices.md) az anomália-detektor API használatakor.
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)található.
