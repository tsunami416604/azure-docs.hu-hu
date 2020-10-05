---
title: Anomália detektor Python ügyféloldali kódtár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: aahi
ms.openlocfilehash: ec13ee65595ccb0bdca1656522a7cd5f21b49a10
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91319254"
---
Ismerkedjen meg a Pythonhoz készült rendellenesség-Kiderítő ügyféloldali kódtár használatába. Az alábbi lépéseket követve telepítheti a csomagot a szolgáltatás által biztosított algoritmusok használatával. Az anomália-detektor szolgáltatás lehetővé teszi, hogy az idősoros adataiban az adatsorozatok adatait automatikusan a legjobb illeszkedő modellekkel találja, függetlenül az iparágtól, a forgatókönyvtől vagy az adatmennyiségtől.

A következőhöz használhatja a a Pythonhoz készült rendellenesség-Kiderítő ügyféloldali kódtárat:

* Az idősorozat-adatkészletben lévő rendellenességek észlelése batch-kérelemként
* Az idősorozat legújabb adatpontjának anomália állapotának észlelése
* Az adatkészletben lévő trendek változási pontjainak észlelése.

[Könyvtár-referenciák dokumentációja](https://go.microsoft.com/fwlink/?linkid=2090370)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector)  |  [Csomag (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/)  |  [Mintakód keresése a githubon](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.x](https://www.python.org/)
* A [Panda adatelemzési könyvtára](https://pandas.pydata.org/)
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" hozzon létre egy rendellenesség-Kiderítő erőforrást, "  target="_blank"> és hozzon létre egy anomália-detektor erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazásnak a rendellenesség-érzékelő API-hoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.


## <a name="setting-up"></a>Beállítás

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

 Hozzon létre egy új Python-fájlt, és importálja a következő könyvtárakat.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Hozzon létre változókat a kulcshoz környezeti változóként, egy idősorozat-adatfájl elérési útját, valamint az előfizetés Azure-beli helyét. Például: `westus2`.

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után az ügyféloldali kódtár a következővel telepíthető:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>Objektummodell

A rendellenesség-Kiderítő ügyfél egy [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél a teljes adathalmazt a [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-), vagy a [Last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-)használatával a legújabb adatpontokon is elvégezheti. A [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090370) függvény észleli azokat a pontokat, amelyek egy trend változásait jelzik.

Az idősorozat-információk küldése egy [kérelem](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python) -objektumban lévő [pontok](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python) sorozata. Az `Request` objektum olyan tulajdonságokat tartalmaz, amelyek leírják az adatok (például a[részletesség](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) ) és az anomáliák észlelésének paramétereit.

Az anomália-detektor válasza egy [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python), [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python)vagy [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370) objektum a használt metódustól függően.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Pythonhoz készült rendellenesség-Kiderítő ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Idősorozat-adatkészlet betöltése fájlból](#load-time-series-data-from-a-file)
* [A teljes adathalmazban észlelt rendellenességek észlelése](#detect-anomalies-in-the-entire-data-set)
* [A legutóbbi adatpont anomália állapotának észlelése](#detect-the-anomaly-status-of-the-latest-data-point)
* [Az adatkészletben lévő változási pontok észlelése](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Adja hozzá az Azure Location változót a végponthoz, és hitelesítse az ügyfelet a kulccsal.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Adatsorozat-adatok betöltése fájlból

A rövid útmutatóhoz tartozó példa adatainak letöltése a [githubról](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. A böngészőben kattintson a jobb gombbal a **RAW**elemre.
2. Kattintson **a hivatkozás mentése másként**elemre.
3. Mentse a fájlt egy. csv-fájlként az alkalmazás könyvtárába.

Ez az idősoros adat. csv-fájlként van formázva, és a rendszer elküldi a rendellenesség-Kiderítő API-nak.

Töltse be az adatfájlt a Panda Library `read_csv()` metódusával, és készítsen egy üres lista változót az adatsorozatok tárolásához. Ismételje meg a fájlt, és az [adatpont](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python) objektumként fűzze hozzá az adatelemet. Ez az objektum a. csv-adatfájl soraiban található timestamp és numerikus értéket fogja tartalmazni.

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Hozzon létre egy [kérés](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python) objektumot az idősorozattal, valamint az adatpontok [részletességét](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (vagy gyakoriságát). Például: `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>A teljes adathalmazban észlelt rendellenességek észlelése

Hívja meg az API-t, hogy észlelje a rendellenességeket a teljes idősoros adatain keresztül az ügyfél [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) metódusának használatával. Tárolja a visszaadott [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objektumot. Ismételje meg a válasz `is_anomaly` listáját, és nyomtassa ki bármelyik érték indexét `true` . Ezek az értékek a rendellenes adatpontok indexének felelnek meg, ha vannak ilyenek.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legutóbbi adatpont anomália állapotának észlelése

A rendellenesség-Kiderítő API meghívásával megállapíthatja, hogy a legutóbbi adatpontja anomália-e az ügyfél [last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) metódusának használatával, és tárolja a visszaadott [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) objektumot. A válasz `is_anomaly` értéke egy olyan logikai érték, amely megadja az adott pont anomáliának állapotát.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="detect-change-points-in-the-data-set"></a>Az adatkészletben lévő változási pontok észlelése

Hívja meg az API-t az idősorozat-információkban található változási pontok észleléséhez az ügyfél [detect_change_point ()](https://go.microsoft.com/fwlink/?linkid=2090370) metódusának használatával. Tárolja a visszaadott [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370) objektumot. Ismételje meg a válasz `is_change_point` listáját, és nyomtassa ki bármelyik érték indexét `true` . Ezek az értékek a trend változási pontjainak indexeit tükrözik, ha vannak ilyenek.

[!code-python[detect change points](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=changePointDetection)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `python` paranccsal és a fájl nevével.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
