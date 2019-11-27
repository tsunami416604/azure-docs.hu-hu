---
title: 'Gyors útmutató: adatanomáliák észlelése az anomáliák Kiderítő ügyféloldali kódtár használatával a Pythonhoz'
titleSuffix: Azure Cognitive Services
description: A rendellenesség-Kiderítő API használatával az adatsorozatban lévő rendellenességeket kötegként vagy adatfolyamként lehet érzékelni.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: f58f4f9c89e2c0bfb7d9eca6d39dab3f25b21990
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483349"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Gyors útmutató: anomália-Kiderítő ügyféloldali kódtár a Pythonhoz

Ismerkedjen meg a Pythonhoz készült rendellenesség-Kiderítő ügyféloldali kódtár használatába. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. Az anomália-detektor szolgáltatás lehetővé teszi, hogy az idősoros adataiban az adatsorozatok adatait automatikusan a legjobb illeszkedő modellekkel találja, függetlenül az iparágtól, a forgatókönyvtől vagy az adatmennyiségtől.

A következőhöz használhatja a a Pythonhoz készült rendellenesség-Kiderítő ügyféloldali kódtárat:

* Az idősorozat-adatkészlet összes rendellenességének észlelése batch-kérelemként
* Az idősorozat legújabb adatpontjának anomália állapotának észlelése

[Könyvtár-referenciák dokumentációja](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [csomag (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [a githubon található mintakód keresése](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Anomália-detektor kulcsa és végpontja
* [Python 3. x](https://www.python.org/)
* A [Panda adatelemzési könyvtára](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Beállítás

### <a name="create-an-anomaly-detector-resource"></a>Anomália-detektor erőforrásának létrehozása

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

 Hozzon létre egy új Python-fájlt, és importálja a következő könyvtárakat.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Hozzon létre változókat a kulcshoz környezeti változóként, egy idősorozat-adatfájl elérési útját, valamint az előfizetés Azure-beli helyét. Például: `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után az ügyféloldali kódtár a következővel telepíthető:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Objektummodell

A rendellenesség-Kiderítő ügyfél egy [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél két módszert biztosít a anomáliák észlelésére: a [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)és a legújabb adatpontok [Last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-)használatával történő teljes adatkészleten. 

Az idősorozat-információk küldése egy [kérelem](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) -objektumban lévő [pontok](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) sorozata. A `Request` objektum olyan tulajdonságokat tartalmaz, amelyek leírják az adatokat (például a[részletességet](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) ), valamint az anomáliák észlelésének paramétereit. 

Az anomália-detektor válasza egy [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) vagy [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objektum a használt módszertől függően. 

## <a name="code-examples"></a>Példák a kódokra 

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Pythonhoz készült rendellenesség-Kiderítő ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Idősorozat-adatkészlet betöltése fájlból](#load-time-series-data-from-a-file)
* [A teljes adathalmazban észlelt rendellenességek észlelése](#detect-anomalies-in-the-entire-data-set) 
* [A legutóbbi adatpont anomália állapotának észlelése](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Adja hozzá az Azure Location változót a végponthoz, és hitelesítse az ügyfelet a kulccsal.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Adatsorozat-adatok betöltése fájlból

A rövid útmutatóhoz tartozó példa adatainak letöltése a [githubról](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. A böngészőben kattintson a jobb gombbal a **RAW**elemre.
2. Kattintson **a hivatkozás mentése másként**elemre.
3. Mentse a fájlt egy. csv-fájlként az alkalmazás könyvtárába.

Ez az idősoros adat. csv-fájlként van formázva, és a rendszer elküldi a rendellenesség-Kiderítő API-nak.

Töltse be az adatfájlt a Panda Library `read_csv()` metódusával, és hozzon egy üres lista változót az adatsorozat tárolására. Ismételje meg a fájlt, és az [adatpont](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) objektumként fűzze hozzá az adatelemet. Ez az objektum a. csv-adatfájl soraiban található timestamp és numerikus értéket fogja tartalmazni. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Hozzon létre egy [kérés](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) objektumot az idősorozattal, valamint az adatpontok [részletességét](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (vagy gyakoriságát). Például: `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>A teljes adathalmazban észlelt rendellenességek észlelése 

Hívja meg az API-t, hogy észlelje a rendellenességeket a teljes idősoros adatain keresztül az ügyfél [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) metódusának használatával. Tárolja a visszaadott [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objektumot. Ismételje meg a válasz `is_anomaly` listáját, és nyomtassa ki a `true` értékek indexét. Ezek az értékek a rendellenes adatpontok indexének felelnek meg, ha vannak ilyenek.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legutóbbi adatpont anomália állapotának észlelése

A rendellenesség-Kiderítő API meghívásával megállapíthatja, hogy a legutóbbi adatpontja anomália-e az ügyfél [last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) metódusának használatával, és tárolja a visszaadott [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) objektumot. A válasz `is_anomaly` értéke egy olyan logikai érték, amely megadja az adott pont anomáliának állapotát.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `python` paranccsal és a fájl nevével.
 
[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
